# Erica Synths PĒRKONS HD-01

[Perkons-HD-01.txt](Perkons-HD-01.txt) provides **four drum voices**, **44 named CCs**, **eight Assign knobs**, and **32 empty automation lanes**. It targets **HapaxOS 3.22** and **PĒRKONS HD-01 firmware 1.2.2**, in **single-channel MIDI mode with the published CC map**. Status: **source-reviewed and statically checked; hardware test pending**.

## Setup

1. Copy the raw definition to `HAPAX/Perkons-HD-01.txt` on Hapax's SD card. Load it on a previously unused track through **2ND + track > Instrument Definition**. Set the output port and channel for the PĒRKONS connection; `NULL` preserves routing rather than choosing it.
2. On PĒRKONS, hold **SHIFT** and press **MOD** to enter configuration. Select **MIDI MODE SINGLE** with step **1 of the third row**. Open **SINGLE CHANNEL MIDI CONFIG** with step **16 of the first row**, and select the same MIDI channel as Hapax. The [firmware 1.2 configuration sheet](https://www.ericasynths.lv/service/file/download/product_id/1167/file_id/527/) shows these positions.
3. Check that **all four active Hapax drum lanes** send on that channel. The lane channel fields are also `NULL`, so loading the file does not correct previously assigned per-lane channels.
4. Check that PĒRKONS uses the CC table below. Its SD-card file `SYSTEM/single-midi-cc.json` can replace the assignments. Preserve a copy before editing a custom map; a factory reset is unnecessary.
5. Begin with an empty internal PĒRKONS pattern so that it does not double Hapax's notes. Configure clock/transport separately if desired. For Hapax as clock source, the PĒRKONS configuration's first-row step **2** selects MIDI clock.
6. Enable **MIDI VELOCITY** (third-row step **8**) if you want incoming velocities to affect the voices. **MIDI CC OUT** (third-row step **7**) controls transmission from the synth; it is not a CC-receive enable switch. Enable it only when you need panel movements sent back for recording, and avoid a MIDI feedback loop.

These controls are documented on printed pp. 31-36 of the [December 2024 manual](https://www.ericasynths.lv/service/file/download/product_id/1167/file_id/521/). The definition does not set the instrument's configuration or modify its SD-card map.

## Drum rows

| Hapax row | Label | MIDI note number |
| --- | --- | --- |
| 1 | Voice 1 | 36 |
| 2 | Voice 2 | 37 |
| 3 | Voice 3 | 38 |
| 4 | Voice 4 | 39 |
| 5-16 | `-` | 40 |

The official table names the triggers C, C-sharp, D and D-sharp without an octave number. The numeric notes **36-39** are corroborated by a [Hapax owner's drum-lane example](https://squarp.community/t/erica-synths-perkons/15818/2). Numeric notes avoid differences in octave naming between devices. Voice labels remain generic because algorithms and modes can change their roles.

The active voices occupy the lower-numbered rows, which matches the initial drum view reported during this project's DRM1 testing. Hapax retains all 16 rows. The spare rows have dash labels and send **note 40**, outside the documented trigger set, to avoid retaining old note assignments. They are **not disabled**: confirm silence on your unit. This choice applies only to the single-channel trigger map; it must not be carried into chromatic multi-channel operation.

## Controls

All entries use ordinary **7-bit CC data, 0-127**. The manual does not publish physical-unit conversions or switch thresholds. The definition labels the three selectors but does not invent enumerated ranges or put them in the initial Assign/automation selection.

| Parameter | Voice 1 CC | Voice 2 CC | Voice 3 CC | Voice 4 CC |
| --- | --- | --- | --- | --- |
| Tune | 70 | 81 | 92 | 103 |
| Decay | 71 | 82 | 93 | 104 |
| Param1 | 72 | 83 | 94 | 105 |
| Param2 | 73 | 84 | 95 | 106 |
| Cutoff | 74 | 85 | 96 | 107 |
| Drive | 75 | 86 | 97 | 108 |
| FX Send | 76 | 87 | 98 | 109 |
| Level | 77 | 88 | 99 | 110 |
| Algorithm | 78 | 89 | 100 | 111 |
| Mode | 79 | 90 | 101 | 112 |
| Filter Mode | 80 | 91 | 102 | 113 |

This reproduces the addresses on **printed p. 35** of the [manual](https://www.ericasynths.lv/service/file/download/product_id/1167/file_id/521/). Its p. 36 screenshot illustrates an editable map and contains different assignments; it is not used to override the preceding implementation table. Check the actual SD-card map if a knob reaches an unexpected destination.

The Assign layout puts the same function across all four voices:

| Position | Knob 1 | Knob 2 | Knob 3 | Knob 4 |
| --- | --- | --- | --- | --- |
| Top row, Assign 1-4 | V1 Cutoff, CC74 | V2 Cutoff, CC85 | V3 Cutoff, CC96 | V4 Cutoff, CC107 |
| Bottom row, Assign 5-8 | V1 Decay, CC71 | V2 Decay, CC82 | V3 Decay, CC93 | V4 Decay, CC104 |

All **eight continuous controls per voice** have empty automation lanes. Param1 and Param2 change function with algorithm/mode; the file retains their generic names. FX Send adjusts each voice's send, not the global delay settings. No CC addresses for global delay time, feedback or master compression, or a program/bank protocol, were established in the reviewed implementation, so they are omitted.

No parameter defaults are declared. A new Assign value can differ from the current kit's stored value, and the definition cannot read that value back. `MAXRATE 24` limits automation updates per lane; reduce rates when running many simultaneous curves over DIN.

## Mode and firmware caveats

**Multi-channel mode uses CC70-80 for every voice, distinguished by MIDI channel.** The voice 2-4 offsets above are specifically for single-channel mode. Use this file as a single drum-kit track; chromatic, independently channeled voice sequencing needs a different track configuration and map. The manual's firmware 1.1 section describes chromatic operation in multi-channel mode.

A [February 2025 Hapax owner report](https://squarp.community/t/perkons-01-and-hapax/13674) describes problems automating the filter-mode switch and says Erica acknowledged them. That is an owner's account, without an identified firmware version or a manufacturer reply in the thread. It predates 1.2.2 and does not establish a current bug or a confirmed fix. Test selectors independently before adding their automation to a performance.

## Sources and versions

Checked **October 15, 2026**:

- [Official product downloads](https://www.ericasynths.lv/perkons-hd-01-black-2520/) list firmware **1.2.2, October 6, 2025**, described as minor bug fixes. They link the **December 20, 2024** English manual and the firmware **1.2 configuration sheet**. The definition is for the HD-01, not the separate Perkons Voice module or Perkons Keys.
- [English manual](https://www.ericasynths.lv/service/file/download/product_id/1167/file_id/521/): printed pp. **31-33** for setup, **34** for firmware 1.1 behavior, **35** for MIDI, **36** for custom CC maps. The MIDI table and customization screenshot were checked visually.
- [Configuration sheet](https://www.ericasynths.lv/service/file/download/product_id/1167/file_id/527/): an image showing the firmware 1.2 settings grid, checked visually.
- [Community definition and drum-lane addition](https://squarp.community/t/erica-synths-perkons/15818): the December 2025 CC listing agrees with the official table; the August 2026 reply supplies numeric trigger notes. These corroborate mappings, not complete hardware acceptance of this file.
- [Hapax firmware](https://squarp.net/hapax/firmware/) and [current template](https://squarp.net/hapax/instr_def/template.txt): HapaxOS **3.22**, definition `VERSION 1`.

## Hardware acceptance

1. Record both firmware versions and the connection. Load on a fresh track; check the `Definition v0-1` comments, DRUM type, output and four lane channels.
2. Trigger rows 1-4 separately. Confirm one matching voice per row, then verify that rows 5-16 are silent. Test different velocities with velocity reception enabled.
3. Sustain or repeat one voice at a time and sweep its cutoff and decay. Check all eight Assign knobs, including that they do not affect a different voice.
4. Check each of the other continuous CCs against its panel control. Test Param1/2 with known algorithms. Test Algorithm, Mode and Filter Mode separately, recording their actual value boundaries before sequencing switches.
5. Record and replay a short automation curve; then save/reload the Hapax project. If recording from the panel, also test PĒRKONS CC output with MIDI echo avoided.

Revision **0.1.0** is the initial single-channel definition. Hardware results remain pending.
