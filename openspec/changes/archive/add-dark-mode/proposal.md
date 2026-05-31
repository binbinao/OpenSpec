# Add Dark Mode Skill for opsx

## Background
With the increasing demand for personalized user experiences, many command-line and UI-based tools provide dark mode support to reduce eye strain and improve usability in low-light environments. Currently, OpenSpec's opsx system lacks a built-in dark mode capability, which limits its adaptability to diverse user preferences and modern interface standards.

## Objective
Introduce a dark mode skill into opsx, allowing users to switch between light and dark themes for applicable UI components (such as welcome screen, ASCII art, and other text-based UI elements). The skill should be configurable via project or global settings and should integrate seamlessly with existing opsx skills.

## Implementation Approach
1. **Theme Definition**: Define color palettes for light and dark modes within the UI module (`src/ui/`).
2. **Configuration**: Extend project and global config schemas to include a `theme` setting (`light` | `dark` | `auto`).
3. **Skill Interface**: Create a new opsx skill (`dark-mode`) exposing commands to get and set the theme, and to toggle between modes.
4. **UI Integration**: Modify UI rendering logic to respect the selected theme (e.g., `src/ui/welcome-screen.ts`, `src/ui/ascii-patterns.ts`).
5. **Persistence**: Store user preference in global or project config for future sessions.
6. **Docs & Tests**: Update docs (e.g., `docs/opsx.md`) and add tests for theme switching behavior.

## Benefits
- Enhances visual comfort for users in low-light conditions.
- Aligns OpenSpec with modern UX expectations.
- Provides a foundation for future theming enhancements (e.g., custom colors).

## Scope
This proposal covers adding the dark mode skill to opsx, basic theme switching, and integration with core UI components. It does not include advanced custom theming or GUI-specific theming (outside current text-based UI scope).

## Risks & Mitigation
- **Inconsistent appearance**: Ensure all UI components adhere to the theme setting by centralizing color logic.
- **Config conflicts**: Validate theme setting against config schema and provide fallback to default theme.

## Future Extensions
- Support for user-defined color schemes.
- Automatic theme detection based on system preferences.