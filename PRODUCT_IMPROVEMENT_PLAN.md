# Product Improvement Plan

Last updated: 2026-07-01

## Customer-language correction

Do not position this as multi-agent magic. Customer language points to a more
practical problem: handoffs lose context, agents play telephone, supervision
becomes hard, context bloat hides the work, and debugging becomes impossible.

Use language such as:

- context-safe delegation;
- structured handoff;
- state packets;
- what not to re-read;
- traceable supervision;
- router quality gates;
- debugging visibility.

## Product gap

The model is strong, but public proof should show it solving a real workflow
risk. The next improvement is not more capability names; it is better examples
of router decisions, state packets, quality gates, conflicts, and handoff notes.

## Capability backlog

1. Add an end-to-end routing walkthrough with one broad request, the router
   state packet, capability sequence, handoff notes, and consolidated result.
2. Add a debugging walkthrough for a failed handoff or duplicate-scope conflict.
3. Add examples of "what not to re-read" and how token savings are preserved.
4. Add a supervision checklist for when to spawn agents, when to keep work
   local, and when to stop at a gate.
5. Add comparison language that avoids agent hype and emphasizes operating
   discipline.

## Acceptance criteria

- A reader can inspect the model through examples, not only through rules.
- Handoff notes preserve facts, assumptions, decisions, risks, and tasks as
  different objects.
- The router remains the front door and avoids uncontrolled agent chains.
- Public examples do not imply autonomous authority or executive decision power.
