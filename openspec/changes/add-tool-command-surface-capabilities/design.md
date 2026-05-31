## Context

OpenSpec currently assumes command delivery maps directly to command adapters. Tools like Trae that invoke OpenSpec workflows via skills (not adapters) end up with no invocable command surface when using `delivery=commands`.

This change introduces an explicit capability model to classify how each tool exposes OpenSpec commands.

## Goals / Non-Goals

**Goals:**

- Add explicit command-surface capability metadata to tools
- Make delivery behavior capability-aware in init/update
- Add preflight validation and clear messaging for incompatible tool selections
- Preserve backward compatibility for existing adapter-based tools

**Non-Goals:**

- Changing how existing adapter-based tools work
- Supporting new delivery modes beyond `both`, `skills`, `commands`

## Decisions

### 1. Tool Command-Surface Capability Metadata

Add optional field to tool metadata in `src/core/config.ts`:

```ts
type CommandSurfaceCapability = 'adapter' | 'skills-invocable' | 'none';

interface ToolMetadata {
  // ... existing fields
  commandSurface?: CommandSurfaceCapability;
}
```

Inference rules:

1. If explicit `commandSurface` metadata exists, use it
2. If no explicit metadata but tool has registered adapter, infer as `adapter`
3. Otherwise, infer as `none`

Initial explicit overrides:

- Trae -> `skills-invocable`

### 2. Capability-Aware Delivery Behavior

Update init and update to compute per-tool effective actions:

**`delivery=both`:**

- Generate skills for all tools with `skillsDir`
- Generate command files only for `adapter` tools
- `none`: no action; MAY emit compatibility warning

**`delivery=skills`:**

- Generate skills for all tools with `skillsDir`
- Remove adapter-generated command files
- `none`: no action; MAY emit compatibility warning

**`delivery=commands`:**

- `adapter`: generate commands, remove skills
- `skills-invocable`: generate/keep skills as command surface (do NOT remove)
- `none`: fail fast with clear error

### 3. Capability Resolver Implementation

Create shared resolver in `src/core/shared/tool-capability.ts`:

```ts
function resolveToolCapability(
  toolId: string,
  toolMetadata: ToolMetadata,
  adapterRegistry: AdapterRegistry
): CommandSurfaceCapability {
  if (toolMetadata.commandSurface) {
    return toolMetadata.commandSurface;
  }
  if (adapterRegistry.has(toolId)) {
    return 'adapter';
  }
  return 'none';
}
```

### 4. Preflight Validation

Before writing/removing artifacts:

- **Interactive**: show compatibility note when selected tools include `skills-invocable` under `delivery=commands`
- **Non-interactive**: fail with deterministic error listing incompatible tools

### 5. Tool Detection Under Commands Delivery

Update `src/core/shared/tool-detection.ts`:

- Under `delivery=commands`, still detect `skills-invocable` tools if managed skills exist
- Exclude `none` tools from command-surface artifact detection

### 6. Drift Detection for Skills-Invocable Tools

When `delivery=commands` is configured and a tool has `skills-invocable` capability:

- Do NOT report drift for existing managed skills
- Consider skills up-to-date if content matches current generation

### 7. Documentation Updates

- Document capability model in `docs/supported-tools.md`
- Document delivery behavior interactions in `docs/cli.md`
- Add troubleshooting note for "commands-only + unsupported tool" failures

## File Changes

- `src/core/config.ts` - add commandSurface field to tool metadata
- `src/core/shared/tool-capability.ts` - new capability resolver
- `src/core/shared/tool-detection.ts` - capability-aware detection
- `src/core/init.ts` - capability-aware generation planning
- `src/core/update.ts` - capability-aware sync planning
- `docs/supported-tools.md` - document Trae behavior
- `docs/cli.md` - document delivery capabilities

## Risks / Trade-offs

**Risk: Adapter inference behavior changes**
If a tool later adds an adapter, it would switch from `skills-invocable` to `adapter`.
→ Mitigation: explicit metadata takes precedence; prefer explicit `skills-invocable` for Trae.

**Risk: User confusion about retained skills under commands mode**
→ Mitigation: clear output messaging explaining why skills are kept for certain tools.

## Rollout Plan

1. Add command-surface metadata and capability resolver
2. Update init to be capability-aware
3. Update update to be capability-aware with drift detection fix
4. Add preflight validation
5. Update documentation
6. Add test coverage
