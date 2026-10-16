# Erica Synths Steampipe

[Steampipe.txt](Steampipe.txt) provides **32 named continuous controls**, **eight Assign knobs**, and **32 empty automation lanes** for **HapaxOS 3.22** and **Steampipe firmware 1.1**. It uses a **project-defined custom CC map that must be entered on the synth**. Loading the Hapax file does not configure Steampipe. Status: **control functions and mapping procedure source-reviewed; static validation passes; setup and audible response untested on hardware**.

## Configure the custom map

1. Record your existing Steampipe CC assignments before changing them. Existing controller templates and recorded CC automation will need the same new numbers. The manual does not establish that a preset backup includes the CC map, so keep a separate record.
2. On Steampipe, open **Settings > MIDI**. Set the input channel for the connection you use (**DIN and USB have separate channel settings**), turn on **CC receive** for that port, and turn **MPE off** for this ordinary polyphonic definition. Select Poly voice mode for chords.
3. Open **Settings > MIDI CC Mapping**. The left encoder selects a parameter and the right encoder changes its CC number. Enter the worksheet below. The addresses are project choices, not a recovered factory map. The documented mapping procedure is on **PDF sheet 16** of the [official manual](https://www.ericasynths.lv/service/file/download/product_id/1434/file_id/566/).
4. Reserve **CC20-31, CC40-53 and CC80-85** for these controls. Check the rest of the mapping for conflicts: move or unassign another parameter already using a reserved number before reusing it. Also check **MIDI Device** sources **A/B/C/D**; they should not use these numbers, or a knob could both change a parameter and apply modulation. Common performance sources such as CC1, CC2 and CC11 remain free.
5. Copy the raw definition to `HAPAX/Steampipe.txt`. Load it on a previously unused Hapax track through **2ND + track > Instrument Definition**. Set its output port and channel to match Steampipe. The file preserves routing with `NULL` and selects `TYPE POLY`.
6. Test the eight Assign controls first, then the remaining mapped controls. Confirm the map persists after leaving Settings and, after saving any sound edits, after a restart. No automatic map-import or map-saving command is assumed by the definition.

You can initially configure just the **eight rows marked with an Assign number** to test the performance page. Configure all 32 before using the remaining automation lanes. Empty lanes have no declared defaults, so the file does not intentionally initialize their values.

### Mapping worksheet

The destination column identifies **panel sections and control labels**, as documented on PDF sheets **7-9**. These are not promised to be the exact strings or row indices in the CC-mapping menu. For example, the resonator is called **Delay Box** on the panel, while its Hapax labels use **Pipe**. Confirm the corresponding function instead of assigning entries by menu order.

| CC | Steampipe destination | Hapax label | Assign knob |
| --- | --- | --- | --- |
| 20 | Envelope: Attack | Env Attack | 5 |
| 21 | Envelope: Decay | Env Decay | |
| 22 | Envelope: Sustain | Env Sustain | |
| 23 | Envelope: Release | Env Release | |
| 24 | Envelope: Keytrack, envelope-time tracking | Env Keytrack | |
| 25 | Envelope: Velocity, envelope-level sensitivity | Env Velocity | |
| 26 | Generator: DC/Noise | Gen DC Noise | |
| 27 | Generator: Cutoff | Gen Cutoff | |
| 28 | Generator: Resonance | Gen Resonance | |
| 29 | Generator: Keytrack | Gen Keytrack | |
| 30 | Generator: Env. Amt | Gen Env Amt | |
| 31 | Generator: Gain | Gen Gain | |
| 40 | Feedback: Push | FB Push | 2 |
| 41 | Feedback: Decay | FB Decay | |
| 42 | Feedback: Release | FB Release | 6 |
| 43 | Saturation: Soft/Hard | Sat Soft Hard | 4 |
| 44 | Saturation: Symmetry | Sat Symmetry | |
| 45 | Patch: Volume, before the reverberator | Patch Volume | |
| 46 | Delay Box: Tune | Pipe Tune | |
| 47 | Delay Box: Fine | Pipe Fine | |
| 48 | Delay Box: Harmonics | Pipe Harmonics | 3 |
| 49 | Delay Box: Splitpoint | Pipe Splitpoint | |
| 50 | Filter: Highpass | Pipe Highpass | |
| 51 | Filter: Lowpass | Pipe Lowpass | 1 |
| 52 | Voice: Drift | Voice Drift | |
| 53 | Voice: Glide T | Glide Time | |
| 80 | Reverberator: Spread | Reverb Spread | |
| 81 | Reverberator: Mix | Reverb Mix | 7 |
| 82 | Reverberator: Bass | Reverb Bass | |
| 83 | Reverberator: Treble | Reverb Treble | |
| 84 | Reverberator: Size | Reverb Size | 8 |
| 85 | Reverberator: Spin | Reverb Spin | |

The manual's mapping screenshot shows an **Envelope Scaling** entry without explaining its correspondence to the panel's separate Keytrack and Velocity controls. Do not assign both controls to that one entry. Identify each by its function and verify the result; the definition's labels deliberately keep them separate.

All entries are independent **7-bit CCs with values 0-127**, including numbers that other MIDI devices might use as fine-controller bytes. There are no `[CC_PAIR]` destinations. The synth determines the conversion to seconds, cents, percentages or other units; that conversion is not published for these assignments. Hapax's definition grammar cannot supply custom minimums, maximums, step sizes or conversion curves.

## Performance page

| Assign | Control | Role |
| --- | --- | --- |
| 1 | Pipe Lowpass, CC51 | Resonator brightness |
| 2 | FB Push, CC40 | Strength of the excitation/feedback interaction |
| 3 | Pipe Harmonics, CC48 | Resonator character and harmonic spacing |
| 4 | Sat Soft Hard, CC43 | Feedback clipping character |
| 5 | Env Attack, CC20 | Excitation onset |
| 6 | FB Release, CC42 | Feedback decay after note release |
| 7 | Reverb Mix, CC81 | Dry/wet balance |
| 8 | Reverb Size, CC84 | Reverberation scale and tail |

Knobs 1-4 occupy the top row; 5-8 occupy the bottom. This selection favors sustained tones and evolving textures. **Generator Cutoff** and **Pipe Lowpass** are separate filters. **Envelope Release** controls the excitation envelope; **FB Release** controls the resonator's feedback after note-off. Their effects depend on the current patch. These distinctions follow the [panel descriptions in the manual](https://www.ericasynths.lv/service/file/download/product_id/1434/file_id/566/).

All 32 named controls have empty automation lanes. No defaults are supplied, and the definition does not retrieve patch values, so the first movement may jump from the current sound to the Hapax value. `MAXRATE 24` sets a per-lane automation rate; reduce it for dense simultaneous automation over DIN.

## Coverage and limitations

This is a continuous-control map. Switches, voice-mode selection, LFO selection/routing and individual LFO parameters, engine settings, master volume, program changes, bank selection and SysEx are not configured. The manual does not enumerate the complete mapping-menu destinations or their value encodings. Those omitted controls are not being declared unsupported by the synth.

**Reverb Spread** in this file is the panel control. Firmware 1.1's **Voice Spread** is a separate engine setting and is not mapped here. The [1.1 announcement](https://www.ericasynths.lv/news/steampipe-11-update-out) also extends LFO speed to **0.01-50 Hz**; it does not publish CC assignments for the new setting.

Set **Split** on the panel when checking Splitpoint, and **Glide** on when checking Glide Time. Generator Resonance has no effect in the generator's one-pole mode. These dependencies are described in the panel-control pages; an inaudible change is not by itself evidence that a CC failed.

MIDI Device sources A/B/C/D and the preset modulation matrix form a separate expressive-control system. They can route one incoming source to several destinations with per-preset depths. This file addresses individual parameters through **Settings > MIDI CC Mapping**. It does not build a modulation matrix or an MPE configuration. In particular, Erica's [MPE setup advice](https://www.ericasynths.lv/support/technical-support/steampipe/not-working-with-osmose), including source A at CC74, is not a factory parameter-address chart.

## Why a custom map

The [July 2025 manual](https://www.ericasynths.lv/service/file/download/product_id/1434/file_id/566/) says Steampipe ships with a factory map and explains how to edit it. Its screenshot shows Envelope Decay at 71, Sustain at 72, Release at 73 and Scaling at 74; Attack is shown in Learn mode without a number. It does not provide the rest of the factory table. Neither an adjacent menu index nor a neighboring CC number establishes the missing addresses.

The search covered official downloads/support, 112dB, Squarp's community and definition collection, Elektronauts, and public controller-map repositories. No complete, verified factory map or broadly adopted alternative was established. An [independent MIDI-definition audit](https://subsystem.co/supplemental/definitions/midi/instruments/erica_synths/steampipe) also leaves its CC list unspecified because of this gap. That is corroboration of the documentation limitation, not a manufacturer specification or proof that no other map exists.

An [Elektronauts discussion](https://www.elektronauts.com/t/erica-synths-112db-steampipe/225117?page=6) includes a request for a map and a Hapax user's CC-recording problem later attributed to pot catch. It supplies no complete mapping table. Those reports predate firmware 1.1; they do not establish a current firmware defect. If panel movement does not transmit during recording, check CC transmission and the pot's catch position before changing addresses.

## Sources and versions

Checked **October 15, 2026**:

- [Official product/downloads page](https://www.ericasynths.lv/steampipe-3153/): latest public firmware **1.1, July 22, 2025**; English manual listed **July 15, 2025**. Later preset-pack dates are not firmware releases.
- [User Manual V3, July 2025](https://www.ericasynths.lv/service/file/download/product_id/1434/file_id/566/): **PDF sheets 7-9** for panel controls; **11** for engine settings; **12-13** for expressive sources and modulation; **14** for LFOs; **15** for the MIDI monitor and port settings; **16** for CC mapping and pot catch. The PDF has no printed page numbers. Panel and mapping images were checked visually.
- [Firmware 1.1 announcement](https://www.ericasynths.lv/news/steampipe-11-update-out): Voice Spread, extended LFO speed, and minor fixes.
- [Hapax firmware](https://squarp.net/hapax/firmware/) and [definition template](https://squarp.net/hapax/instr_def/template.txt): HapaxOS **3.22**, definition `VERSION 1`.

The **parameter identities and setup procedure** come from Erica's manual. **Every CC address in this file** is an author-selected convention, to be configured and verified on the instrument.

## Hardware acceptance

1. Record firmware versions and port/channel. Configure the map, load a fresh Hapax track, and check the `Custom CC map - setup required` comments and the eight assignments.
2. Confirm notes play. While sustaining a suitable patch, move Pipe Lowpass through several values. Steampipe's **MIDI Monitor** shows its last five received CC messages: verify **CC51** and changing values at the receiver, then verify the intended audible change. Repeat for all eight Assign knobs.
3. Check the remaining 24 controls individually. Pay particular attention to Envelope Keytrack versus Velocity, both filters, both release controls, and Reverb Spread versus Voice Spread. Confirm that no unrelated control moves because of a duplicate assignment or A/B/C/D source.
4. Check endpoints and intermediate values on the synth; do not infer a linear parameter scale from 0-127. Use a working sound and note the panel prerequisites for controls such as Splitpoint and Glide Time.
5. For panel recording, enable **CC transmit** on the connected return port, route Hapax's input correctly, and avoid echoing received CCs back to the synth. Confirm pot catch permits the control to move. Record and play back one curve before recording several together.
6. Save/reload the Hapax project and verify that the Steampipe map survives a restart after preserving unsaved sound edits. Note the results before treating the setup as performance-tested.

Revision **0.1.0** is the initial custom map. Hardware configuration, value coverage and audible operation remain pending.
