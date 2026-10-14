# Oberheim OB-X8

Use [OB-X8.txt](OB-X8.txt) for normal polyphonic sequencing and NRPN parameter playback. This first revision is **source-reviewed and statically checked, with hardware verification pending**.

The target is HapaxOS 3.22 and **OB-X8 Main OS 2.0.0.1**, the latest version listed on [Oberheim's support section](https://oberheim.com/products/ob-x8/) when checked on October 14, 2026. The base MIDI table predates OS 2.0. The [OS 2.0 addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf) documents new features but supplies no replacement CC/NRPN address table. Known legacy controls are included; undocumented additions are not guessed.

## Setup

1. Copy the raw definition to `HAPAX/OB-X8.txt` on the SD card and load it on an empty track.
2. Choose an actual Hapax MIDI output and channel. For a direct DIN connection, use the connected output A/B/C/D. For a direct USB connection to the synth, use Hapax's USB **Host** port and the appropriate virtual cable. `NULL` in the file preserves the track's existing settings.
3. Set the OB-X8 **MIDI Channel** to the same channel. Avoid `All` for the initial test.
4. Set **MIDI Param Receive = NRPN**, **MIDI Control Enable = On**, and **MPE = Off** on the OB-X8. Begin with a single program, outside Split/Double mode.
5. If using the OB-X8 keyboard through Hapax and returning notes to the synth, set **Local Control = Off**, choose the matching Hapax input, and set **MIDI Out Cable** on the OB-X8 to the connection in use. This prevents the direct keyboard path and returned MIDI path from playing each note twice.
6. For initial output-only testing, **MIDI Param Send = Off** keeps panel edits from generating extra input traffic. For a separate MIDI-monitor capture of the synth's parameter messages, select **NRPN**. This does not enable Hapax to record those incoming messages as NRPN automation.

The names of the MIDI globals and the NRPN preference are specified in the [OB-X8 manual, printed pp. 112-120](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf). Local Control guidance also appears in [Oberheim's troubleshooting guide](https://support.oberheim.com/hc/en-gb/articles/7067213876370-OB-X8-Troubleshooting-Guide).

## What is included

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

**Ranges in comments do not constrain Hapax.** The published definition grammar has no supported `MIN=`, `MAX=`, or named-enumeration fields. A 14-bit destination exposes a much larger range than these OB-X8 controls use. Watch the numeric value when using Assign; do not sweep its full range. If encoder steps are too coarse, enter exact values in Automation rather than assuming an undocumented fine-adjust gesture.

On current firmware, select a lane, hold its row button, and turn a left encoder to set a row's exact snap value. Useful custom rows for cutoff are `0, 25, 50, 75, 100, 125, 150, 175`. For a switch use only `0` and `1` and disable interpolation. Row values are configured on Hapax and saved with the project; this `.txt` cannot preconfigure them. Hold an event pad and edit its displayed value for precise individual events. See [Squarp's automation manual](https://squarp.net/hapax/manual/modeautomation/).

That manual explicitly excludes **incoming NRPN recording**. Use this definition for named destinations, hand-edited automation, and output control. Do not expect turning an OB-X8 panel knob to record a corresponding NRPN lane, or infer that Assign can bypass this recording restriction. CC, pitch-bend, and channel-aftertouch recording are separate supported paths.

`MAXRATE 24` limits each automation lane to 24 updates per quarter note. Multiple NRPN lanes share the same MIDI connection, and each update consists of several CC messages. Start with one or two active lanes and reduce their rates if playback becomes congested. Naming 99 controls does not itself send 99 messages; populated lanes, defaults, and controller actions determine traffic.

## Source issues and exclusions

These decisions are based on the [base manual, printed pp. 113-123](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf), cross-checked against its CC table, the [OS 2.0 addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf), and [the 2.0.0.1 changelog](https://oberheim.com/wp-content/uploads/2024/08/OB-X8-2.0.0.1-Changelog.txt).

| Issue | Decision in this revision |
| --- | --- |
| CC12 appears twice, for oscillator cross-modulation and oscillator filter-envelope modulation | Use the separately documented NRPN 9 and 12. Do not invent a corrected CC number. |
| NRPN 56 and 57 both name LFO2 destination Osc2 | Omit 56. Retain the published 57 mapping; the likely Osc1 correction for 56 remains an inference requiring a capture. |
| NRPN 48 Mod2 ramp-up range says 0-27; corresponding CC111 says 0-127 | Omit 48 pending confirmation. Different CC/NRPN scales are possible, so neither table is silently corrected. |
| NRPN 81 arp-speed range says 0-27; CC85 says 0-127; OS 2.0 also adds clock divisions | Omit 81 pending confirmation of its free-running and clocked behavior. |
| NRPN 94 chord key limit range is printed as 0-1207; CC117 says 0-127 | Omit 94 rather than choose an assumed corrected maximum. |
| Several CC ranges exceed the MIDI byte maximum of 127; sustain is inconsistently listed as 0-1 | Use NRPN for sound parameters. Performance CCs use 0-127; sustain follows the dedicated received-message threshold of 64. |
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

The following checks remain **pending**. A successful lint run does not complete them.

1. Record both devices' displayed OS versions, connection type, MIDI channel, and this file's revision. Load it on a blank POLY track; confirm the track name, 8 assignments, 17 lanes, and readable on-device comments. Check for parser warnings.
2. Play notes and verify note-offs, sustain on/off, pitch bend, and channel pressure with appropriate OB-X8 modulation destinations enabled.
3. With only the cutoff lane populated, enter `0`, `1`, `100`, `127`, `128`, and `175`. Inspect outgoing MIDI with a monitor, especially the CC6/38 transition at 127/128. Confirm that cutoff follows the full range on the synth.
4. Test filter attack at `0`, `127`, `128`, and `255`, and a switch such as Osc Sync at `0` and `1` with interpolation off. These exercise both data bytes and low-range controls.
5. Check the eight assignments numerically and audibly. Confirm whether the current encoder increments are usable for each range; record any coarse-step limitation.
6. Verify that a newly loaded blank track introduces no unexpected parameter changes, then test pattern changes, stop, project save/reload, and existing-pattern behavior. The file specifies no defaults, but loading into an already configured track is not a promise to erase old state.
7. Test the intended number of simultaneous lanes at the intended tempo. Include notes and clock traffic if those share the connection.
8. To resolve omitted entries or OS 2.0 codes, capture the OB-X8's outgoing messages while changing only one control. Report the complete CC99/98/6/38 sequence, endpoint values, OS version, and behavior on receipt. Update the definition only after verification.

## Revision history

- **0.1.0, 2026-10-14:** initial documented NRPN performance definition; hardware test pending.
