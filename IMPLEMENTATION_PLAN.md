# Incremental implementation plan

This plan turns the requests in [Specs.md](./Specs.md) into small, verifiable
changes that build on the current design in [ARCHITECTURE.md](./ARCHITECTURE.md).
The work should proceed incrementally; each phase should be reviewed and
validated before starting the next.

## Guiding principles

- Keep the original OSM state distinct from user-saved scenarios. “Show before
  edits” should continue to mean the original, unedited state.
- Reuse the project's GeoJSON persistence and migration patterns where suitable.
- Make comparisons explicit about which two configurations are being compared.
- Keep routing behavior and result methodology visible to users, especially
  when demand data is synthetic or incomplete.
- Add focused backend and frontend tests for each new behavior.

## Phase 0: Resolve product behavior

Confirm these details before implementing the features that depend on them:

- Whether a saved configuration is a snapshot of all editable state or only
  selected edit types.
- Whether editing continues in a working copy after saving, and what happens
  when switching configurations with unsaved edits.
- Whether scenarios can be duplicated, renamed, and deleted, and whether a
  configuration can be protected from accidental edits.
- Whether an alley may be used for a trip whose origin or destination is on
  that alley, while disallowing it as a through-route.
- Whether a road excluded from the driving graph can still serve as a trip's
  origin or destination, or must be absent from routes entirely.
- What “incoming” and “outgoing” mean for destination and area-wide analyses.
- How unreachable trips are handled in averages, and which aggregate statistic
  and demand weighting to show.
- Whether notes attach to map coordinates, roads/intersections, boundaries, or
  more than one of these.

These decisions can be refined during implementation, but should be recorded
before the affected behavior is built.

## Phase 1: Save and restore named configurations

**First implementation slice.** Add the ability to save the current edits as
one named configuration, then restore it later. Keep the existing original OSM
baseline and “Show before edits” behavior unchanged.

Scope:

- Define a configuration snapshot that includes all relevant editable state:
  modal and diagonal filters, travel flows, turn restrictions, road
  classifications, and driving-graph eligibility.
- Persist named snapshots with the project using a backward-compatible
  savefile/schema change.
- Add minimal UI to save/name and select/restore a configuration.
- Establish explicit behavior for unsaved edits when switching.
- Preserve existing projects and legacy savefiles through migration or
  compatible defaults.

Acceptance checks:

- A snapshot round-trips through save, reload, and restore with all edit types
  intact.
- Existing project savefiles still load and retain their current behavior.
- Saving or switching a scenario does not change the original OSM baseline.
- Renaming, duplicate names, and any supported deletion behavior are handled
  consistently.

## Phase 2: Road classification and driving-graph eligibility

Rename the editing tool to “Reclassify Road” and extend the current binary
main-road designation to represent local, main, and alley classifications.
Also let users include or exclude existing roads from driving routes without
removing their geometry from the map.

Scope:

- Add an explicit road classification to backend state and project persistence.
- Update the editor interaction, display, and explanatory text.
- Add a separate, undoable and persistent driving-graph eligibility setting
  for existing roads. Keep this distinct from modal filters, which represent
  access restrictions at a point or along a road.
- Keep excluded road geometry visible and editable on the map; do not introduce
  user-drawn roads as part of this phase.
- Define and implement alley routing so alleys are not used as transit
  shortcuts while local access remains possible, per the agreed behavior.
- Update affected neighborhood derivations and rendering if classification
  changes should affect them.

Acceptance checks:

- Repeated clicks cycle through the specified classifications, including undo
  and redo.
- Excluding an existing road prevents its use in routes according to the
  agreed endpoint behavior, while retaining its map geometry.
- Graph eligibility persists across save/reload and participates in named
  configuration snapshots.
- Save/reload preserves classifications.
- Routing tests demonstrate both the through-routing restriction and any
  allowed local access.
- Existing roads and projects retain their prior classification behavior.

## Phase 3: Compare two configurations for one destination

Generalize the existing original-versus-edited comparison so it can route
between two selected configurations. Then extend “Impact on One Destination”
to support both directions.

Scope:

- Build or reuse routers for the selected configuration pair and invalidate
  cached results when either configuration changes.
- Add configuration selection to the analysis UI.
- Retain the current outbound result and add the reverse, destination-to-road
  analysis using directed routing rules.
- Clearly show when a route is unavailable in either configuration.

Acceptance checks:

- A route comparison uses the selected configurations, not implicitly the
  original OSM state.
- Inbound routes respect one-way streets and turn restrictions.
- Changes to either selected configuration update the analysis.

## Phase 4: Area-wide average travel-time analysis

Add a new analysis mode for comparing average travel times across a selected
area and configuration pair.

Scope:

- Reuse the existing OD demand and routing infrastructure where applicable.
- Support agreed scopes, such as the full study area, local roads, and selected
  destinations.
- Report inbound and outbound results separately.
- Document sampling, demand weights, unreachable-trip handling, and any
  limitations of synthetic or census-based demand.

Acceptance checks:

- Aggregate results match small deterministic test cases with known routes and
  trip weights.
- Scope and direction filters select the intended trips.
- Unreachable routes are handled according to the agreed rule and are not
  silently omitted.
- The UI identifies the selected configurations, area, and demand assumptions.

## Phase 5: Map notes

Add notes as project-persisted map features.

Scope:

- Implement note creation, viewing, editing, and deletion.
- Store note text and a stable geographic anchor; include an optional linked
  feature if that behavior is selected.
- Keep notes associated with the project and, if required, a specific
  configuration.

Acceptance checks:

- Notes survive autosave, reload, and project export/import.
- Editing or deleting a note does not alter routing or other project features.
- Notes render and remain selectable at their stored locations.

## Cross-cutting validation

For each phase, run the smallest relevant existing test/build commands, then
broaden validation when changes cross subsystem boundaries:

- Backend unit tests for state, persistence, and routing behavior.
- Frontend tests for project storage and scenario controls.
- Web type checking/build for UI and WASM wrapper changes.
- Savefile migration and export/import checks for persistence changes.

Do not treat generated WASM bindings or prebuilt assets as source-of-truth;
regenerate them through the repository's existing build workflow when a backend
API changes.
