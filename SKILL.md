---
name: qentrah-architecture-guardian
description: Use when designing, reviewing, refactoring, or implementing Qentrah database schema, Convex functions, Hono backend modules, domain folder structure, modelization, saved views/tabs, configurable fields/workflows, shared helpers, or reusable UI architecture.
---

# Qentrah Architecture Guardian

Use this skill to keep Qentrah architecture scalable, organization-rooted, and modelized. Working code is not enough: database shape, indexes, backend split, domain interface, and reuse rules must be correct before implementation.

## Required context

Before proposing or editing architecture, read:

- Root `CONTEXT.md`
- Relevant files in `docs/decisions/`
- `docs/architecture/scalable-database-and-modelization-blueprint.md`, if present
- `references/qentrah-architecture-rules.md`

If the repository blueprint is missing, use the reference file as the architecture contract and propose creating the repository blueprint.

## Workflow

1. Identify whether the request touches schema, backend, domain modelization, shared UI/helpers, saved views/tabs, configurable fields/workflows, or a new domain.
2. Map the feature to its parent domain module and subdomain modules.
3. Classify data as account/security, organization workspace, scoped records, configuration, ephemeral UI state, or external raw payloads.
4. Check schema shape: `organizationId`, lifecycle and index fields, typed config, references rather than copied records, and configurable workflow rows.
5. Check the backend split: Convex domain modules own validators, data, policy, read, write, present, and lifecycle; Hono routes are adapters only.
6. Check the domain interface: callers use the parent interface, implementation stays internal unless exported, and one behavior has one implementation path.
7. Produce a plan before broad edits, including current behavior, target architecture, migration waves, indexes, tests, and compatibility strategy.

## Stop conditions

Stop and produce an architecture plan instead of editing when:

- A schema migration touches more than one high-traffic table.
- A feature needs saved view, tab, workflow, or custom-field architecture.
- A refactor changes authorization or audit behavior.
- A helper would be shared across domains without a clear interface.
- A new domain lacks an interface, schema contract, indexes, or lifecycle model.

## Output rules

For reviews, lead with risks and exact files. For plans, include the target data model, tables to add or consolidate, indexes, backend folder split, parent interface, migration waves, and validation checks.

For implementation, keep passes small: schema contract and validators; read/write modules; adapters; UI/domain migration; old-path deletion; tests and source guards.
