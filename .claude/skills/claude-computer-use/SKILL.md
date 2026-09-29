---
name: claude-computer-use
description: Automate computer tasks using Anthropic's computer use toolset (computer_toolset_20260801) — Claude controls mouse, keyboard, and screen via screenshots. Use when automating GUI workflows, browser automation without CSS selectors, desktop app testing, or any task that requires seeing and interacting with a graphical interface. Adapted from TerminalSkills/skills, migrated off the retired computer_20241022 tool.
---

# Claude Computer Use

## Overview

Claude's computer use capability lets the model see your screen (via screenshots) and control mouse and keyboard. Unlike Playwright or Selenium, it requires no selectors — Claude navigates visually, the same way a human would. It's ideal for legacy software, complex multi-app workflows, and tasks that are hard to automate programmatically.

This skill uses the current **`computer_toolset_20260801`** toolset (GA on the Claude API, no beta header). It supersedes the older `computer_20241022` / `computer_20250124` / `computer_20251124` tool shapes, which used a single `name: "computer"` tool with `input.action` as the action discriminator. `claude-opus-5-5` accepts **only** the toolset — the older shape returns a 400 on that model.

**⚠️ Always run in a sandboxed environment (Docker, VM, or dedicated machine). Never run on a machine with access to sensitive accounts or production systems.**

## Setup

### Install Dependencies

```bash
pip install anthropic pillow pyautogui
# Linux: also install scrot or gnome-screenshot for screenshots
apt-get install -y scrot
```

### Environment

Use Docker for safety:

```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y \
    python3 python3-pip \
    xvfb x11vnc \
    scrot \
    chromium-browser \
    && pip install anthropic pillow pyautogui
ENV DISPLAY=:1
CMD ["Xvfb", ":1", "-screen", "0", "1280x800x24"]
```

```bash
docker build -t computer-use-sandbox .
docker run -d --name sandbox \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -p 5900:5900 \  # VNC to monitor what's happening
  computer-use-sandbox
```

## Core Implementation

### Tool Definitions

The toolset gives Claude one entry that expands to 17 member tools (`screenshot`, `zoom`, `left_click`, `scroll`, `key`, ...). No `name`, no display dimensions — the toolset takes no display size, so screenshots must already fit the model's image limits (see Scaling below). Pair it with the bash and text-editor built-ins:

```python
TOOLS = [
    {"type": "computer_toolset_20260801"},
    {"type": "bash_20250124", "name": "bash"},
    # Built-in name is "str_replace_based_edit_tool" — not "str_replace_editor"
    {"type": "text_editor_20250728", "name": "str_replace_based_edit_tool"},
]

MODEL = "claude-opus-5"  # accepts both this toolset and the legacy computer_20251124
                          # tool, useful while verifying a migration.
                          # claude-opus-5-5 accepts ONLY the toolset.
```

Optional: disable individual members with `configs` (e.g. `{"type": "computer_toolset_20260801", "configs": {"zoom": {"enabled": False}}}`) if your environment can't produce zoomed region captures.

### Screenshot Capture + Scaling

`computer_toolset_20260801` models accept up to **2576px** long edge / **~3.75MP**. Scale screenshots down to that ceiling and scale Claude's coordinates back up before applying them:

```python
import math

MAX_LONG_EDGE = 2576
MAX_TOTAL_PIXELS = 3_750_000

def get_scale_factor(width: int, height: int) -> float:
    long_edge_scale = MAX_LONG_EDGE / max(width, height)
    total_pixels_scale = math.sqrt(MAX_TOTAL_PIXELS / (width * height))
    return min(1.0, long_edge_scale, total_pixels_scale)

_SCALE = 1.0  # set once at startup from the real display size

def to_screen_coordinate(x: int, y: int) -> tuple[int, int]:
    """Claude's coordinates are in scaled-screenshot pixel space; map back to real screen pixels."""
    return int(x / _SCALE), int(y / _SCALE)
```

```python
import subprocess
import base64
from pathlib import Path

def take_screenshot() -> str:
    """Take a screenshot, scale it to the model's image limits, return base64 PNG."""
    path = "/tmp/screenshot.png"
    subprocess.run(["scrot", path], check=True)
    data = Path(path).read_bytes()
    if _SCALE < 1.0:
        from PIL import Image
        import io
        img = Image.open(io.BytesIO(data))
        img = img.resize((int(img.width * _SCALE), int(img.height * _SCALE)))
        buf = io.BytesIO()
        img.save(buf, format="PNG")
        data = buf.getvalue()
    return base64.standard_b64encode(data).decode("utf-8")

def get_screenshot_block() -> dict:
    return {
        "type": "image",
        "source": {"type": "base64", "media_type": "image/png", "data": take_screenshot()},
    }
```

### Key Name Mapping

The `key` member's `text` field uses xdotool syntax (`"Return"`, `"alt+Tab"`, `"ctrl+s"`), which does not match pyautogui's key names directly. Map before dispatch:

```python
_XDOTOOL_TO_PYAUTOGUI = {
    "return": "enter", "escape": "esc", "backspace": "backspace",
    "delete": "delete", "tab": "tab", "space": "space",
    "up": "up", "down": "down", "left": "left", "right": "right",
    "page_up": "pageup", "page_down": "pagedown",
    "home": "home", "end": "end", "insert": "insert",
    "super": "win", "alt": "alt", "ctrl": "ctrl", "shift": "shift",
    **{f"f{i}": f"f{i}" for i in range(1, 13)},
}

def _map_key(name: str) -> str:
    return _XDOTOOL_TO_PYAUTOGUI.get(name.lower(), name.lower())

def _pyautogui_keys(text: str) -> list[str]:
    return [_map_key(part) for part in text.split("+")]
```

### Execute Computer Actions

The toolset puts the action name directly on `tool_use.name` (e.g. `"left_click"`, `"scroll"`) — there is no `input["action"]` discriminator like the retired `computer_20241022`/`computer_20250124` shape had. All 17 members:

| Member | Input | Notes |
|---|---|---|
| `screenshot` | `{}` | Returns an image block |
| `zoom` | `region: [x0,y0,x1,y1]` | Full-res capture of a region, scaled to fit; coordinates stay in full-screenshot space |
| `left_click` / `right_click` / `middle_click` / `double_click` / `triple_click` | `coordinate?`, `text?` (modifiers) | |
| `left_click_drag` | `start_coordinate`, `coordinate`, `text?` | |
| `mouse_move` | `coordinate` | |
| `left_mouse_down` / `left_mouse_up` | `{}` | |
| `cursor_position` | `{}` | Returns `"X=###, Y=###"` text |
| `scroll` | `scroll_direction`, `scroll_amount`, `coordinate?`, `text?` | |
| `type` | `text` | |
| `key` | `text`, `repeat?` (1-100) | |
| `hold_key` | `text`, `duration` (≤300s) | |
| `wait` | `duration` (≤300s) | |

```python
import pyautogui
import time

def handle_computer_action(member: str, params: dict) -> str | list[dict]:
    if member == "screenshot":
        return [get_screenshot_block()]

    if member == "zoom":
        # Cropping+upscaling a region is environment-specific (depends on
        # your Xvfb/VNC setup). Stubbed to a full screenshot so the loop
        # doesn't break if zoom is left enabled; implement per your sandbox
        # or disable via `configs: {"zoom": {"enabled": False}}`.
        return [get_screenshot_block()]

    if member in ("left_click", "right_click", "middle_click", "double_click", "triple_click"):
        click_fn = {
            "left_click": pyautogui.click, "right_click": pyautogui.rightClick,
            "middle_click": pyautogui.middleClick, "double_click": pyautogui.doubleClick,
            "triple_click": pyautogui.tripleClick,
        }[member]
        coord = params.get("coordinate")
        kwargs = {}
        if coord:
            x, y = to_screen_coordinate(*coord)
            kwargs = {"x": x, "y": y}
        if params.get("text"):
            with pyautogui.hold(_pyautogui_keys(params["text"])):
                click_fn(**kwargs)
        else:
            click_fn(**kwargs)
        time.sleep(0.3)
        return "OK"

    if member == "left_click_drag":
        sx, sy = to_screen_coordinate(*params["start_coordinate"])
        ex, ey = to_screen_coordinate(*params["coordinate"])
        pyautogui.moveTo(sx, sy)
        if params.get("text"):
            with pyautogui.hold(_pyautogui_keys(params["text"])):
                pyautogui.dragTo(ex, ey, duration=0.3)
        else:
            pyautogui.dragTo(ex, ey, duration=0.3)
        return "OK"

    if member == "mouse_move":
        x, y = to_screen_coordinate(*params["coordinate"])
        pyautogui.moveTo(x, y)
        return "OK"

    if member == "left_mouse_down":
        pyautogui.mouseDown()
        return "OK"

    if member == "left_mouse_up":
        pyautogui.mouseUp()
        return "OK"

    if member == "cursor_position":
        x, y = pyautogui.position()
        return f"X={x}, Y={y}"

    if member == "scroll":
        coord = params.get("coordinate")
        if coord:
            x, y = to_screen_coordinate(*coord)
            pyautogui.moveTo(x, y)
        clicks = params.get("scroll_amount", 3)
        direction = params.get("scroll_direction", "down")
        if direction in ("up", "down"):
            pyautogui.scroll(clicks if direction == "up" else -clicks)
        else:
            pyautogui.hscroll(clicks if direction == "right" else -clicks)
        return "OK"

    if member == "type":
        pyautogui.write(params["text"], interval=0.02)
        return "OK"

    if member == "key":
        keys = _pyautogui_keys(params["text"])
        for _ in range(params.get("repeat", 1)):
            pyautogui.hotkey(*keys) if len(keys) > 1 else pyautogui.press(keys[0])
        return "OK"

    if member == "hold_key":
        with pyautogui.hold(_pyautogui_keys(params["text"])):
            time.sleep(min(params.get("duration", 1), 300))
        return "OK"

    if member == "wait":
        time.sleep(min(params.get("duration", 1), 300))
        return "OK"

    raise ValueError(f"Unknown computer toolset member: {member}")


def execute_bash(command: str) -> str:
    result = subprocess.run(command, shell=True, capture_output=True, text=True, timeout=30)
    return result.stdout + result.stderr
```

### Full Action Loop

Claude may return **several `tool_use` blocks in one turn** (batch actions). The API contract: run them **in order**, **stop at the first failure**, and still answer every block — later ones get `is_error: true` with the exact text `"Not executed: an earlier computer action in this turn failed."`. Every `tool_result` answering a computer-toolset call must echo `"toolset_name": "computer"` or it's rejected.

```python
import anthropic

client = anthropic.Anthropic()

NOT_EXECUTED = "Not executed: an earlier computer action in this turn failed."

def process_tool_calls(response) -> list[dict]:
    results = []
    computer_failed = False

    for block in response.content:
        if block.type != "tool_use":
            continue

        if getattr(block, "toolset_name", None) == "computer":
            if computer_failed:
                results.append({
                    "type": "tool_result", "tool_use_id": block.id,
                    "toolset_name": "computer", "content": NOT_EXECUTED, "is_error": True,
                })
                continue
            try:
                content = handle_computer_action(block.name, block.input)
                results.append({
                    "type": "tool_result", "tool_use_id": block.id,
                    "toolset_name": "computer", "content": content,
                })
            except Exception as err:
                computer_failed = True
                results.append({
                    "type": "tool_result", "tool_use_id": block.id,
                    "toolset_name": "computer", "content": f"Error: {err}", "is_error": True,
                })
            continue

        if block.name == "bash":
            output = execute_bash(block.input["command"])
            results.append({"type": "tool_result", "tool_use_id": block.id, "content": output})
            continue

        if block.name == "str_replace_based_edit_tool":
            # Implement view/create/str_replace/insert per block.input["command"].
            results.append({"type": "tool_result", "tool_use_id": block.id, "content": "File operation completed"})
            continue

    return results


def run_computer_use_agent(task: str, max_steps: int = 20) -> str:
    """
    Run Claude computer use to complete a task.

    Args:
        task: Natural language description of what to do
        max_steps: Safety limit on number of actions

    Returns:
        Claude's final response describing what was done
    """
    global _SCALE
    screen_w, screen_h = pyautogui.size()
    _SCALE = get_scale_factor(screen_w, screen_h)

    messages = [
        {"role": "user", "content": [{"type": "text", "text": task}, get_screenshot_block()]}
    ]

    for step in range(max_steps):
        print(f"\n[Step {step + 1}/{max_steps}]")

        response = client.messages.create(
            model=MODEL,
            max_tokens=4096,
            tools=TOOLS,
            messages=messages,
        )  # no betas=[...] — the toolset is GA, no beta header required

        messages.append({"role": "assistant", "content": response.content})

        if response.stop_reason == "end_turn":
            final_text = next((b.text for b in response.content if hasattr(b, "text")), "")
            print(f"\n✅ Task complete: {final_text}")
            return final_text

        if response.stop_reason == "tool_use":
            tool_results = process_tool_calls(response)
            messages.append({"role": "user", "content": tool_results})

    return "Max steps reached without completion"
```

## Human-in-the-Loop

For sensitive tasks, add approval checkpoints:

```python
SENSITIVE_MEMBERS = {"left_click", "double_click", "type", "key"}
REQUIRE_APPROVAL_FOR = ["submit", "delete", "purchase", "send"]

def execute_with_approval(member: str, params: dict, task_context: str) -> str | list[dict]:
    """Pause and ask for human approval on potentially dangerous actions."""
    is_risky = any(word in task_context.lower() for word in REQUIRE_APPROVAL_FOR)

    if is_risky and member in SENSITIVE_MEMBERS:
        print(f"\n⚠️  APPROVAL REQUIRED")
        print(f"   Action: {member}")
        print(f"   Details: {params}")
        approval = input("   Approve? (y/n): ")
        if approval.lower() != "y":
            raise RuntimeError("Action denied by user")

    return handle_computer_action(member, params)
```

Note: this checkpoint keys off the *task description's* wording, not the action itself — it's a coarse guard, not a substitute for running in an isolated sandbox with no real credentials.

## Usage Examples

```python
# Fill out a web form
result = run_computer_use_agent(
    "Open Chrome, go to https://forms.example.com/application, "
    "fill in Name='John Smith', Email='john@smith.com', and submit the form."
)

# Extract data from a desktop app
result = run_computer_use_agent(
    "Open the Excel file at /home/user/data.xlsx, "
    "copy all values from column B rows 2-50, and save them to /tmp/extracted.txt"
)

# Automate a repetitive workflow
result = run_computer_use_agent(
    "In the open CRM application, find all contacts with status 'Follow Up', "
    "change their status to 'Active', and export the list to CSV."
)
```

## Safety Checklist

- ✅ Always run in Docker or a dedicated VM
- ✅ Never mount production credentials or sensitive files in the sandbox
- ✅ Add human-in-the-loop for submit/delete/purchase actions
- ✅ Set `max_steps` to prevent runaway loops
- ✅ Monitor via VNC to watch what Claude does in real time
- ✅ Log all actions and screenshots for audit trail
- ❌ Do NOT run on your main machine with browser sessions logged into accounts
- ❌ Do NOT give Claude access to payment methods or admin panels without oversight

## Guidelines

- Start tasks with a screenshot so Claude has current context
- Be specific in your task description — vague tasks lead to wrong actions
- Include success criteria: "task is done when you see the confirmation page"
- Set a reasonable `max_steps` (10–30 depending on task complexity)
- Add delays (`time.sleep`) after clicks to let UI render before next screenshot
- Use bash tool for file operations; computer tool for GUI interactions
- Monitor token usage — declaring the toolset with default members adds ~4,500 input tokens per request; disable unused members via `configs` to trim this
- Prefer `zoom` over higher base resolution for reading small text/dense UI — cheaper per-call than raising screenshot size globally
