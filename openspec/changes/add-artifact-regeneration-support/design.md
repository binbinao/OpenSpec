## Context

Currently `/opsx:apply` reads whatever's on disk without checking if upstream artifacts have been modified. Users who edit design.md after tasks.md are generated must manually delete tasks.md to regenerate.

## Goals / Non-Goals

**Goals:**

- Detect when downstream artifacts are stale relative to upstream changes
- Prompt user with clear options when staleness detected
- Support explicit regeneration of specific artifacts
- No false positives (only warn when genuinely stale)

**Non-Goals:**

- Automatic regeneration without user consent
- Blocking apply entirely (just warn)
- Tracking code file changes (only artifact dependencies)

## Decisions

### 1. Metadata Approach: Metadata File

Use `.openspec-meta.json` in change directory:

```json
{
  "tasks.md": {
    "generated_at": "2025-01-24T10:00:00Z",
    "dependencies": {
      "design.md": "2025-01-24T09:55:00Z",
      "specs/feature/spec.md": "2025-01-24T09:50:00Z"
    }
  }
}
```

Rationale: frontmatter pollutes generated artifacts, git-based has edge cases with non-git projects.

### 2. Artifact Dependency Graph

Standard dependency order:
- proposal -> design -> specs -> tasks

Each artifact depends on all previous artifacts in the chain.

### 3. Staleness Detection Algorithm

```ts
function detectStaleness(artifactPath: string, metadata: ArtifactMetadata): StalenessResult {
  const deps = metadata[artifactPath].dependencies;
  const staleDeps = [];

  for (const [dep, depMtime] of Object.entries(deps)) {
    const currentMtime = getMtime(dep);
    if (currentMtime > new Date(depMtime)) {
      staleDeps.push(dep);
    }
  }

  return { isStale: staleDeps.length > 0, staleDependencies: staleDeps };
}
```

### 4. Apply Integration

Before executing tasks:

1. Load metadata if exists
2. For each artifact to be applied, check staleness
3. If stale, prompt user:
   - "Regenerate tasks (recommended)"
   - "Continue anyway with current tasks"
   - "Cancel"
4. Continue based on user choice

### 5. Regeneration Mechanism

Add `--regenerate <artifact>` flag to `/opsx:continue`:

- Deletes specified artifact and all downstream artifacts
- Regenerates from the specified point onward
- Updates metadata on regeneration

Example workflow:
```
/opsx:continue --regenerate tasks
# Deletes tasks.md, regenerates from design.md
```

### 6. Metadata Lifecycle

- Generated when artifact is created
- Updated on regeneration
- Deleted when artifact is manually removed by user
- Migration: existing artifacts without metadata are treated as having no dependencies (not stale)

## File Changes

- `src/core/workflow/apply.ts` - staleness detection integration
- `src/core/workflow/continue.ts` - regeneration flag handling
- `src/core/metadata/artifact-metadata.ts` - metadata read/write/compare
- `.openspec-meta.json` - per-change metadata file (generated)

## Risks / Trade-offs

**Risk: Cross-platform mtime comparisons**
→ Mitigation: normalize to UTC timestamps, handle filesystem precision differences

**Risk: Metadata drift from manual edits**
→ Mitigation: warn but don't block; user can always choose "continue anyway"

## Rollout Plan

1. Implement metadata tracking system
2. Add staleness detection logic
3. Integrate with apply workflow
4. Add regeneration flag to continue
5. Documentation and testing
