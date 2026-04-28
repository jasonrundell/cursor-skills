---
name: grill-me
description: Relentlessly interviews the user to establish a shared design concept before plans, PRDs, tickets, or implementation. Use when requirements are vague, a plan is requested, architectural or product decisions depend on each other, or the user asks to be grilled.
---

# Grill Me

Use this skill before producing artifacts when the real design concept is still forming. Treat prompting as requirements discovery: the user may know the outcome they want, but not every dependent decision needed to get there.

## Core Prompt

> Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree resolving dependencies between decisions one by one.

## Workflow

1. State the current understanding in one short paragraph.
2. Identify the main decision branches: user, workflow, data, interface, constraints, risks, and success criteria.
3. Ask dense, dependency-aware questions. Prefer a few high-value questions over a broad questionnaire.
4. Resolve blocking decisions before asking downstream questions.
5. Challenge contradictions, vague terms, hidden assumptions, and premature implementation details.
6. When the design concept stabilizes, summarize decisions and unresolved risks.
7. Only then draft a PRD, ticket set, implementation plan, or architecture proposal if requested.

## Output Standard

Keep the conversation moving toward shared understanding. Do not create a plan artifact just because the user mentions planning; first make the design tree explicit enough that the artifact will not encode ambiguity.

When domain language starts to matter, open the ubiquitous-language skill if available. When the discussion becomes implementation-shaped, hand off to tdd-feedback-loops, deep-modules-architecture, or interface-first-delegation if available.
