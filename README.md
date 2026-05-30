# Manifold

A structured, community-maintained knowledge base for expressive MIDI controllers.

Manifold documents what controllers *actually send* — not just their marketing copy. Every parameter is classified, every CC assigned a type, every expressive dimension mapped to its MIDI source. The data is human-readable YAML and machine-readable by design, intended to be consumed by synths, tools, and developers building for the expressive music ecosystem.

---

## Why

The expressive controller landscape is fragmented. Wind controllers, MPE surfaces, standalone instruments, AUv3 apps, and CC surfaces all speak different dialects of MIDI. Documentation is scattered across PDFs, forum posts, and companion app tooltips — when it exists at all.

Manifold is a single structured place to answer:

- What does this controller actually send?
- Which of those are performance parameters (continuous, real-time)?
- Which are setup parameters (configured once, before playing)?
- What can be reconfigured, and how?
- Does it support two-way communication?
- Where is it headed — MIDI 2.0, MIDI-CI?

---

## Structure

```
manifold/
├── controllers/
│   ├── wind/           # Breath/embouchure-driven
│   ├── mpe-surface/    # Per-note XYZ via touch
│   ├── expressive/     # Standalone instruments, non-standard paradigms
│   ├── software/       # AUv3, iOS apps, desktop software controllers
│   ├── standard-midi/  # Note + velocity, no per-note expression
│   └── cc-only/        # Pure control surfaces
├── SCHEMA.md           # Full field documentation
├── _template.yaml      # Blank profile to copy
└── tools/              # Validators, exporters (in development)
```

Each controller is a single `.yaml` file following the [schema](SCHEMA.md).

---

## The Core Distinction

Every parameter a controller sends is one of three types:

| Type | Description | Example |
|---|---|---|
| `config` | Set once before playing | MIDI channel, MPE zone, PB range |
| `discrete` | Per-event, not continuous | Velocity, note-on/off |
| `continuous` | Changes while playing | Breath, pressure, pitch bend, slide |

This distinction is first-class in the schema. It's what separates a documentation project from a useful data source — a synth or tool consuming Manifold profiles knows which parameters to track for modulation and which to surface in a setup panel.

---

## Controllers

### Wind / Breath
| Controller | Manufacturer | Status |
|---|---|---|
| [Sylphyo](controllers/wind/sylphyo.yaml) | Aodyo | draft |
| [Zefiro / Zefiro Pro](controllers/wind/zefiro.yaml) | Artinoise | stub |
| [WX11](controllers/wind/wx11.yaml) | Yamaha | stub |
| [EWI-USB](controllers/wind/ewi-usb.yaml) | Akai | stub |
| [re.corder](controllers/wind/re-corder.yaml) | Artinoise | stub |

### MPE Surface
| Controller | Manufacturer | Status |
|---|---|---|
| [Exquis](controllers/mpe-surface/exquis.yaml) | Intuitive Instruments | stub |
| [Sensel Morph](controllers/mpe-surface/sensel-morph.yaml) | Sensel | stub |
| [Lightpad](controllers/mpe-surface/lightpad.yaml) | ROLI | stub |
| [Launchpad Pro](controllers/mpe-surface/launchpad-pro.yaml) | Novation | stub |
| [Launchpad X](controllers/mpe-surface/launchpad-x.yaml) | Novation | stub |

### Expressive Instruments
| Controller | Manufacturer | Status |
|---|---|---|
| [Eigenharp Pico](controllers/expressive/eigenharp-pico.yaml) | Eigenlabs | draft |
| [Orba](controllers/expressive/orba.yaml) | Artiphon | stub |
| [Orba 2](controllers/expressive/orba-2.yaml) | Artiphon | stub |
| [Oddball](controllers/expressive/oddball.yaml) | Oddball | stub |
| [GeoShred](controllers/expressive/geoshred.yaml) | MoForte | stub |

### Software / AUv3
| Controller | Developer | Status |
|---|---|---|
| [DrawnQurve](controllers/software/drawnqurve.yaml) | MTILT | stub |
| [TouchOSC](controllers/software/touchosc.yaml) | Hexler | stub |
| [KB-1](controllers/software/kb1.yaml) | Kai Aras | stub |
| [Velocity Keyboard](controllers/software/velocity-keyboard.yaml) | Blue Mangoo | stub |
| [4Pockets Surface Builder](controllers/software/fourpockets.yaml) | 4Pockets | stub |

### Standard MIDI
| Controller | Manufacturer | Status |
|---|---|---|
| [Keystep](controllers/standard-midi/keystep.yaml) | Arturia | stub |

### CC / Control Surface
| Controller | Manufacturer | Status |
|---|---|---|
| [nanoKONTROL2](controllers/cc-only/nanokontrol.yaml) | Korg | draft |
| [Control Block](controllers/cc-only/control-block.yaml) | ROLI | stub |

---

## Using Manifold Data

Manifold profiles are plain YAML — parse them with any standard library.

The `mappings:` section (optional, namespaced by target) lets synths and tools
embed their own parameter routing on top of the shared factual data:

```yaml
mappings:
  your_synth:
    modulation_sources:
      - id: breath
        source: cc2
        suggested_target: dynamics
    patch_tab:
      - param: mpe_mode
        value: true
```

This keeps the shared factual data clean while allowing any project to layer
its own configuration on top. See [SCHEMA.md](SCHEMA.md) for the full spec.

---

## Roadmap

**Now — Knowledge Base**
Structured profiles, consistent schema, community-verifiable data.

**Next — Tooling**
Schema validator, completeness report, JSON/plist export for consumption by synths and tools.

**Later — Two-Way Communication**
Config file format for controllers that support it. Exquis developer mode (dynamic note layout). Programmatic setup where hardware allows.

**Horizon — MIDI 2.0 / MIDI-CI**
Profile Configuration, Property Exchange, Protocol Negotiation. DrawnQurve as a reference implementation. Document early adopters (WIMSS, others) as case studies.

---

## Contributing

Contributions welcome — especially verified profiles from people with the hardware.
See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

Profile status levels:
| Status | Meaning |
|---|---|
| `stub` | Skeleton only; needs research |
| `draft` | Major fields filled; details pending |
| `complete` | Fully documented |
| `verified` | Tested against hardware/software |

The most valuable contributions are `complete → verified` upgrades with real measurements
(update rates, actual CC ranges, confirmed quirks).

---

## License

Profile data: [CC0 1.0 Universal](LICENSE-data) — no rights reserved.
Tools and code: [MIT](LICENSE-tools)

Data in Manifold should be freely usable, without attribution requirements,
by anyone building for the expressive music ecosystem.
