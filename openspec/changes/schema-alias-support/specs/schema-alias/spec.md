## Purpose

Define schema alias support allowing multiple schema names to resolve to the same physical schema directory, enabling backward-compatible schema renaming.

## ADDED Requirements

### Requirement: Schema alias resolution
The system SHALL support schema name aliases so that multiple names can resolve to the same schema.

#### Scenario: Alias resolves to primary schema
- **WHEN** user specifies a schema name that is an alias
- **THEN** the system SHALL resolve it to the primary schema name
- **AND** SHALL use the primary schema directory for loading

#### Scenario: Primary schema name used directly
- **WHEN** user specifies the primary schema name
- **THEN** the system SHALL resolve it normally without alias resolution

#### Scenario: Unknown schema name
- **WHEN** user specifies a schema name that is neither primary nor alias
- **THEN** the system SHALL fail with an appropriate error message

### Requirement: Bidirectional alias support
Aliases SHALL work in all contexts where schema names are used.

#### Scenario: CLI init with alias
- **WHEN** user runs `openspec init --schema openspec-default`
- **THEN** the system SHALL resolve `openspec-default` to `spec-driven`

#### Scenario: Config file with alias
- **WHEN** config file specifies `schema: openspec-default`
- **THEN** the system SHALL treat it as equivalent to `schema: spec-driven`

#### Scenario: Schema list shows alias
- **WHEN** user lists available schemas
- **THEN** the system SHALL show alias names as valid options
- **AND** SHALL indicate the resolved primary schema

### Requirement: Alias configuration
The system SHALL define aliases in a central location.

#### Scenario: Aliases defined in schema registry
- **WHEN** schema resolver loads
- **THEN** it SHALL load alias mappings from a configuration source
- **AND** make them available for resolution

#### Scenario: Default alias for spec-driven
- **WHEN** the system initializes default aliases
- **THEN** it SHALL include `openspec-default` as an alias for `spec-driven`

## MODIFIED Requirements

### Requirement: Default schema constant
The default schema constant SHALL be updated to use the canonical name.

#### Scenario: Default schema reference
- **WHEN** code references the default schema
- **THEN** it SHALL use `openspec-default` as the canonical name
- **AND** `spec-driven` remains functional as a supported alias
