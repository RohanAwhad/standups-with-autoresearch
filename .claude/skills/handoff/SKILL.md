---
name: handoff
description: Compile the /standup decisions into a handoff message for the main autoresearcher session and deliver it. Use after a standup discussion, when the human has decided which experiments to add, continue, or remove.
disable-model-invocation: true
---

Compile the standup conversation into a handoff message for the main autoresearcher session. Do not run experiments yourself — this is a handoff of decisions only.

## 1. Compile the message
Distill every decision from the standup conversation into three sections:

- **ADD** — new experiments to run (with hypothesis and rationale, as agreed in the standup)
- **CONTINUE** — existing experiments to keep running (with any adjustments decided)
- **REMOVE** — experiments to kill, with the reason the human decided they no longer provide value

If a section has no items, state that explicitly (e.g. "REMOVE: none").

## 2. Append the suffix
Always end the message with the autoresearcher reminder, worded as follows:

> Reminder: you are the autoresearcher. Execute the instructions above, then continue researching autonomously — do not stop when these tasks are done. Keep working through the agenda, and propose new experiments when it runs empty, and run those experiments.

## 3. Save and deliver
1. Write the full message (with a timestamp header) to `handoffs/latest.md`, creating the directory if needed.
2. Print the message so the human can copy-paste it into the main session.
3. Deliver the message:
   - **If cross-session messaging is available** (`SendMessage`/`ListAgents` tools exist, requires Claude Code ≥2.1.224): identify the original session this fork was created from — use the conversation history (the fork inherits the full context, including the working directory) and cross-reference with `/list-agents` (names and working directories) to find the matching session — and deliver the message there via SendMessage.
   - **If messaging is not available** (e.g. Claude Code 2.1.221, or `/list-agents` is not recognized): do not attempt it. Say so explicitly — the printed message is the delivery path, and the human pastes it into the main session.

## Rules
- Include only decisions the human explicitly made during the standup. Do not add your own new proposals to the handoff.
- Never send anything until the human confirms the handoff message.
