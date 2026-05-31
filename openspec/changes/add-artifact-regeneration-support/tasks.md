## 1. Metadata Tracking System

- [ ] 1.1 Choose and implement metadata approach (metadata file vs frontmatter vs git-based)
- [ ] 1.2 Implement artifact metadata generation that records generation time and dependency mtimes
- [ ] 1.3 Store metadata in `.openspec-meta.json` in change directory
- [ ] 1.4 Add metadata reading/parsing utilities

## 2. Staleness Detection

- [ ] 2.1 Implement dependency graph for artifacts (proposal -> design -> specs -> tasks)
- [ ] 2.2 Add staleness detection logic comparing current mtime to recorded generation mtime
- [ ] 2.3 Handle edge cases: missing metadata, moved files, etc.
- [ ] 2.4 Add unit tests for staleness detection

## 3. Apply Integration

- [ ] 3.1 Integrate staleness detection into `/opsx:apply` workflow
- [ ] 3.2 Add interactive prompt when stale artifacts detected
- [ ] 3.3 Implement "continue anyway" option
- [ ] 3.4 Add clear warning output format

## 4. Regeneration Support

- [ ] 4.1 Add `--regenerate` flag to `/opsx:continue`
- [ ] 4.2 Implement regeneration logic that deletes specified artifact before generation
- [ ] 4.3 Handle artifact chain regeneration (e.g., regenerate tasks regenerates design)
- [ ] 4.4 Update workflow instructions to document regeneration

## 5. Documentation

- [ ] 5.1 Update `/opsx:apply` documentation to describe staleness warning
- [ ] 5.2 Document `/opsx:continue --regenerate` usage
- [ ] 5.3 Add troubleshooting section for regeneration issues

## 6. Verification

- [ ] 6.1 Manual test: edit design.md after tasks.md exists, run apply
- [ ] 6.2 Manual test: regenerate tasks with flag
- [ ] 6.3 Run existing tests to ensure no regressions
