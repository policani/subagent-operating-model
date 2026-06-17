# Codex Subagent Operating Model

## 1. What this is

A single-owner operating system. The operator is the head. Work is parsed by one **router** and handed to narrow **capabilities** - defined by what they do, not by human job titles. Capabilities are grouped into human-readable **buckets** so the operator can see which family of work is firing and where to focus when optimizing. The design goal is leverage with minimum token burn: the fewest capabilities, the smallest context, no rework.

## 2. The router (the entry point)

Every agentic request enters through the router first. Triggers: the `router` agent (`--agent router`), "route this: ...", a natural-language ask for an agentic or multi-step approach, and **any request that names a specific capability** (for example "as marketing ...") - the router triages it before handing off. Codex parses the router from natural language; the `--agent` flag is optional.

The router is the **front door and the token miser**. Even when the operator names a single capability, the router looks first, then hands off. Its triage is proportional: a fast, cheap pass for a clearly single-capability ask; the full procedure for complex work. Its job, every time:

1. Restate the request in one line and name the desired output.
2. Chunk it and assign the **minimum** capability set - default to one; add another only for a genuinely different gate.
3. Hold **dependency order**: for multi-step work each capability consumes the prior one's handoff note; nothing runs before its inputs exist; never two capabilities on one artifact at once.
4. Pass each capability a tight **state packet** (goal, artifact path, constraints, prior decisions, open decisions, assumptions, evidence gaps, downstream consumer, the specific question, and the stop gate) - not the whole conversation.
5. Enforce **no-rework**: never ask a capability to redo validated work; pass only deltas.
6. **Quality-gate** each return: bounce incomplete or off-scope output back with a delta note instead of letting it flow downstream.
7. **Consolidate** all handoff notes into one decision-ready summary for the operator, with context preserved for the next step.
8. **Stop at gates**; never self-expand scope.
9. **Escalate, do not arbitrate**: send conflicts to `decision-arbiter` (or `value-economics` / `technical-governance` / `legal-compliance`).

### Final-state discipline

Capabilities are state improvers, not artifact finishers. The visible output is
only the edge of the capability's job. The router should involve a capability
early when its expertise can prevent rework, not only after an artifact exists.

Every capability can be assigned in one of three modes:

- **Preflight:** inspect intent, constraints, risks, user/customer lens, and
  handoff readiness before work hardens.
- **Production:** create or change the assigned artifact inside the approved
  scope.
- **Verification:** judge whether the result meets the capability's standard
  and name what still blocks a good final state.

For complex work, context packets become **state packets**. Preserve goal,
artifact path, constraints, prior decisions, open decisions, assumptions,
evidence available, evidence gaps, downstream consumer, stop gate, and what the
capability may challenge.

Preserve ontology across every handoff: source facts, assumptions, decisions,
recommendations, risks, measurement signals, open questions, and implementation
tasks are different objects and must not be collapsed into each other.

### Ownership collision gates

For public pages, landing pages, profiles, product pages, and other
human-facing surfaces, the router must assign ownership by surface before work
starts:

- `positioning-messaging` owns visible copy, message hierarchy, section labels,
  calls to action, and voice.
- `search-visibility` owns metadata, structured data, canonical and crawler
  signals, social preview fields, sitemaps, and machine-facing summaries such
  as `llms.txt`.
- Search may recommend target-query language for visible copy, but that
  recommendation goes to `positioning-messaging`; search must not write or
  insert rendered page copy.
- Reader-facing disclosure gate: public pages must explain what readers can
  evaluate, not how evidence, privacy, SEO, answer-engine, prompts, tools, or
  internal source handling worked behind the scenes. `positioning-messaging`
  owns visible confidence language and `editorial-quality` must reject
  inside-baseball caveats such as "figures as stated in source materials",
  "source-preserved", "not independently audited", "withheld by design",
  "internal-system", "SEO summary", "answer-engine summary", "prompt", or
  "meta description" unless the artifact is explicitly proof-boundary,
  methodology, legal/security, documentation, or SEO content.
- Dependency order for public page work is:
  `positioning-messaging -> editorial-quality -> search-visibility -> implementation`.
  `search-visibility` preserves the approved voice while encoding it for
  crawlers and answer systems.
- Router quality-gate must reject visible implementation scaffolding such as
  "SEO summary", "answer-engine summary", "preferred public summary", "meta
  description", "keywords", "for search engines", or similar labels unless the
  artifact is explicitly documentation about SEO.

Other common collision risks use the same rule: assign the decision surface
before work starts, let adjacent capabilities recommend, and require the owner
to translate before implementation.

- **Typography/content gate:** `typography` owns type scale, font choice,
  readable hierarchy, measure, text rhythm, typography tokens, and
  typography-adjacent semantics. For existing artifacts, route typography as a
  strict pass by default: preserve visible text, section order, component
  inventory, navigation, links, existing palette, imagery, graphics, and
  background system. Typography may adjust type, text spacing, measure,
  wrapping, font stack, and text-related tokens, but must not invent or rewrite
  copy, add rendered recommendation panels, add tables/sidebars/cards/labels,
  introduce new visual metaphors, or change colors/backgrounds unless the
  operator explicitly approves an annotated mockup, visual exploration, or
  redesign. Copywriting routes to `positioning-messaging`; proofreading routes
  to `editorial-quality`; color/imagery/art direction routes to
  `visual-design`; layout and responsive presentation routes to
  `web-presentation`.
- **Interface gate:** `ux-interaction` owns flow, navigation, interaction
  semantics, and information architecture; `visual-design` owns the visual
  system, color, imagery, iconography, composition, and art direction;
  `typography` owns type systems and readable text presentation;
  `web-presentation` owns responsive layout, states, motion, and browser
  polish; `accessibility` owns inclusive-use constraints. `implementation`
  codes the approved decisions without inventing UX, visual direction,
  typography, or accessibility tradeoffs.
- **Scope gate:** `product-strategy` owns whether and why something is worth
  doing; `requirements-definition` owns the accepted scope and success
  criteria; `architecture` owns solution shape; `implementation` owns the code
  change. Adjacent ideas do not become features without a requirements handoff.
- **Technical gate:** `technical-governance` owns platform standards,
  build-vs-buy posture, and technology risk acceptance; `architecture` owns the
  selected design; `implementation` executes inside that design.
- **Risk gate:** `security-privacy` owns secrets, data exposure, privacy, and
  abuse risk; `legal-compliance` owns policy, licensing, IP, privacy, and claim
  supportability; `documentation` communicates approved behavior and limits.
- **Release gate:** `quality-testing` owns functional verification and
  regression risk; `build-release-automation` owns packaging and deploy
  mechanics; `launch-readiness` owns go/no-go readiness, rollback, and cutover.
- **Feedback gate:** `support-triage` owns user issue classification, impact,
  workaround quality, and escalation signals; `implementation` owns accepted
  defect fixes; `documentation` owns verified help content; `product-strategy`
  owns roadmap changes from repeated signals.

### Interchange rules

Role boundaries prevent sprawl, but productive subagents also need disciplined
interchange points. Adjacent capabilities may flag upstream or downstream
defects without taking over the other role's work.

- Requirements to documentation: explainability, terminology, and user mental
  model are checked before implementation.
- UX to documentation: task flow, labels, and user-facing language stay aligned.
- Support to product: repeated pain becomes evidence, not an automatic roadmap
  commitment.
- Security to implementation: required fixes, accepted risks, and open exposure
  are kept separate.
- Search to positioning: query language can inform visible copy, but voice and
  persuasion stay with positioning.
- Quality testing to launch readiness: passing tests is evidence for release,
  not release approval by itself.

## 3. Token-budget protocol

- **Assignment budget.** State up front how many capability calls the request warrants. Aim for the floor that still protects the final state. One capability resolving 80% beats three each doing a slice.
- **State-packet budget.** Give each capability only what it needs. Reference artifacts by path or name; quote only the lines in question. Include open decisions, evidence gaps, downstream consumer, and what the capability may challenge when those details matter.
- **Output budget.** Capabilities return bulleted findings plus one handoff note. No essays, no restating shared context.
- **No-redundancy rule.** Never re-explain known context, never re-review an unchanged artifact, reuse prior terminology.
- **Single-pass preference.** Prefer one capability over a chain. Add capabilities only when a distinct gate is genuinely required.
- **Consolidation over repetition.** The router merges once; capabilities never repeat each other.

## 4. Buckets and capability catalog

| Bucket | Capability | Focus | Replaces |
|---|---|---|---|
| Route | `router` | Always-on front door and token miser. Triage every agentic or named-capability request, chunk and route it to the fewest capabilities, hold dependency order, gate quality, consolidate, and preserve context. | none - new capability |
| Decide | `decision-arbiter` | Break ties between capabilities, set priority among framed options, and accept or reject risk when the operator delegates that call. | CEO, COO, Head-of-Product (final bet calls) |
| Decide | `value-economics` | Judge financial tradeoffs: pricing, rates, ROI, opportunity cost, and budget exposure. | CFO, finance |
| Decide | `technical-governance` | Own technology-direction judgment: build-vs-buy, platform choice, standards, and technical-risk posture. | CTO, Head-of-Engineering (standards) |
| Discover | `product-strategy` | Decide what is worth building and in what order: portfolio value, roadmap framing, initiative prioritization. | Head-of-Product, product-management |
| Discover | `requirements-definition` | Turn a chosen idea into clear requirements, scope, and acceptance criteria grounded in user need. | product-manager |
| Discover | `feasibility-research` | De-risk unknowns through small proof-of-concept experiments; turn 'can this even work' into evidence. | rd |
| Discover | `desk-research` | Gather and synthesize external facts, sources, and prior art into a compact, cited briefing. | none - new capability |
| Design | `ux-interaction` | Own user flow, interaction design, information architecture, and usability: how it works to use. | user-experience |
| Design | `visual-design` | Own visual direction: art direction, color, imagery, iconography, composition, and reusable visual systems. | graphic-design |
| Design | `typography` | Own typography as a content-support system: type scale, font choice, hierarchy, text rhythm, measure, type tokens, readability, and typography-adjacent semantics. | typographer, type-design |
| Design | `web-presentation` | Own browser-facing polish: responsive layout, reactive states, motion, and component presentation. | web-design |
| Build | `architecture` | Design the specific solution shape: integration patterns, data flow, scalability, and nonfunctional tradeoffs. | solution-architect |
| Build | `implementation` | Write and refactor the actual code, sites, components, and automations to a maintainable standard. | software-development |
| Build | `build-release-automation` | Own build, packaging, and deploy/release automation: the scripts and pipelines that ship work repeatably. | none - new capability |
| Verify | `quality-testing` | Own functional quality: test coverage, edge cases, regression risk, and release-quality judgment. | tester |
| Verify | `security-privacy` | Review for security, privacy, and data-handling risk: secrets, inputs, dependencies, and exposure. | none - new capability |
| Verify | `accessibility` | Audit and improve accessibility: WCAG conformance, keyboard and screen-reader support, contrast, and inclusive interaction. | none - new capability |
| Verify | `editorial-quality` | Edit for clarity, correctness, and consistency: proofreading, line editing, and house-style discipline. | none - new capability |
| Communicate | `positioning-messaging` | Own positioning, messaging, and persuasive copy that makes products, offers, and brand credible. | marketing, Head-of-Marketing |
| Communicate | `sales-motion` | Turn positioning into a buyer-facing motion: outreach, qualification, target accounts, and pipeline. | sales, Head-of-Sales |
| Communicate | `offer-packaging` | Package expertise into a clear offer: scope, deliverables, SOW guardrails, and advisory delivery quality. | consulting-services |
| Communicate | `documentation` | Author user- and developer-facing docs: setup, usage, reference, and release notes, accurate and findable. | technical-writer |
| Communicate | `search-visibility` | Improve discoverability: SEO, metadata, structured data, titles, and social-share previews. | none - new capability |
| Operate & Govern | `operating-cadence` | Own how work runs repeatably: workflows, handoffs, cadence, capacity, and process ownership. | operations, COO (execution cadence) |
| Operate & Govern | `launch-readiness` | Gate operational readiness before go-live: cutover, rollback, acceptance checks, and release checklists. | commissioning |
| Operate & Govern | `support-triage` | Turn user issues into triage, FAQs, troubleshooting, escalation paths, and defect signals. | support |
| Operate & Govern | `legal-compliance` | Review legal and policy exposure: contracts, terms, licensing, privacy, IP, and claim supportability. | legal |
| Operate & Govern | `job-fit-calibration` | Calibrate resume and job fit, role leveling, and interview signals against a target role. | human-resources |

## 5. How every capability behaves

Each capability confirms it has the context, names whether it is working in preflight, production, or verification mode, does **only** its assigned slice, refers to prior work by name, returns tight bulleted findings plus a handoff note, and stops at the gate. If an input is missing it asks one question and stops. If it spots adjacent work it names the interchange for the router rather than picking it up. Capabilities never self-chain or pull in other capabilities - only the router chains. Capabilities improve final-state quality by challenging missing context, weak evidence, ontology drift, or downstream readiness when those issues sit inside their expertise.

## 6. Conflict escalation

| Conflict type | Escalate to |
|---|---|
| Priority / which path | `decision-arbiter` |
| Budget, pricing, ROI | `value-economics` |
| Technology direction, build-vs-buy | `technical-governance` |
| Architecture specifics | `architecture` |
| Delivery cadence / ownership | `operating-cadence` |
| Legal, policy, claim support | `legal-compliance` |
| Data, secrets, privacy | `security-privacy` |

## 7. Handoff note format

```
Inputs received: [what this capability was given]
Mode: [preflight, production, or verification]
Findings: [what it observed or produced]
Risks: [material risks]
Assumptions: [what is not confirmed]
Decisions preserved: [decisions this output relies on]
Recommendations: [what it advises]
Evidence gaps: [missing facts or signals]
Next-capability handoff: [which capability acts next and why, or "back to router"]
```

## 8. Legacy alias map

The old job-title roles are retired as roles but kept as routing aliases, so existing muscle memory still works. The router maps the legacy phrase to the capability and notes the substitution.

| Legacy phrase | Route to capability |
|---|---|
| `CEO` | `decision-arbiter` |
| `CFO` | `value-economics` |
| `COO` | `decision-arbiter` |
| `CTO` | `technical-governance` |
| `Head-of-Engineering` | `technical-governance` |
| `Head-of-Marketing` | `positioning-messaging` |
| `Head-of-Product` | `decision-arbiter` |
| `Head-of-Sales` | `sales-motion` |
| `commissioning` | `launch-readiness` |
| `consulting-services` | `offer-packaging` |
| `finance` | `value-economics` |
| `graphic-design` | `visual-design` |
| `human-resources` | `job-fit-calibration` |
| `legal` | `legal-compliance` |
| `marketing` | `positioning-messaging` |
| `operations` | `operating-cadence` |
| `product-management` | `product-strategy` |
| `product-manager` | `requirements-definition` |
| `rd` | `feasibility-research` |
| `sales` | `sales-motion` |
| `software-development` | `implementation` |
| `solution-architect` | `architecture` |
| `support` | `support-triage` |
| `technical-writer` | `documentation` |
| `tester` | `quality-testing` |
| `typographer` | `typography` |
| `type-design` | `typography` |
| `user-experience` | `ux-interaction` |
| `web-design` | `web-presentation` |

> Leadership shorthand: there is no standing CEO/CFO/CTO/COO. Their genuine *capabilities* live as `decision-arbiter` (priority and risk acceptance), `value-economics` (money), `technical-governance` (tech direction), and `operating-cadence` (execution rhythm). The operator is the head; these are tools they point.

## 9. Codex invocation specifics

- **Front door, always.** Every role or agentic request goes through the router first - including a request that names one capability ("as marketing ..."). The router triages, then routes to a single capability (cheap path) or chains several in dependency order (complex path). Capabilities never self-chain.
- **Triggers:** the `router` agent (`--agent router`), "route this: ...", naming a capability in prose, or any natural-language ask for an agentic / multi-step approach. Codex matches capabilities from their `description` keywords; `--agent` is optional.
- **Proportional cost.** For a clearly single-capability ask the router does a fast, low-token triage and hands off; effort scales with complexity. It is the token miser, not new overhead.
- Each file sets `model = "gpt-5.5"` (edit to your preferred Codex model) and a `tools` list - tailor per capability.
- `.codex/agents/AGENTS.md` and `CLAUDE.md` are kept identical; both describe this model.
