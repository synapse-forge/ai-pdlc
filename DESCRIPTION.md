# AI-PDLC — AI-Native Product Development Lifecycle

AI-PDLC is a versioned, spec-first workflow for building software and other technology products with AI agents and skills. It applies wherever agents do the work and humans are accountable for the result: software, documentation, data pipelines, content, hardware firmware, research artifacts, agent skills, tool definitions, and agent crews. Specialized agent pods handle the mechanical work (specifying, designing, building, testing, shipping, observing). Humans provide requirements and context, steer the pods, and approve or reject deliverables at each quality gate. A task's risk level determines how much process and human involvement it requires? The workflow exists to keep three failure modes in check: drift between the spec and the product, probabilistic errors, and uncontrolled complexity growth.

## Why it exists

AI coding agents raise delivery speed and lower delivery certainty. Three failure modes dominate, and classic methodologies (Waterfall, Agile, Lean, Spiral) were built for humans doing the work, so they have no controls for them:

- **Drift:** the spec and the product it describes part ways as changes pile up without a versioned anchor.
- **Probabilistic errors:** agents are confident, fluent, and sometimes wrong, so every generated output needs a verification path.
- **Uncontrolled complexity growth:** agents produce faster than humans can review, letting structure and dependencies grow beyond what anyone can audit.

AI-PDLC exists to keep these three in check. Drift is stopped by the versioned spec and revision pinning, probabilistic errors by deterministic checks and human gates, complexity growth by risk-gated autonomy and evidence-based review. It combines proven practices (phase gates, reviews, risk registers, retrospectives) and adds one new thing: the work itself moves to agents under continuous, risk-based verification.

## Key principles

- **Spec is the source of truth.** A testable, version-controlled specification anchors everything downstream. Acceptance criteria are written as executable failing tests, pinned to the spec revision they verify.
- **Versioned everything.** Specs, plans, prompts, skills, evals, data, configs, and the model checkpoint itself are pinned by revision or digest. Risk decisions, gate evidence, and rollbacks reference exact versions, so outcomes are reproducible and reverts are atomic.
- **Risk decides process, item by item.** Every work item is assessed for likelihood × consequence before an agent touches it. The assessment alone sets how much autonomy the agent gets and which gates it must pass.
- **One shared risk register.** Every pod writes to and reads from it: active risks, severity, mitigations, owners, burn-down. It is updated at every gate and feeds the retrospectives.
- **Context is a finite budget.** Pods exchange distilled artifacts, not raw chat history or private memory, so context degradation cannot accumulate across handoffs.
- **End-to-end traceability.** A revision-pinned chain links intent → spec → plan → code → test → deploy, so any artifact can be audited back to its origin.
- **A closed learning loop.** Evals and retrospectives feed back into pods, prompts, skills, and the methodology itself.

## Roles

Roles are hats; one person may wear several, but the author and approver of the same artifact must always be different people.

- **Product Owner (PO):** owns strategy, spec, retrospectives; approves, revises, rejects.
- **Technical Reviewer (TR):** owns design, plan, merge evidence; approves, revises, rejects.
- **Risk Owner (RO):** owns the risk register, class assignment, stop-work; raises or lowers classes, halts work.
- **Release Authority (RA):** owns release and operations; go, hold, rollback, mitigate, escalate.

Delegation is recorded and lands on a named human, never an agent. Security sign-off is RO authority and may be delegated to a named security owner.

## Core mechanism: risk drives process

Before an agent runs, each work item receives a risk class. The class sets the agent's autonomy and the human gates the work must pass. Raising a class is cheap and can be agent-initiated; lowering one always requires a human decision. Classes attach to work items, not to projects or teams, and can change mid-flight. Agent growth is work: a new skill, tool, or crew is itself a work item and earns its class before it can be installed or reused.

| Class | Typical work | Autonomy | Human gates |
| :--- | :--- | :--- | :--- |
| R0 — Cosmetic | copy, formatting, non-critical UI text | Full agent autonomy | Sampled TR audit |
| R1 — Standard | CRUD, tests, internal tooling | Agent works, evidence reviewed | TR at plan and merge; RA at release |
| R2 — Core | schema changes, auth, payments, migrations | Human-in-the-loop planning | PO, TR, RO from spec to merge; RA at release |
| R3 — Critical | secrets, PII, compliance, infra, rollback | Human-led, agent assists | All roles at every stage; security sign-off; RO + RA dual release sign-off |

A human gate opens regardless of class when any of these fire:

- scope, dependency, or blast-radius change (immediate reassessment)
- a first-of-kind model, tool, skill, or pattern (TR at the next boundary)
- a new external dependency (supply-chain review)
- contact with protected data, auth, secrets, or infra (forced R3)
- a production incident on the affected path
- a change to a reviewed revision, which invalidates the prior decision

**Escalation.** Every escalation lands on RO (stop-work authority) or RA (operations): asynchronous for R0, immediate for R2 and above, always recorded in the risk register. Drift and non-determinism tolerances are set per project in the risk register; exceeding a tolerance is an escalation, not a silent pass.

## Verification

Deterministic checks (schema and contract validation, compile, lint, tests, non-determinism re-runs, budget checks) run automatically after every LLM-generated step. They are preconditions, not gates.

Human gates sit only at judgment boundaries: strategy, spec, design, plan, merge, release, operations. Reviewers judge an evidence bundle (diffs, test results, traces, pinned digests), not raw code. Every decision lands in the versioned decision log; changing any pinned revision invalidates the decision and reopens the gate. A missing required role fails the gate closed and escalates it. There is no auto-approve.

## Pods

Pods are reusable, composable agent teams behind a versioned interface: accepted operations, typed inputs and outputs, declared side effects, completion evidence. A pod consults another with a Consultation Request Pack (CRP), which bounds the requested outcome and scope. Merge review uses a Merge-Readiness Pack (MRP): the exact change set plus its review evidence. Pods exchange distilled artifacts instead of raw context; the Knowledge pod enforces context budgets.

| Pod | Owns | Gate |
| :--- | :--- | :--- |
| Product Strategy | vision, goals, product intent | PO |
| Requirements / Spec | the spec; acceptance criteria written as executable failing tests | PO, class confirmed with RO |
| Architecture | decomposition, interfaces | TR: constitution, blast radius |
| UI/UX Design (optional) | UX flows, design system, mockups, component specs; heuristic and accessibility review evidence | TR: design evidence |
| Security & Compliance | security review of designs and compliance-relevant diffs | RO or delegated security owner |
| Planning | risk-classed tasks, sequenced for early risk burn-down | TR |
| Implementation (backend, frontend, data, mobile) | code, strictly against the plan | deterministic checks only; humans enter at merge |
| Testing / QA | unit, property, contract, AC-as-test suites | deterministic only |
| Orchestration | schedules pods, enforces gate chains, assembles MRPs | TR: evidence integrity |
| DevOps / Release | progressive delivery, rehearsed rollbacks, fitness functions | RA |
| Operations / SRE | observes drift and incidents, operates mitigations | RA |
| Analysis | retrospectives, evals, methodology change proposals | PO |
| Data / Fine-tuning | training and eval datasets, model fine-tuning, provenance | RO |
| Skills / Agents | new skills, tool definitions, agent crews; install requires eval evidence | TR: capability, blast radius |
| Knowledge / Context | distilled artifacts, context budgets | deterministic budget checks only |

Any pod may consult any other; the order above is a typical route. The invariant is the gate chain, not the order. The pod that produces an artifact does not verify its own output. Optional pods exist only where the product has that surface; a product without a UI skips the UI/UX pod and its gates.

Interfaces are machine-checkable, and conformance is tested at the boundary. Handoffs (specs, plans, CRPs, MRPs, evidence, gate decisions) have canonical types and schema versions; artifacts name their producer and pin inputs to revisions or digests, large content is referenced rather than embedded, and private memory and chat history are never implicit inputs. Payloads defined by AI-PDLC use JSON Schema, with producers and consumers validating independently and contract checks in CI; invalid input fails explicitly or returns for clarification. Transport, tools, events, tracing, and provenance reuse existing protocols (MCP, A2A, OpenAPI, AsyncAPI, CloudEvents, OpenTelemetry, in-toto, SLSA). Contract changes are versioned and compatibility-tested; schema validity proves nothing about correctness or approval, and no handoff can silently change scope, authority, acceptance criteria, or risk-based gates.

*Constitution: the project's fixed design laws (for example, simplicity, no premature abstraction). Fitness functions: automated checks that a release is healthy (for example, performance and cost thresholds).*

## Usage example

A task to fix a label in the billing UI starts at R0. The agent edits the copy and, in the same pull request, touches a schema used by the payment service. The scope check at merge detects the new blast radius and reclassifies the item as R2: the merge gate reopens with TR and RO review, and the change ships only with their recorded sign-off. The reclassification lands in the risk register, and the incident feeds the Analysis pod's retrospective. The same holds when a pod writes a skill for later reuse: the skill ships as a work item — class, eval evidence, gate — not as a silent habit.

## Built on existing practice

Risk-driven process (Spiral), risk registers, and likelihood × consequence assessment; NIST AI RMF; ISO 31000 and ISO 26262 scaling; GitHub Spec Kit's spec-first workflow; Tikal's Twelve-Factor Agentic SDLC; OpenAI's Practices for Governing Agentic AI Systems. Interfaces: JSON Schema, OpenAPI, AsyncAPI, CloudEvents. Context and orchestration: MCP, A2A, OpenAI Agents SDK, Semantic Kernel, CrewAI. Design: UI/UX Pro Max skill. Observability and provenance: OpenTelemetry, W3C Trace Context, in-toto, SLSA. Verification: pytest, Hypothesis, Pact, Ruff, ESLint, SonarQube, CodeQL, axe (accessibility). Delivery and evaluation: GitHub Actions / GitLab CI, canary releases, feature flags, promptfoo. AI-PDLC defines only what these do not cover: domain payloads, risk classes, and gate behavior.

## Comparison with existing approaches

| Dimension | Classic methodologies (Agile, Waterfall) | Early hybrid AI (Copilot, Cursor tab) | Task-level AI workflows (GitHub Spec Kit and similar) | AI-PDLC |
| :--- | :--- | :--- | :--- | :--- |
| **Execution** | Humans write all code, tests, and docs | Humans write; AI drafts line-level suggestions | Agents complete well-scoped tasks (issue → PR) end to end | Agents execute; humans steer, verify, and arbitrate gates |
| **Planning & estimation** | Effort-based: function points, COCOMO, story points, velocity | Same estimates; AI speeds coding, not planning | Issue decomposition into tasks; token cost per task becomes a budget line | Risk (likelihood × consequence) as planning currency; sequenced for early risk burn-down |
| **Risk management** | Phase gates, review boards, risk registers; only Spiral sequences by risk | Ad hoc; AI output accepted on trust, no risk model | Task-level sandboxing; trust from benchmark scores, not continuous risk assessment | Continuous risk register; per-work-item classes with asymmetric reclassification |
| **Verification & gates** | Human QA and code review; V-Model pairs each phase with a test level | Same gates, but AI output volume outpaces review capacity | CI plus benchmark pass rate; tests are the reward signal and can be gamed | Deterministic checks + evidence-based human review; acceptance criteria run as tests |
| **Source of truth & drift** | Spec documents (Waterfall) or code + tests (Agile); drift fought by discipline and tribal knowledge | Code; docs and tests lag behind AI-assisted output | Code + issue tracker; drift caught by CI failures, not prevented | Versioned spec; every artifact pinned by revision; drift detected at gates |
| **Scaling** | Add people or split teams; Brooks's law and Conway's law shape the limits | Per-developer speed-up only; review capacity becomes the bottleneck | Parallel agents on isolated tasks; coordination and context limits reappear | Compose pods behind typed interfaces; context exchanged as distilled artifacts |
| **Human role & governance** | PM, standups, retrospectives, human review; humans make every decision | Same process; humans additionally police AI suggestions | Human approves each task and its result; agent works unsupervised within it | Humans author the spec, steer pods, arbitrate gates on evidence |
| **Improvement loop** | Retrospectives, lessons learned, kaizen; learning lives in people | Same loop; improvement still depends on humans noticing patterns | Benchmark scores steer model choice; no per-product learning loop | Evals and retrospectives feed back into pods, prompts, and the methodology |

## Non-goals

AI-PDLC does not pick your LLM (model choice is per-task and evidence-backed), does not replace human product judgment, and does not let agents put self-written skills into service without a class and a gate. Today it is a methodology description, not an implementation.
