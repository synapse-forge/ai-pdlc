# AI-PDLC

AI-PDLC (AI-Native Product Development Lifecycle) is a versioned, spec-first workflow for building products with AI agents. Specialized agent pods do the mechanical work — specifying, designing, building, testing, shipping, observing — while humans provide requirements and context the specification, steer the pods, and approve or reject deliverables at each quality gate. How much process and human attention a task gets is decided by one question: how risky is it?

The full methodology is described in [DESCRIPTION.md](DESCRIPTION.md).

## Why it exists

AI coding agents raise delivery speed but lower delivery certainty. Classic methodologies were built for humans doing the work and have no controls for the three failure modes that dominate with agents:

- **Drift** — the spec and the product part ways as changes pile up without a versioned anchor.
- **Probabilistic errors** — agents are confident, fluent, and sometimes wrong; every generated output needs a verification path.
- **Uncontrolled complexity growth** — agents produce faster than humans can review.

AI-PDLC keeps these in check with a versioned spec and revision pinning, deterministic checks plus human gates, and risk-gated autonomy with evidence-based review.

## How it works

Every work item is assigned a risk class before an agent runs. The class sets the agent's autonomy and the human gates the work must pass: cosmetic work (R0) runs fully autonomous with sampled audits, standard work (R1) is reviewed on evidence at plan and merge, core work (R2) — schema changes, auth, payments, migrations — is planned with a human in the loop, and critical work (R3) — secrets, PII, compliance, infra — is human-led with sign-off from every role at every stage. Raising a class is cheap and can be agent-initiated; lowering one always requires a human decision.

Deterministic checks (compile, lint, tests, schema validation) run automatically after every agent step and are preconditions, not gates. Human gates sit only at judgment boundaries — strategy, spec, design, plan, merge, release, operations — where reviewers judge an evidence bundle, not raw code. There is no auto-approve.

See [DESCRIPTION.md](DESCRIPTION.md) for roles, pods, interfaces, a worked example, and a comparison with existing approaches.

## Status

AI-PDLC is currently a methodology description, not an implementation. It does not pick your LLM, does not replace human product judgment, and does not prescribe an agent framework.

## Getting started

Read [DESCRIPTION.md](DESCRIPTION.md). It is the only document and covers the whole methodology: principles, risk classes, verification, roles, pods, and interfaces.

## Installation

TBD — there is no implementation to install.

## Contributing

TBD.

## License

[Apache License 2.0](LICENSE)
