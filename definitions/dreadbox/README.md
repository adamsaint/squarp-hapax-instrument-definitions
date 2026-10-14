# Dreadbox Typhon

[Typhon.txt](Typhon.txt) provides **98 named CCs**, **eight encoder assignments**, **20 empty automation lanes**, and **four bank-start program favorites**. It targets **HapaxOS 3.22** and **Typhon 4.2.1**. Status: **source-reviewed and statically checked; hardware test pending**. The bank bytes have older firsthand community confirmation; they still need verification on 4.2.1.

## Sources and version

Checked October 14, 2026:

- [Dreadbox support downloads](https://dreadbox-fx.com/support/) list Typhon updater **4.2.1**, alongside the older 4.2 packages. Preset Manager 4.2 is a separate application version.
- The [4.2.1 release announcement](https://dreadbox-fx.com/typhon-update-v4-2-1/) is dated **July 6, 2026** and describes MIDI-port stability and screensaver fixes. It does not announce another controller remapping.
- The [current 2026 manual](https://dreadbox-fx.com/wp-content/uploads/2026/04/Typhon-Users-Manual_2026.pdf) supplies the **4.2-marked CC table on printed pp. 23-24** and current settings on **p. 20**. Both pages of the CC table were checked visually, including its continuation on p. 24.
- [Two owners' bank-selection reports](https://squarp.community/t/some-def-files-not-working-in-new-firmware/10585) establish the zero-based bank MSBs and a working Hapax entry shape. This supplements a detail missing from the current manual.

## Setup

1. Copy the raw file to `HAPAX/Typhon.txt` and load it on an empty track. Set Hapax's output port and channel to match the connection. The definition preserves routing with `NULL`.
2. In Typhon's **Menu > Global settings > MIDI channels**, set **Input** to that channel. In **Port Configuration**, enable the connected MIDI or USB input.
3. Set **MIDI CC > CC IN = Enable** and **CC Handling = Overriding** for absolute automation. Additive handling changes how values affect the current sound.
4. Set **MIDI PC > PC IN = Enable** if using the bank-start favorites. The current settings table lists independent CC and PC input switches; older prose later in the same PDF still describes a combined switch.
5. Leave Typhon's internal sequencer stopped for the initial external-note test. Its sequence controls in this definition affect Typhon's own sequence, and incoming notes can transpose that sequence while it is running.
6. To sync modulators or delay, select an external clock and configure the relevant Sync options. Set Hapax's clock/transport output deliberately if you also want to start the internal sequencer; these settings are not encoded by the file.

Typhon is monophonic. `TYPE POLY` selects Hapax's ordinary note-track mode; it does not make the synth polyphonic. Use monophonic patterns or Hapax's note-editing options as appropriate.

For a later panel-recording test, connect Typhon's output to Hapax, select that input on the track, and enable Typhon **CC OUT**. Start with a one-way output connection for the initial check, then verify the return path and MIDI-thru settings. No automatic patch-state synchronization is provided. Mod wheel, CC2, velocity, and aftertouch depend on the patch's target assignments in Typhon's menu. Pitch-wheel range is also configured there. Source: [manual settings and implementation](https://dreadbox-fx.com/wp-content/uploads/2026/04/Typhon-Users-Manual_2026.pdf).

## Assign controls and coverage

| Encoder | Control | CC |
| --- | --- | --- |
| 1 | Filter cutoff | 74 |
| 2 | Resonance | 71 |
| 3 | Oscillator waveshape | 70 |
| 4 | Oscillator 2 offset | 77 |
| 5 | Filter envelope amount | 6 |
| 6 | Glide | 5 |
| 7 | FX2 mix | 91 |
| 8 | FX3 mix | 97 |

All ordinary controllers in the current table are named. The 20 pre-created lanes cover the principal sound controls, three FX mixes, mod wheel, CC2, sustain, pitch bend, and channel aftertouch. Other named controls remain available for additional lanes. `MAXRATE 24` limits each lane's update rate; it is not a total traffic limit. No preset-specific defaults are declared.

CC values are seven-bit MIDI data, **0-127**. The manual does not specify every selector threshold or the conversion to displayed units. In particular, mode/type selection, legato, glide mode, sequencer length, and divisions should be checked at exact values before sequencing them. Disable interpolation for discrete changes in Hapax's UI. The text format does not encode enums or device-specific value bounds.

The three modulator blocks retain generic Param1-3 labels because their meanings change with the selected modulator mode. Their pitch destinations correspond to the manual's CV/CV1/CV2 targets inside Typhon; they do not address Hapax's physical CV outputs. FX parameter labels are likewise generic where algorithms change their meaning. Custom modulation amounts use the targets already selected in the patch. See the [manual's modulation and effect chapters](https://dreadbox-fx.com/wp-content/uploads/2026/04/Typhon-Users-Manual_2026.pdf).

## Bank-start favorites

The four entries select the first slot of banks A-D. They are location labels, not factory patch names:

| Favorite | Bank MSB | Bank LSB | Definition PC | MIDI program byte |
| --- | --- | --- | --- | --- |
| Bank A 01 | 0 | Unspecified | 1 | 0 |
| Bank B 01 | 1 | Unspecified | 1 | 0 |
| Bank C 01 | 2 | Unspecified | 1 | 0 |
| Bank D 01 | 3 | Unspecified | 1 | 0 |

These values follow the [firsthand Hapax tests](https://squarp.community/t/some-def-files-not-working-in-new-firmware/10585), which corrected an earlier one-based bank description. A program entry must include its name; an unlabeled `1:0:NULL` line is incomplete syntax. Select and send a favorite explicitly when testing.

Typhon has 256 preset locations, while a Hapax definition supports 128 named program entries in total. This file includes only four useful starting points. Add your own favorites within that limit once bank behavior has been confirmed on the installed firmware. Program changes replace the active sound; test with saved edits.

## Audited differences and omissions

- **Legacy CC files:** the [original community thread](https://squarp.community/t/dreadbox-typhon-instrument-definition-file/7716) begins with an older mapping. Its controller numbers must not be assumed interchangeable with the current 4.2 table.
- **CC32:** the [July 2026 community file](https://squarp.community/t/dreadbox-typhon-july-2026/16280) lists M2 Param3 twice, at 32 and 33. The current manual leaves 32 unused and assigns that parameter to **33**. Only 33 is included here.
- **CC71 label:** the MIDI table says VCO Resonance, although resonance belongs to the filter in the control description. This file uses the neutral label Resonance and preserves controller 71; the naming correction is an interpretation of the manual, not a different address.
- **CC120 and CC123:** Typhon documents All Sounds Off and All Notes Off, but these are channel-mode messages outside Hapax's ordinary 0-119 CC destination range. They are omitted from the CC list.
- **NRPN, CC pairs, and SysEx:** no such sound-control mapping is documented in the current manual. No addresses were invented for them.

## Hardware acceptance test

All checks below remain pending:

1. Load the file in HapaxOS 3.22 and verify all eight Assign labels, 20 lanes, and four program favorites.
2. With Typhon's sequencer stopped, play an external note. Test cutoff CC74 and resonance CC71, then all eight assignments. Confirm they change the intended controls on 4.2.1.
3. Check M2 Param3 using **CC33**, each modulator's three custom amounts, and the FX3 controls **CC93-98**. Select suitable algorithms so the tested parameters have audible effects.
4. Test sustain at 0/127, pitch bend, and aftertouch with a configured destination. Verify discrete controls separately with interpolation disabled.
5. On saved presets, send each bank-start favorite and verify A01/B01/C01/D01. Record any required PC/CC receive settings and the MIDI capture.
6. If using Typhon's sequencer, test CC99-102 while it runs and check clock/transport behavior. These lanes are intentionally not pre-created.
7. Record a short cutoff gesture through the return connection, then save/reload the Hapax project and replay it. Verify that the routing does not feed changes back repeatedly.

Revision **0.1.0**, 2026-10-14: initial current-map definition.
