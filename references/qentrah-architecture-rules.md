# Qentrah Architecture Rules

## Product model

Organization is the workspace root. Business data belongs to `organizationId`; account and user data holds only security, profile, device, and personal preference data.

```text
Organization workspace
  -> Spaces
    -> Projects
      -> work records
  -> Surfaces
    -> tabs
      -> saved views or pinned records
  -> Configuration
    -> workflows, fields, layouts, templates, permissions
```

## Database rules

- Every business table starts with `organizationId`.
- High-traffic records use `recordState: "active" | "archived" | "deleted"` with lifecycle timestamps.
- Use typed Convex IDs for internal relations where practical.
- Denormalize `spaceId` and `projectId` only for real indexed surfaces.
- Durable configuration must not use `v.any()`.
- Seed defaults are organization rows, not permanent hardcoded behavior.

## Views, tabs, and surfaces

Use `surfaces` for render locations, `surfaceTabs` for ordered tabs, and `savedViews` for reusable typed query/render configurations. Tabs reference records; they do not copy record data into configuration.

## Workflow and field configuration

Use `workflowDefinitions` and ordered `workflowStates` for configurable stages. Use `customFieldDefinitions`, `customFieldValues`, `fieldLayouts`, and optional `fieldOptions` for field systems. Scope configuration with `scopeType` and `scopeId`.

## Index rules

Use tenant-first indexes for high-traffic records, such as:

```ts
.index("by_org_state_updated", ["organizationId", "recordState", "updatedAt"])
.index("by_org_project_state_updated", ["organizationId", "projectId", "recordState", "updatedAt"])
.index("by_org_space_state_updated", ["organizationId", "spaceId", "recordState", "updatedAt"])
```

Do not use unbounded `.collect()` plus `.filter()` for high-traffic data. Cursor-paginate lists and add indexes only for real query paths.

## Convex folder rule

```text
convex/<domain>/
  validators.ts
  data.ts
  policy.ts
  read.ts
  write.ts
  present.ts
  lifecycle.ts
```

`read.ts` and `write.ts` orchestrate; `data.ts` owns database behavior; `policy.ts` owns access decisions; `lifecycle.ts` owns transitions; and `present.ts` owns projections. Public functions define `args` and `returns`.

## Hono backend rule

```text
src/server/domains/<domain>/
  routing/
  handlers/
  validation/
  services/
  errors/
```

Routes register paths, handlers parse and respond, services compose workflows, validation owns Zod schemas, and authorization is enforced server-side.

## Shared helper and domain interface rules

Share helpers only for real cross-domain invariants such as authorization, audit, retry/backoff, structured errors, pagination, resource references, query normalization, workflow resolution, and field coercion.

Each parent domain exports its supported types, hooks, request functions, view models, and entry UI modules. Other domains import that parent interface rather than deep implementation paths.

## New domain checklist

- Name the resource type and owner module.
- Define scope, lifecycle, workflow, seed defaults, indexes, permissions, and audit.
- Define the parent domain interface.
- Test that interface.
