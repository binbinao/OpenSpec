# Tasks: Dark Mode Implementation

## Setup
- [ ] Extend global and project config schemas to include `ui.theme` (light/dark/auto)
- [ ] Create theme palette definitions in `src/ui/themes.ts`

## UI Integration
- [ ] Modify `src/ui/welcome-screen.ts` to respect selected theme
- [ ] Update `src/ui/ascii-patterns.ts` for dark mode colors
- [ ] Ensure all UI components reference centralized theme values

## Skill Implementation
- [ ] Implement `opsx dark-mode get` command
- [ ] Implement `opsx dark-mode set --mode` command
- [ ] Implement `opsx dark-mode toggle` command
- [ ] Add theme persistence logic (save to config)

## Testing & Docs
- [ ] Add unit tests for theme switching logic
- [ ] Verify rendering in both light and dark modes
- [ ] Update `docs/opsx.md` with dark mode usage instructions

## Finalization
- [ ] Review and update any related documentation
- [ ] Mark change as complete and ready for archive