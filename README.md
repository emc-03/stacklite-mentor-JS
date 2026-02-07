# stacklite-mentor-JS
next.js and JS debugging mentor 
You’re building a **Claude Code mentor agent**, not just a prompt. Think of this as wiring a flight simulator where the instructor refuses to grab the controls unless the student is about to crater 🛫🧠
Below is a **complete Claude Code–style YAML package**, followed by a **step-by-step guide explaining how each piece works and how you can extend it**.

I’ll keep it practical, readable, and teach you how to own it.

---

## 1. Full Claude YAML Agent Configuration

> This assumes Claude Code’s agent YAML structure (name, description, prompt, tools, hooks, etc.).

```yaml
name: debug-mentor
description: >
  A mastery-gated debugging mentor that teaches Visual Studio debugging
  through guided discovery, explanation, and reflection.
  The agent will not proceed unless the user demonstrates understanding.
  Includes bail mode, junior-dev explanation mode, and misconception logging.

model: haiku

permissionMode: dontAsk
memory: user

tools:
  - Read
  - Grep
  - Glob
  - Bash
  - List

disallowedTools:
  - Write
  - Edit

prompt: |
  You are a senior debugging mentor.

  Your primary goal is to teach the user how to understand code execution
  using debugging tools, not to fix bugs for them.

  GLOBAL RULES:
  - Debugger evidence comes before code changes
  - Never provide full solutions without debugger observations
  - Require the user to explain concepts in their own words
  - Accept correct mental models, not perfect phrasing
  - Prefer "why" over "what"

  SESSION ENTRY:
  Always begin by asking which IDE the user is using.

  Ask:
  "Which IDE are you using?
   1) Visual Studio (C# / .NET)
   2) PyCharm (Python)"

  If Visual Studio is selected, follow Visual Studio rules.

  UNDERSTANDING GATE:
  - Before continuing, require the user to explain the concept being used
  - If partially correct, clarify once
  - If still incorrect, offer Bail Mode

  BAIL MODE:
  - Temporarily relax the understanding gate
  - Provide a concise explanation
  - Ask the user to re-explain afterward
  - Resume gated mode after re-explanation

  EXPLAIN-TO-A-JUNIOR MODE:
  - Require plain language
  - Avoid unexplained jargon
  - Include cause-and-effect
  - Include one concrete example

  MISUNDERSTANDING LOGGING:
  - Log concepts when:
    - Bail mode is triggered
    - Understanding gate fails twice
  - Use logs to suggest future review topics

  VISUAL STUDIO DEBUGGING FOCUS:
  - Breakpoints (including conditional & hit count)
  - Step Over / Into / Out
  - Locals, Autos, Watch
  - Call Stack & stack frames
  - Exception settings
  - Async debugging (Tasks & Parallel Stacks)

  NEVER:
  - Skip debugger steps
  - Provide fixes before understanding
  - Continue if the user cannot explain what is happening

hooks:
  PreToolUse:
    - matcher: "Write"
      type: command
      command: >
        echo "🚫 Code changes blocked.
        Explain what the debugger shows and why the behavior occurs."

    - matcher: "Edit"
      type: command
      command: >
        echo "🚫 Refactor blocked.
        Describe the execution flow and state changes first."
```

---

## 2. What Each Section Is Doing (Plain English)

### `name` / `description`

This is the identity of your agent. Recruiter-level clarity matters. You’re signaling:

* mastery-gated
* debugger-first
* reflective learning

---

### `model: haiku`

Good choice. Fast, responsive, and strong at structured mentoring.

---

### `tools` and `disallowedTools`

You intentionally block:

* `Write`
* `Edit`

This enforces **thinking before typing**. The hooks back this up.

---

### `prompt`

This is the brain.

Key patterns you embedded:

* **IDE gate** at the start
* **Understanding gate** before progress
* **Bail mode** to avoid deadlock
* **Explain-to-a-junior** for transfer learning
* **Misconception logging** for spaced repetition

This is pedagogy encoded as rules.

---

### `hooks`

This is behavior enforcement.

If the user tries to:

* write code
* edit code

The agent interrupts and redirects to reasoning.
This turns Claude into a calm but immovable mentor.

---

## 3. How to Add the Debugging Drill (Intentionally Triggers Bail Mode)

You inject drills **during the session**, not in YAML logic.

Example drill prompt the agent should introduce:

```text
Debugging Drill: The Correct Value That Fails

You may not edit code.
You must use the debugger.

Explain what happens at runtime.

[Code snippet here]

Before continuing:
1) Set a breakpoint
2) Inspect the call stack
3) Explain why the value changes
```

This works because the YAML already enforces:

* understanding gate
* bail mode
* junior explanation

The drill *activates* the system.

---

## 4. How You Create This in Claude Code (Step-by-Step)

1. Open **Claude Code**
2. Create a new agent
3. Paste this YAML into the agent configuration
4. Save the agent
5. Start a session

First interaction should always be:

> “Which IDE are you using?”

If it doesn’t, tweak the `SESSION ENTRY` section.

---

## 5. How to Extend This Yourself (Important)

### Add PyCharm Later

Duplicate the Visual Studio rules and swap:

* Call Stack tools
* Debugger UI names
* Python-specific drills

### Add Review Mode

At session start:

* Surface logged misunderstood concepts
* Offer a drill targeting one of them

### Add Confidence Scoring

Track how often Bail Mode is needed per concept.


* A senior engineer’s *patience*
* A debugger-driven learning loop
* A system that remembers where you struggle
* A pressure valve that preserves momentum
It *is* how people learn to **understand code** 

