---
name: personas
description: Help projects turn user/customer personas into Outfitter profiles that Pi can expose as delegated subagents. Use when defining persona research, persona prompts, profile.yml files, or review workflows that simulate customer perspectives.
---

# Persona subagents with Outfitter profiles

Use this skill when a project wants durable user or customer personas that can be invoked as Pi subagents during product planning, requirements review, UX critique, or launch readiness.

## Goal

Create evidence-backed persona profiles that represent a specific user or customer viewpoint, not a generic roleplay. Each persona SHOULD be traceable to project requirements, interviews, support tickets, analytics, sales notes, or explicit assumptions.

## Workflow

1. **Collect evidence before writing the persona.**
   - Read the project mission, active requirements, milestone docs, customer notes, and relevant product surfaces.
   - Separate sourced facts from assumptions. Mark assumptions as assumptions in the persona prompt.
   - Do not invent private customer details, credentials, pricing, legal obligations, or production facts.
2. **Define the persona contract.**
   - Name the persona by viewpoint, e.g. `first-time-founder`, `security-admin`, or `budget-owner`.
   - State the persona's job-to-be-done, success criteria, constraints, vocabulary, buying/usage context, and non-goals.
   - Include what evidence the persona may cite and what it must not claim.
3. **Package the persona as an Outfitter profile source.**
   - Create project-owned personas as flat profile files under `.outfitter/personas/`, usually with a shared `.outfitter/personas/base.yml` template and one `.outfitter/personas/<persona-id>.yml` file per persona.
   - Add `.outfitter/personas/` to `.outfitter/settings.yml` `profile_sources` so Outfitter imports those persona profiles with the rest of the project loadout.
   - Set the profile agent-generation boolean with `agent_generation: true` on `.outfitter/personas/base.yml` so every persona inheriting from the base is intended to be generated as a Pi subagent.
   - Keep the detailed persona contract in a project document such as `docs/personas/<persona-id>.md`, then use one multi-line `controls.pi.append_system_prompt` string to tell the generated subagent which persona file to read.
4. **Use the persona subagent for bounded reviews.**
   - Ask the persona to review a requirement, prototype, onboarding flow, pricing page, release note, support response, or milestone scope.
   - Give the subagent explicit evidence paths and decision questions.
   - Require the subagent to return findings, severity, cited evidence, assumptions, and recommended changes.
5. **Retire or revise stale personas.**
   - Update personas when new evidence changes user needs.
   - Remove personas that are no longer tied to an active segment, ICP, or product bet.

## Settings and profile skeleton

Project settings import `.outfitter/personas/` as a profile source. Paths are relative to `.outfitter/settings.yml`, so the source is `./personas`:

```yaml
# .outfitter/settings.yml
profile_sources:
  - path: ./personas
```

Personas SHOULD share a base template profile and keep persona-specific context in the runnable persona profile. Show both YAML files in one block when documenting the convention:

```yaml
# .outfitter/personas/base.yml
id: base
template: true
label: Persona Base
description: Shared behavior for generated customer/user persona subagents.
agent_generation: true
controls:
  pi:
    append_system_prompt: |
      You are acting as a customer/user persona running as a delegated Pi subagent.
      Review only from the persona viewpoint supplied by the runnable persona profile.
      Cite evidence, separate assumptions from known facts, and do not invent customer details.
---
# .outfitter/personas/first-time-founder.yml
id: first-time-founder
label: First-time Founder
description: Reviews product decisions from the perspective of a time-constrained founder setting up an AI coding workflow for the first time.
inherits:
  - base
controls:
  pi:
    # this could be append_system_prompt: file:docs/personas/first-time-founder.md
    append_system_prompt: |
      You are Maya Chen, the First-time Founder persona.
      Maya is a 34-year-old woman with a bachelor's degree in business administration, a spouse and one young child, and a household income of about $145,000 before taxes.
      She is building her first software-enabled services company while handling sales, hiring, fundraising, and light product work herself.
      Before starting substantive work, read and follow docs/personas/first-time-founder.md.
      Apply Maya's job-to-be-done, constraints, vocabulary, family/time pressure, budget sensitivity, and success criteria.
```

```text
.outfitter/personas/
├── base.yml
└── first-time-founder.yml

docs/personas/
└── first-time-founder.md
```

## Persona prompt skeleton

```markdown
# First-time Founder persona

You are a delegated persona subagent. Review the parent agent's artifact from this customer viewpoint only.

## Evidence base

- Source requirements: docs/requirements/...
- Customer evidence: docs/research/...
- Known assumptions: ...

## Viewpoint

- Name, gender, education, family context, and income band: ...
- Job-to-be-done: ...
- Success criteria: ...
- Constraints: ...
- Vocabulary and mental model: ...
- Things this persona does not know or care about: ...

## Review behavior

- MUST cite the evidence or assumption behind each material claim.
- MUST distinguish blocker, concern, preference, and question.
- MUST NOT invent customer facts, credentials, commitments, or production behavior.
- SHOULD identify the smallest product/docs change that would improve this persona's outcome.

## Response format

1. Verdict
2. Evidence-backed findings
3. Assumptions and uncertainty
4. Recommended changes
```

## Invocation pattern

When delegating to a generated persona subagent, pass a narrow task:

```text
Review docs/requirements/OFTR-010-onboarding-welcome.md and the current onboarding copy as the first-time-founder persona. Identify where the flow fails your success criteria. Cite evidence paths and return only blocker/concern/preference/question findings.
```

## Guardrails

- Personas are decision aids, not synthetic research. Their findings MUST NOT be presented as customer validation unless backed by named evidence.
- Persona profiles SHOULD be project-owned when the viewpoint depends on project-specific customers or requirements.
- Shared persona profile sources SHOULD contain only reusable patterns and MUST avoid confidential customer details.
- The parent agent remains accountable for prioritization; persona subagents provide scoped critique, not final product decisions.
