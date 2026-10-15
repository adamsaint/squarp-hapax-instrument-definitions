# Oberheim OB-X8

Use [OB-X8.txt](OB-X8.txt) for polyphonic sequencing and exact NRPN automation values. Its direct NRPN Assign controls have a **reported coarse-step usability problem**. [OB-X8-CC.txt](OB-X8-CC.txt) is a separate CC performance alternative for testing. Both files are source-reviewed and statically checked; full hardware acceptance is pending.

The target is HapaxOS 3.22 and **OB-X8 Main OS 2.0.0.1**, the latest version listed on [Oberheim's support section](https://oberheim.com/products/ob-x8/) when checked on October 14, 2026. The base MIDI table predates OS 2.0. The [OS 2.0 addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf) documents new features but supplies no replacement CC/NRPN address table. Known legacy controls are included; undocumented additions are not guessed.

## Choose a definition

| File | OB-X8 MIDI Param Receive | Use and limits |
| --- | --- | --- |
| [OB-X8.txt](OB-X8.txt) | NRPN | 99 named sound parameters with their documented raw values. Direct Assign editing is too coarse according to user testing; exact event editing remains available. |
| [OB-X8-CC.txt](OB-X8-CC.txt) | CC | 20 named CCs, the same eight assigned controls, and 17 lanes. Knobs use the ordinary 0-127 CC range. Full cutoff/envelope-time coverage needs an endpoint test. |

The CC file is an alternative setup, not an overlay. The OB-X8's receive mode selects how sound parameters are controlled; changing it to CC means the original sound-parameter NRPN lanes are no longer the matching setup. Load the alternative on a fresh track so existing automation does not interfere.

## NRPN setup

1. Copy the raw definition to `HAPAX/OB-X8.txt` on the SD card and load it on an empty track.
2. Choose an actual Hapax MIDI output and channel. For a direct DIN connection, use the connected output A/B/C/D. For a direct USB connection to the synth, use Hapax's USB **Host** port and the appropriate virtual cable. `NULL` in the file preserves the track's existing settings.
3. Set the OB-X8 **MIDI Channel** to the same channel. Avoid `All` for the initial test.
4. Set **MIDI Param Receive = NRPN**, **MIDI Control Enable = On**, and **MPE = Off** on the OB-X8. Begin with a single program, outside Split/Double mode.
5. If using the OB-X8 keyboard through Hapax and returning notes to the synth, set **Local Control = Off**, choose the matching Hapax input, and set **MIDI Out Cable** on the OB-X8 to the connection in use. This prevents the direct keyboard path and returned MIDI path from playing each note twice.
6. For initial output-only testing, **MIDI Param Send = Off** keeps panel edits from generating extra input traffic. For a separate MIDI-monitor capture of the synth's parameter messages, select **NRPN**. This does not enable Hapax to record those incoming messages as NRPN automation.

The names of the MIDI globals and the NRPN preference are specified in the [OB-X8 manual, printed pp. 112-120](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf). Local Control guidance also appears in [Oberheim's troubleshooting guide](https://support.oberheim.com/hc/en-gb/articles/7067213876370-OB-X8-Troubleshooting-Guide).

## NRPN file contents

- **99 NRPN definitions:** oscillator, filter, envelope, LFO, portamento, voice, pan, and arpeggiator controls. Every active entry carries its published raw value range as a comment.
- **Seven performance CC definitions:** mod lever, breath, foot, master volume, expression, sustain, and brightness. Their effect depends on the OB-X8's controller/modulation configuration.
- **Eight assignments** and **17 empty automation lanes**. Additional named controls remain available when adding a lane; the 64-lane limit applies to instantiated lanes, not the number of names in this file.
- **`TYPE POLY`**, routing set to `NULL`, and **`MAXRATE 24`**. The rate is a conservative starting choice for this definition, not an Oberheim requirement.
- No explicit `DEFAULT=` values, preset-name list, or drum configuration.

Press **2ND + FILL** to use the assignments:

| Encoder | Control | Destination | Raw values |
| --- | --- | --- | --- |
| 1 | Filter cutoff | NRPN 22 | 0-175 |
| 2 | Filter resonance | NRPN 23 | 0-127 |
| 3 | Filter envelope amount | NRPN 59 | 0-127 |
| 4 | Main LFO rate | NRPN 29 | 0-127 |
| 5 | Filter attack | NRPN 60 | 0-255 |
| 6 | Filter release | NRPN 66 | 0-255 |
| 7 | Vintage | NRPN 26 | 0-127 |
| 8 | Mod lever | CC 1 | 0-127 |

These are raw MIDI parameter values, not calibrated times or percentages. For example, NRPN 75 uses codes 0-7 for the voice-count setting; its raw value is not the displayed voice count.

## NRPN encoding and editing

The OB-X8 MIDI protocol sends an address using CC99/98, followed by a value using **both CC6 and CC38**. All NRPN entries therefore specify `14`, even switches and parameters with a maximum below 128. This follows the protocol on printed pp. 119-120 of the [manufacturer's MIDI implementation](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf).

For example, cutoff **NRPN 22 = 100**, on MIDI channel 1, should produce these decimal controller/value pairs (status `B0` in hex):

```text
CC99 = 0       address MSB
CC98 = 22      address LSB
CC6  = 0       value MSB
CC38 = 100     value LSB
```

For cutoff 175, the data bytes are `CC6 = 1`, `CC38 = 47`. Do not change `:14` to `:7` to make the encoder range smaller: one-byte data-entry encoding changes the message, not just the editing scale. Do not add independent automation for CC6/38/98/99; those controllers form the NRPN protocol.

**Ranges in comments do not constrain Hapax.** Rechecked against [HapaxOS 3.22](https://squarp.net/hapax/firmware/) and the [2026 template](https://squarp.net/hapax/instr_def/template.txt): there are no documented per-parameter `MIN=`, `MAX=`, step-size, or named-enumeration fields. A 14-bit destination exposes 0-16383, although these OB-X8 controls use only 0-127, 0-175, or 0-255. Setting `DEFAULT=` changes an initial value, not that range; `MAXRATE` changes transmission frequency, not encoder increments.

On October 14, 2026, the user confirmed Assign values of **0, 64, 128** on consecutive clicks and reported the controls unusable. They reported running the latest firmware from Squarp's site, which listed 3.22 on that date; the displayed version and connection type were not independently recorded. For cutoff's 0-175 range, a further 64-unit step would already exceed the documented maximum. A [firsthand community report](https://squarp.community/t/feature-requested-a-min-max-range-for-cc-nrpn-assign/10597/18) describes the same increment on another small-domain 14-bit NRPN synth. The current documentation supplies no verified fine-adjust gesture for Assign. The initial file should not be treated as a tested live-control surface.

On current firmware, select a lane, hold its row button, and turn a left encoder to set a row's exact snap value. Useful custom rows for cutoff are `0, 25, 50, 75, 100, 125, 150, 175`. For a switch use only `0` and `1` and disable interpolation. Row values are configured on Hapax and saved with the project; this `.txt` cannot preconfigure them. Hold an event pad and edit its displayed value for precise individual events. These operations help with programmed automation; they do not change the Assign knobs' range. See [Squarp's automation manual](https://squarp.net/hapax/manual/modeautomation/).

That manual explicitly excludes **incoming NRPN recording**. Use this definition for named destinations, hand-edited automation, and output control. Do not expect turning an OB-X8 panel knob to record a corresponding NRPN lane, or infer that Assign can bypass this recording restriction. CC, pitch-bend, and channel-aftertouch recording are separate supported paths.

`MAXRATE 24` limits each automation lane to 24 updates per quarter note. Multiple NRPN lanes share the same MIDI connection, and each update consists of several CC messages. Start with one or two active lanes and reduce their rates if playback becomes congested. Naming 99 controls does not itself send 99 messages; populated lanes, defaults, and controller actions determine traffic.

## CC performance alternative

Load [OB-X8-CC.txt](OB-X8-CC.txt) on a fresh track, choose the matching port/channel, and set **MIDI Param Receive = CC**, **MIDI Control Enable = On**, and **MPE = Off**. Keep **MIDI Param Send = Off** for the first output-only test. For a later panel-recording test, change Send to CC and configure Hapax's input.

The assignments keep the original control order:

| Encoder | Control | CC |
| --- | --- | --- |
| 1 | Filter cutoff | 33 |
| 2 | Filter resonance | 34 |
| 3 | Filter envelope amount | 31 |
| 4 | Main LFO rate | 51 |
| 5 | Filter attack | 39 |
| 6 | Filter release | 42 |
| 7 | Vintage | 3 |
| 8 | Mod lever | 1 |

These addresses come from the [OB-X8 manual, printed pp. 113-116 (PDF sheets 123-126)](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf). CC34, CC31, CC51, and CC3 have documented 0-127 parameter domains, so they are the clearest first tests. All ordinary CC messages carry values 0-127; this avoids the direct 14-bit Assign range but does not add custom min/max metadata.

**Cutoff and envelope-time coverage is unresolved.** The manual prints cutoff as 0-175 and envelope times as 0-255 even in its CC table, while recommending NRPN for complete parameter coverage. It does not specify a CC scaling formula. Do not assume CC127 reaches NRPN175/255, that values are doubled, or that only the lower portion is reachable. Compare both endpoints on hardware before relying on those controls for full-range sweeps. CC74 is a configurable modulation controller, not a guaranteed substitute for absolute cutoff CC33.

For the first test, turn resonance slowly and confirm successive small CC values and corresponding audible changes. Then compare cutoff CC0/127 against NRPN0/175, and filter attack CC0/127 against NRPN0/255, switching the OB-X8 receive mode for each comparison. Use the same patch and settings and do not save over it. Record both OS versions, connection, outgoing MIDI values, and the observed endpoint behavior. Test recording/replay separately. The CC file has not yet been tested on hardware.

## Other range workarounds

Hapax's [modMatrix](https://squarp.net/hapax/manual/modefx/) can take an ordinary CC as a source, send an NRPN as its destination, and attenuate it with Depth, Offset, Polarity, and Consume. This is a possible on-device route for finer live control while keeping the OB-X8 in NRPN mode. It is not expressible in the documented definition syntax. Exact endpoint scaling, available depth precision, and Assign/recording behavior need a MIDI-output test before publishing a working recipe; this repository does not claim that a guessed percentage is an exact range map.

The Filter effect's CC MIN/MAX selects **controller numbers**, not parameter-value limits. It does not solve this problem. Likewise, Oberheim's supported NRPN increment/decrement messages act on the currently selected parameter; a single normal Assign declaration cannot describe the address-selection and directional message sequence needed for eight independent relative controls.

## Source issues and exclusions

These decisions are based on the [base manual, printed pp. 113-123](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf), cross-checked against its CC table, the [OS 2.0 addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf), and [the 2.0.0.1 changelog](https://oberheim.com/wp-content/uploads/2024/08/OB-X8-2.0.0.1-Changelog.txt).

| Issue | Decision in this revision |
| --- | --- |
| CC12 appears twice, for oscillator cross-modulation and oscillator filter-envelope modulation | Use the separately documented NRPN 9 and 12. Do not invent a corrected CC number. |
| NRPN 56 and 57 both name LFO2 destination Osc2 | Omit 56. Retain the published 57 mapping; the likely Osc1 correction for 56 remains an inference requiring a capture. |
| NRPN 48 Mod2 ramp-up range says 0-27; corresponding CC111 says 0-127 | Omit 48 pending confirmation. Different CC/NRPN scales are possible, so neither table is silently corrected. |
| NRPN 81 arp-speed range says 0-27; CC85 says 0-127; OS 2.0 also adds clock divisions | Omit 81 pending confirmation of its free-running and clocked behavior. |
| NRPN 94 chord key limit range is printed as 0-1207; CC117 says 0-127 | Omit 94 rather than choose an assumed corrected maximum. |
| Several CC ranges exceed the MIDI byte maximum of 127; sustain is inconsistently listed as 0-1 | Keep NRPN for exact raw values. The separate CC file sends only legal 0-127 data and marks cutoff/envelope endpoint coverage unverified. Sustain follows the dedicated received-message threshold of 64. |
| OS 2.0 expands oscillator waveform combinations and filter types | Keep the established NRPN 5/6/24 addresses, with their **base-manual** ranges clearly marked. The new codes are unverified; this is not the complete OS 2.0 enumeration. |
| OS 2.0 adds LFO volume destinations, pressure-to-volume, CC74 assignments, and other expressive settings | No undocumented NRPN addresses are synthesized from Page 2 menu numbers. Configure these on the instrument. |
| NRPN 102-121 edit program-name characters | Omitted from a performance definition. |
| Separate Split/Double controls use addresses above 1023, with some ambiguous upper/lower table entries | Omitted from this single-program definition. Do not assume an address offset borrowed from another Sequential/Oberheim synth. |
| Bank-select descriptions in the base manual are inconsistent, and OS 2.0 expands banks | Leave `[PC]` empty. Capture bank/program messages from the actual instrument before adding favorites. |

The existing [community OB-X8 definition by c.furrow](https://squarp.community/t/instrument-definition-oberheim-ob-x8/10760) (January 22, 2024) independently flags the duplicate CC12. It also embeds port/channel choices and lists CC120-127. This file was authored independently using NRPNs, portable routing, and Hapax's ordinary CC0-119 destination range.

An empty `[PC]` section does not prevent ordinary program changes from Hapax. It simply supplies no favorite names. In a future favorite entry, the definition's program number is **1-128**, while the transmitted MIDI program byte is **0-127**. Bank MSB/LSB values remain 0-127. Verify the synth's bank bytes before populating that section.

## Split, Double, MPE, and poly aftertouch

For OS 2.0 Split operation, the [Oberheim addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf) assigns Lower to the global channel and Upper to the next channel when the global channel is not `All`. A practical test uses channels 1 and 2 on two Hapax tracks. Confirm parameter targeting on each part before treating this as a verified two-part definition. Double mode uses the single global channel; it does not provide the same independent-channel behavior.

OS 2.0 supports MPE, but this definition deliberately selects conventional `POLY`. An MPE setup also requires the OB-X8's MPE setting, a compatible Hapax USB input when using an external MPE controller, matching output-zone configuration, and bend ranges. Those settings are not described by a complete set of definition-file commands in the current template. `AFTR`/`POLYAT` on Hapax is a separate per-note-pressure track type, not an alias for MPE. No MPE or AFTR hardware compatibility claim is made for this revision.

## Hardware acceptance test

The following NRPN checks remain **pending**, apart from the reported Assign usability failure above. A successful lint run does not complete them. Use the separate procedure above for the CC file.

1. Record both devices' displayed OS versions, connection type, MIDI channel, and this file's revision. Load it on a blank POLY track; confirm the track name, 8 assignments, 17 lanes, and readable on-device comments. Check for parser warnings.
2. Play notes and verify note-offs, sustain on/off, pitch bend, and channel pressure with appropriate OB-X8 modulation destinations enabled.
3. With only the cutoff lane populated, enter `0`, `1`, `100`, `127`, `128`, and `175`. Inspect outgoing MIDI with a monitor, especially the CC6/38 transition at 127/128. Confirm that cutoff follows the full range on the synth.
4. Test filter attack at `0`, `127`, `128`, and `255`, and a switch such as Osc Sync at `0` and `1` with interpolation off. These exercise both data bytes and low-range controls.
5. The user confirmed Assign values 0, 64, 128 on consecutive clicks. Record the displayed firmware and repeat for each assigned parameter if investigating a firmware change; do not mark usability successful merely because the synth responds.
6. Verify that a newly loaded blank track introduces no unexpected parameter changes, then test pattern changes, stop, project save/reload, and existing-pattern behavior. The file specifies no defaults, but loading into an already configured track is not a promise to erase old state.
7. Test the intended number of simultaneous lanes at the intended tempo. Include notes and clock traffic if those share the connection.
8. To resolve omitted entries or OS 2.0 codes, capture the OB-X8's outgoing messages while changing only one control. Report the complete CC99/98/6/38 sequence, endpoint values, OS version, and behavior on receipt. Update the definition only after verification.

## Revision history

- **NRPN 0.1.1 / CC 0.1.0, 2026-10-14:** record the user's Assign usability failure; add a focused CC alternative and endpoint test. NRPN addresses and assignments are unchanged; no unsupported range syntax was added.
- **0.1.0, 2026-10-14:** initial documented NRPN performance definition; hardware test pending.
