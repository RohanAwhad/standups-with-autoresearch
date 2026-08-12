# Human ↔ Autoresearcher Communication Protocol

A protocol for maintaining a long-lived conversation with a continuously-running autonomous research agent (Claude Code), without breaking the agent's autonomy.

## Goal

Talk to an autoresearcher mid-loop: learn what it has done since you last talked, understand its reasoning, and steer its work — add experiments, remove ones that no longer provide value — while it keeps researching autonomously between your interactions.

## Problem statement / motivation

An autoresearcher is an agent that runs experiments on its own, indefinitely. The natural way to work with it is like a PI talking to a research scientist: the agent is the expert, you are the decision-maker who also wants to learn (what works, what doesn't, how it differs from the baseline, why experiments were designed the way they were).

Naive chat fails for two reasons:

1. **Role collapse** — talking to the agent in its active session makes it forget it is an autoresearcher. The mission lives in a one-time instruction; your message becomes the newest instruction and the agent answers you, then waits. Nothing re-injects the role.

## Solution

Separate the two concerns: the research loop never talks to the human, and the human never talks to the research loop.

- **Main session**: the autoresearcher, running as a background agent (`claude --bg --name autoresearcher`), with its mission in `CLAUDE.md` so the role survives every turn.
- **Fork for chat**: `/fork` the main session into a background copy, attach, and run `/standup` — a skill that locks the fork into conversation mode (no experiments) and produces the "what has been done + why" summary.
- **Brainstorm + decide**: the human steers; the agent proposes; the human is the dictator on add/continue/remove.
- **`/handoff`**: a skill that compiles the decisions into a curated message with an autoresearcher-role reminder suffix, then delivers it back to the main loop — automatically via cross-session messaging (`SendMessage`) when available (Claude Code ≥2.1.224), otherwise as a printed message to paste.

## Components

- `.claude/skills/standup/SKILL.md` — fork → conversation-mode standup session
- `.claude/skills/handoff/SKILL.md` — compile decisions → handoff message → deliver to main loop
- `handoffs/latest.md` — persisted record of the latest handoff (written by `/handoff`)
