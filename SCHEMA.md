# Manifold — Controller Profile Schema Reference

Version: `0.2`

**Changelog from 0.1:**
- Added `param_type` to CCs and expression dimensions: `continuous | discrete | config`
- Added `resolution` and `update_rate` to continuous sources
- Replaced synth-specific `vane:` section with namespaced `mappings:` section
- Added `prescriptive` implementation type for controllers under active development

---

## The Core Distinction: Setup vs. Performance

Every parameter a controller sends falls into one of three types.
This distinction is what makes Manifold data useful to synths and tools,
not just humans.

| `param_type` | Description | Examples |
|---|---|---|
| `config` | Set once, before playing | MIDI channel, MPE zone, pitch bend range |
| `discrete` | Per-event, not continuous | Note-on velocity, note-off, program change |
| `continuous` | Changes while playing | Breath, pressure, pitch bend (live), slide |

For `continuous` parameters, two additional fields matter:

- **`resolution`** — `7bit` (0–127), `14bit` (0–16383 via MSB+LSB), or `float` (MIDI 2.0)
- **`update_rate`** — approximate messages/second under normal playing; `null` if unknown

Higher resolution and update rate = more nuanced modulation. These directly inform
smoothing, interpolation, and display decisions in any tool consuming Manifold data.

---

## `meta` — Identity

```yaml
meta:
  name: ""              # (required) Display name
  slug: ""              # (required) kebab-case unique identifier
  manufacturer: ""      # (required) Maker or developer
  version: ""           # (optional) Hardware revision or software version
  category: ""          # (required) See valid values below
  schema_version: "0.2" # (required)
  status: stub          # (required) stub | draft | complete | verified
  notes: ""             # (optional)
```

### Valid `category` values

| Value | Description |
|---|---|
| `wind` | Breath/embouchure-driven controller |
| `mpe-surface` | Per-note XYZ expression via touch surface |
| `expressive` | Standalone expressive instrument, non-standard paradigm |
| `software` | AUv3, iOS app, or desktop software controller |
| `vane-native` | Developed within the MTILT ecosystem; profile is partly prescriptive |
| `standard-midi` | Note + velocity, no per-note expression |
| `cc-only` | Primary function is sending CCs / control data; notes may be incidental |

---

## `connectivity`

```yaml
connectivity:
  protocol: []              # standard-midi | mpe | midi-2 | osc | proprietary
  connection: []            # usb | bluetooth-midi | midi-din | virtual-midi
                            # network-osc | ios-au | proprietary-usb
  middleware_required: false
  middleware: []
  # Each middleware entry:
  # - name: EigenD
  #   description: ""
  #   url: ""
  #   platforms: [macos, windows, linux]
  #   status: community-maintained
  companion_app: null
  companion_app_platforms: []
```

---

## `implementation`

```yaml
implementation:
  type: fixed               # fixed | configurable | software-defined
                            # user-defined | prescriptive
  config_method: null       # device-menu | companion-app | sysex | software | n/a
  bidirectional: false      # Can the host send config/layout data back to the controller?
  bidirectional_notes: null # How? Via what protocol/mode?
  notes: null
```

`bidirectional: true` flags controllers where the host can reconfigure the device
at runtime — Exquis developer mode, MIDI-CI Property Exchange, proprietary SysEx.
This is the foundation of the two-way communication roadmap.

`prescriptive` type is used for controllers under active development where the
profile defines what the controller *should* send, informing implementation.

---

## `modes` *(optional)*

For controllers with distinct MIDI output modes.

```yaml
modes:
  - id: standard
    name: Standard MIDI
    default: true
    notes: null
  - id: mpe
    name: MPE
    default: false
    notes: null
```

**One file, many host profiles.** A single Manifold file documents the full
option space — all modes, all configurable parameters. A consuming host
(synth, tool, rig manager) represents a *choice* over that space: one
controller file × one mode × one frozen config-state. The same `sylphyo.yaml`
backs "Sylphyo breath on CC2" and "Sylphyo breath on aftertouch" as two
distinct host profiles.

---

## Named-Source Registry

Conventional CC assignments for common continuous sources. Consumers can use
these as seeds when a controller profile lists a source by name but the CC
number is device-configurable.

| Source name | Conventional CC | Notes |
|---|---|---|
| Mod wheel | CC1 | Present on nearly every keyboard-style controller |
| Breath | CC2 | Standard for wind controllers; CC11 used as a mirror/alt |
| Expression pedal | CC11 | Also CC4 on some pedalboards (e.g. FCB-01) |
| Sustain | CC64 | Standard pedal input |
| Slide / timbre | CC74 | MPE Y-axis convention (MIDI spec recommendation) |

These are defaults and conventions, not requirements. Always prefer an
explicit `cc:` value in the profile when the hardware has a fixed assignment.

---

## `sends` — MIDI Output

```yaml
sends:
  notes: false
  note_range:
    low: 0
    high: 127
  velocity: false
  velocity_resolution: 7bit
  channel_pressure: false
  poly_pressure: false
  pitch_bend: false
  pitch_bend_range_semitones: 2
  pitch_bend_range_configurable: false
  program_change: false
  clock: false
  sysex: false

  ccs:
    - cc: 2
      name: Breath
      description: ""
      param_type: continuous   # continuous | discrete | config
      resolution: 7bit         # 7bit | 14bit | float
      update_rate: null        # messages/sec if known
      mode: null               # null = all modes; or mode_id
      configurable: false
```

---

## `expression` — Expressive Dimensions

```yaml
expression:
  breath:
    source: null             # cc2 | cc11 | channel-pressure | poly-pressure | null
    param_type: continuous
    resolution: 7bit
    update_rate: null
    notes: null

  pressure:                  # Finger pressure, not breath
    source: null
    param_type: continuous
    resolution: 7bit
    update_rate: null
    notes: null

  slide:                     # Lateral movement; CC74 in MPE
    source: null
    param_type: continuous
    resolution: 7bit
    update_rate: null
    notes: null

  pitch:
    source: null
    param_type: continuous
    resolution: 14bit        # Pitch bend is always 14-bit in standard MIDI
    range_semitones: null
    update_rate: null
    notes: null

  strike:                    # Note-on velocity
    source: null
    param_type: discrete
    resolution: 7bit
    notes: null

  lift:                      # Note-off velocity
    source: null
    param_type: discrete
    resolution: 7bit
    notes: null

  roll:                      # Key tilt; Eigenharp-style
    source: null
    param_type: continuous
    resolution: 7bit
    update_rate: null
    notes: null

  acceleration:              # Accelerometer / motion
    source: null
    param_type: continuous
    resolution: 7bit
    update_rate: null
    notes: null

  strips: []
  # Strip entry:
  # - id: strip-left
  #   name: Left Strip
  #   source: cc
  #   cc: null
  #   param_type: continuous
  #   resolution: 7bit
  #   update_rate: null
  #   notes: null
```

---

## `mpe` *(optional)*

```yaml
mpe:
  zone: null                 # lower | upper | both | configurable
  member_channels: null
  manager_channel: null
  per_note_pitch_bend_range: null
  notes: null
```

---

## `overlays` *(optional, surface controllers)*

For controllers with swappable physical overlays that redefine the send map
(e.g. Sensel Morph). Each overlay is effectively a sub-controller with its
own `sends` and `expression`. Some overlays contain on-device layout switches,
modelled as nested `layouts:`.

```yaml
overlays:
  - id: piano
    name: Piano Overlay
    default: false
    sends:
      notes: true
      velocity: true
      poly_pressure: true
      ccs: []
    expression:
      pressure:
        source: poly-pressure
        notes: null
      # ... other dimensions as needed

  - id: buchla-thunder
    name: Buchla Thunder Overlay
    default: false
    notes: "On-overlay button switches between two sub-layouts."
    layouts:
      - id: layout-a
        name: Layout A
        default: true
        sends:
          notes: true
          ccs: []
        expression: {}
      - id: layout-b
        name: Layout B
        default: false
        sends:
          notes: true
          ccs: []
        expression: {}
```

Each overlay (or layout within an overlay) maps to one host rig.

---

## `mappings` *(optional, namespaced)*

Any synth or tool can embed its own parameter routing here, namespaced by
a short identifier. The shared factual data above is the common ground;
mappings are layered on top.

```yaml
mappings:
  your_synth:
    # Parameters to surface in a setup/patch panel (config-type)
    patch_tab:
      - param: mpe_mode
        value: true
        notes: null
      - param: pitchbend_range
        value: 48
        notes: "Must match controller setting"

    # Continuous and discrete sources for modulation routing
    modulation_sources:
      - id: breath
        source: cc2
        param_type: continuous
        resolution: 7bit
        suggested_target: dynamics
        notes: null
      - id: pitch
        source: pitch-bend
        param_type: continuous
        resolution: 14bit
        suggested_target: pitch
        notes: null

    quirks: []
    notes: null

  another_synth:
    patch_tab: []
    modulation_sources: []
    notes: null
```

### For controllers under active development (`prescriptive` type)

Add a `design_notes` key under your namespace:

```yaml
mappings:
  your_project:
    design_notes:
      target_resolution: 14bit
      target_update_rate: 200      # messages/sec target per continuous source
      open_questions:
        - ""
      development_priorities:
        - ""
```

---

## `param_type` Decision Guide

| What it is | `param_type` | Typical destination |
|---|---|---|
| MIDI channel | `config` | Setup panel |
| MPE zone | `config` | Setup panel |
| Pitch bend range (the setting) | `config` | Setup panel |
| CC number assignment | `config` | Setup panel |
| Breath / CC2 | `continuous` | Modulation source |
| Pitch bend (live) | `continuous` | Modulation source |
| Aftertouch / pressure | `continuous` | Modulation source |
| CC74 / slide | `continuous` | Modulation source |
| Accelerometer | `continuous` | Modulation source |
| Strips / ribbons | `continuous` | Modulation source |
| Note-on velocity | `discrete` | Modulation source / envelope |
| Note-off velocity | `discrete` | Modulation source / envelope |
| Program change | `discrete` | Not typically tracked |
| Clock | `discrete` | Not typically tracked |
