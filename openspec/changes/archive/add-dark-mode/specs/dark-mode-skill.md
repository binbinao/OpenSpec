# Dark Mode Skill Specification for opsx

## Overview
The **dark-mode** skill enables opsx users to manage UI theme settings for OpenSpec's text-based interfaces. It supports switching between light, dark, and auto (system preference) modes and persists the choice in configuration files.

## Commands

### Get Current Theme
```bash
opsx dark-mode get
```
Returns the currently active theme (`light`, `dark`, or `auto`).

### Set Theme
```bash
opsx dark-mode set --mode <light|dark|auto>
```
Sets the theme to the specified mode and saves it to the active configuration scope.

### Toggle Theme
```bash
opsx dark-mode toggle
```
Switches between `light` and `dark` modes. If the current mode is `auto`, resolves to the detected system preference before toggling.

## Configuration

The theme setting is stored under `ui.theme` in the configuration hierarchy:

```yaml
ui:
  theme: light  # or dark, auto
```

Supported scopes:
- **Project config** (`openspec/project.md`)
- **Global config** (`src/core/global-config.ts`)

Precedence: Project > Global > Default (`light`)

## Behavior
- When `auto` is selected, the skill detects the system preference (via environment or OS settings) at runtime.
- UI components (`welcome-screen`, `ascii-patterns`, etc.) should query the active theme before rendering and adjust colors accordingly.
- Invalid or unsupported values fall back to `light` and emit a warning.

## Examples

### Set dark mode globally
```bash
opsx dark-mode set --mode dark
```

### Retrieve current mode
```bash
$ opsx dark-mode get
dark
```

### Toggle from light to dark
```bash
$ opsx dark-mode toggle
Theme switched to dark
```

## Integration Requirements
- Extend config schemas to validate `ui.theme`.
- Implement theme resolution logic in UI modules.
- Provide helper functions for other skills to access the current theme.

## Future Extensions
- Allow custom color definitions per theme.
- Add support for per-component theming.
- Integrate with OS-level dark mode listeners for real-time updates.