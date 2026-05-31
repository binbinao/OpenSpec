## Purpose

Define artifact staleness detection and regeneration support for OpenSpec workflow, enabling users to update artifacts mid-flight without manual deletion.

## ADDED Requirements

### Requirement: Artifact metadata tracking
The system SHALL track metadata for generated artifacts including generation time and dependency modification times.

#### Scenario: Metadata generated on artifact creation
- **WHEN** an artifact is generated during workflow
- **THEN** the system SHALL create a metadata file recording generation timestamp
- **AND** SHALL record modification times of dependency artifacts

#### Scenario: Metadata file structure
- **WHEN** metadata is stored
- **THEN** it SHALL include artifact path, generated_at timestamp, and dependency mtimes
- **AND** SHALL be stored in `.openspec-meta.json` in the change directory

### Requirement: Staleness detection
The system SHALL detect when downstream artifacts are stale relative to upstream changes.

#### Scenario: Detect stale downstream artifact
- **WHEN** apply runs with existing downstream artifact
- **AND** upstream dependency has been modified since downstream was generated
- **THEN** the system SHALL identify the artifact as stale
- **AND** SHALL report which dependencies caused staleness

#### Scenario: No staleness detected
- **WHEN** apply runs with all downstream artifacts up-to-date
- **THEN** the system SHALL proceed without staleness warnings

### Requirement: Staleness user prompt
The system SHALL prompt users with clear options when stale artifacts are detected.

#### Scenario: Stale artifact detected during apply
- **WHEN** staleness is detected during apply execution
- **THEN** the system SHALL present options:
- **AND** SHALL include "Regenerate" (recommended)
- **AND** SHALL include "Continue anyway"
- **AND** SHALL include "Cancel"

#### Scenario: User chooses regenerate
- **WHEN** user selects "Regenerate" from staleness prompt
- **THEN** the system SHALL delete the stale artifact
- **AND** SHALL regenerate it from current upstream artifacts

#### Scenario: User chooses continue anyway
- **WHEN** user selects "Continue anyway"
- **THEN** the system SHALL proceed with existing artifacts
- **AND** SHALL NOT regenerate

### Requirement: Explicit regeneration flag
The system SHALL support explicit regeneration of specific artifacts via flag.

#### Scenario: Regenerate flag on continue
- **WHEN** user runs continue with `--regenerate <artifact>` flag
- **THEN** the system SHALL delete the specified artifact and all downstream artifacts
- **AND** SHALL regenerate from the specified point onward

#### Scenario: Regenerate updates metadata
- **WHEN** an artifact is regenerated
- **THEN** the system SHALL update metadata with new generation timestamp
- **AND** SHALL update dependency mtimes

### Requirement: Non-interactive regeneration
The system SHALL support regeneration in non-interactive contexts.

#### Scenario: Non-interactive staleness handling
- **WHEN** staleness detected in non-interactive mode
- **THEN** the system SHALL fail with deterministic error message
- **AND** SHALL list stale artifacts and required regeneration

## MODIFIED Requirements

### Requirement: Continue workflow supports regeneration
The continue workflow SHALL support explicit regeneration in addition to creation.

#### Scenario: Continue with regenerate flag
- **WHEN** continue is invoked with `--regenerate` flag
- **THEN** it SHALL behave as specified in the regeneration flag requirements
- **AND** SHALL update metadata appropriately
