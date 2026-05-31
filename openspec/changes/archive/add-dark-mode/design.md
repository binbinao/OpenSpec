# Design: Dark Mode for OPSX

## Overview
Define how the dark mode theme integrates with existing UI components and config system.

## Components Affected
- Welcome screen
- ASCII art patterns
- Any other text-based UI elements

## Approach
- Extend `src/ui/*` to support color theming
- Read `ui.theme` from config (light/dark/auto)
- Centralize palette definitions for maintainability

## Tasks
- Define color variables for both modes
- Modify rendering logic to adapt output
- Ensure theme persistence across sessions