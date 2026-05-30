# Contributing to Manifold

Manifold improves with contributors who have the hardware. If you play a
controller listed here — or one that isn't — you can help.

---

## What to Contribute

### Upgrading a stub to draft or complete

Most profiles are stubs. The most useful thing you can do is fill one in:

1. Find the controller's YAML in `controllers/`
2. Replace `TODO` fields with real values
3. Add any CCs not yet listed
4. Document quirks you've discovered
5. Update `status:` accordingly

Use your instrument. Play it. Look at what comes out of a MIDI monitor
([MIDI Monitor](https://www.snoize.com/midimonitor/) on macOS,
[MIDI Wrench](https://www.crudebyte.com/mobile/midi_wrench/) on iOS).
Measure update rates if you can. Note edge cases.

### Adding a new controller

1. Copy `_template.yaml` to the appropriate category folder
2. Name the file `slug-form.yaml` (lowercase, hyphens, no spaces)
3. Fill in as completely as you can
4. Open a PR — stubs are welcome if the controller is notable

Use the [new controller issue template](.github/ISSUE_TEMPLATE/new-controller.md)
to propose a controller before filing a PR if you want feedback first.

### Correcting a profile

If something is wrong — wrong CC number, incorrect behavior, missing quirk —
open an issue using the [correction template](.github/ISSUE_TEMPLATE/profile-correction.md)
or just fix it and submit a PR with a note on how you verified it.

### Proposing schema changes

The schema is versioned. Breaking changes require a version bump and a migration
path for existing profiles. Open a discussion issue first before implementing.

---

## Profile Quality

**Factual before opinionated.** The core profile — what the controller sends,
what its CCs are, what's configurable — should be verifiable by anyone with
the hardware. Keep it accurate.

**TODOs over guesses.** If you don't know a value, leave it `null` and add a
comment: `# TODO: verify`. Don't fill in plausible-sounding values you haven't confirmed.

**Quirks matter.** The `quirks` list is often the most valuable part of a
profile. Undocumented behaviors, gotchas, firmware differences, interaction
bugs — these are what users and developers need and can't find in official docs.

**Measurements welcome.** If you can measure a controller's actual MIDI update
rate (messages/second for a continuous source like breath or pressure), that
data is genuinely useful. Add it to `update_rate:`.

---

## The `mappings:` Section

The `mappings:` section in each profile is for synth/tool-specific routing.
It's namespaced — `mappings.vane`, `mappings.your_synth` — so multiple projects
can coexist. If you're maintaining a synth or tool that consumes Manifold data,
you're welcome to add your mappings here via PR.

The shared factual data (everything outside `mappings:`) is the common ground.
Keep it clean.

---

## Style

- YAML, 2-space indent
- Comments on anything non-obvious
- `null` for unknown/not-applicable (not empty string, not `false`)
- Lowercase `slug:` values, kebab-case
- `schema_version:` must match current schema when submitting new profiles

---

## Verification

The highest-value contribution is moving a profile from `complete` to `verified`.
This means you have the hardware, you've run it through a MIDI monitor,
you've confirmed the documented behavior matches reality, and you can say so
in the PR. Please note firmware version and any software used.

---

## Questions

Open a discussion. This is a small project and the norms are still forming.
