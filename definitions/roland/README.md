# Roland TR-1000

[TR-1000.txt](TR-1000.txt) provides **ten drum voices and TRIGGER OUT**, **66 named CCs**, **eight Assign knobs**, and **24 empty automation lanes** on one Hapax drum track. It targets **HapaxOS 3.22** and **TR-1000 system program 1.22**, using Roland's version **1.20** MIDI chart and reference manual. Status: **source-reviewed and statically checked; hardware test pending**.

## Setup

1. Copy the raw definition to `HAPAX/TR-1000.txt` on Hapax's SD card. Load it on a fresh, empty track through **2ND + track > Instrument Definition**. The manufacturer directory is repository organization; copy the file directly into `HAPAX`.
2. Connect a Hapax MIDI output to the TR-1000's MIDI IN and set the Hapax track's output port to that connection. The file uses `OUTPORT NULL`, so loading it preserves the existing port. Input port and channel are also preserved.
3. Open **MENU > SYSTEM > MIDI** on the TR-1000 and check these settings:

| TR-1000 setting | Required value | Purpose |
| --- | --- | --- |
| MIDI Mode | Single Ch. | Receives the kit's notes on Pattern Ch. |
| Pattern Ch. | 10 | Matches the definition's track and drum-row channels |
| RX Note | ON | Enables incoming drum notes |
| Rx Edit Data | ON | Enables incoming CC controls |

4. Check **Inst Note** against the note tables below, including alternate/layer notes. These assignments can be customized or turned off. Adapt the definition to your settings if necessary; a factory reset is unnecessary.
5. Confirm that the Hapax track and **all 16 drum rows** use channel 10. The file sets both explicitly. If using another channel, change **Pattern Ch.**, the Hapax track channel, and every drum-row channel together. Changing only the track channel leaves the rows sending on 10.
6. Use an empty internal TR-1000 pattern when Hapax supplies the drum notes, so the two sequencers do not play additional hits. The definition does not clear or select a pattern, change TR-1000 settings, or load a kit.

The MIDI menu and receive settings are documented on **printed p. 47** of the [reference manual](https://static.roland.com/assets/media/pdf/TR-1000_reference_eng03_W.pdf). Roland's 1.20 release notes instead describe MIDI MODE as a Project setting. This guide follows the detailed manual and MIDI chart, which both place it under SYSTEM > MIDI; the actual menu location still needs confirmation on hardware.

## Drum rows

These are the **Single Ch. factory note numbers** on page **2** of the [MIDI chart](https://static.roland.com/assets/media/pdf/TR-1000_MIDIImpleChart_eng02_W.pdf). Numeric notes avoid differences in octave names between devices.

| Hapax row | Label | Roland track | MIDI note number |
| --- | --- | --- | --- |
| 1 | Kick | BD | 36 |
| 2 | Snare | SD | 38 |
| 3 | Low Tom | LT | 43 |
| 4 | High Tom | HT | 50 |
| 5 | Rim Shot | RS | 37 |
| 6 | Hand Clap | HC | 39 |
| 7 | Closed HH | CH | 42 |
| 8 | Open HH | OH | 46 |
| 9 | Crash | CC | 49 |
| 10 | Ride | RC | 51 |
| 11 | TRG Out | TRG | 84 |
| 12-16 | `-` | Spare | 0 |

Rows 1-10 follow the panel's track order. Kits can assign different sounds to those tracks; the labels describe their usual roles. Row 11 addresses **TRIGGER OUT**, rather than an eleventh audio voice. To test its output, check **MENU > PROJECT > SOUND > Trig Out = Trig** and monitor the trigger destination; the output behavior is described on **printed p. 49** of the reference manual.

Hapax retains 16 drum slots. Rows 12-16 have dash labels and send **note 0 on channel 10**, which is absent from the factory Single Ch. trigger set. They are **not disabled**. Confirm silence on your unit, especially if an Inst Note assignment was changed to 0. Do not use this spare-note choice in Each Track Ch. mode, where low notes trigger slices.

### Alternate and layer notes

The file installs the normal kit triggers above. Roland also publishes these Single Ch. notes, which can be substituted into rows if needed:

| Roland track | Layer A only note | Layer B and alternate note |
| --- | --- | --- |
| BD | 35 | 99 |
| SD | 40 | 104 |
| LT | 41 | 105 |
| HT | 48 | 112 |
| RS | - | 56 |
| HC | - | 54 |
| CH | - | 44 |
| OH | - | 58 |
| CC | - | 61 |
| RC | - | 63 |

The chart groups the final column as **Layer B and Alt.**; it is not a third layer. There is no alternate TRG note in that table. The complete normal/layer/alternate set has more entries than Hapax's 16 rows, so this definition starts with the normal kit layout. Check customized Inst Note settings before replacing any rows.

## CC controls

All 66 addresses below come from **page 1** of the MIDI chart. They use ordinary **7-bit MIDI data, 0-127**. The chart does not publish conversions to the instrument's displayed units or exact switch thresholds. The definition therefore uses raw values and does not declare parameter defaults, ranges or enum labels.

### Global controls

| Control | CC |
| --- | --- |
| Ext In | 9 |
| Delay Level | 12 |
| Delay Time | 13 |
| Delay Feedback | 14 |
| Master FX On | 15 |
| Master FX Ctrl1 | 16 |
| Master FX Ctrl2 | 17 |
| Master FX Ctrl3 | 18 |
| Analog FX On | 19 |
| Filter | 20 |
| Drive | 21 |
| Morph | 89 |
| Reverb Time | 90 |
| Reverb Level | 91 |

Master FX On and Analog FX On are named for manual selection, but are excluded from the initial Assign and automation choices. Test their value boundaries before sequencing switches. The effects' state and routing must allow the selected control to have an audible effect.

### Layered-track controls

| Parameter | BD CC | SD CC | LT CC | HT CC |
| --- | --- | --- | --- | --- |
| Tune | 22 | 29 | 50 | 57 |
| Decay | 23 | 30 | 51 | 58 |
| Mix | 24 | 31 | 52 | 59 |
| Ctrl1 | 25 | 46 | 53 | 60 |
| Ctrl2 | 26 | 47 | 54 | 61 |
| Ctrl3 | 27 | 48 | 55 | 62 |
| Level | 28 | 49 | 56 | 63 |

The SD map jumps from **CC31 Mix to CC46 Ctrl1**. The intervening controller numbers are not additional sound controls in Roland's chart. The definition keeps each controller independent; adjacent numbers are not evidence of a 14-bit CC pair.

### Other track controls

| Roland track | Tune CC | Decay CC | Ctrl CC | Level CC |
| --- | --- | --- | --- | --- |
| RS | 80 | 81 | 82 | 83 |
| HC | 84 | 85 | 86 | 87 |
| CH | 102 | 103 | 104 | 105 |
| OH | 106 | 107 | 108 | 109 |
| CC | 110 | 111 | 112 | 113 |
| RC | 114 | 115 | 116 | 117 |

The **CC track prefix means Crash Cymbal**: for example, the label `CC Tune` uses controller **110**. CTRL labels remain generic because their function depends on the selected instrument or effect assignment. Selecting a Hapax drum row does not redirect its Assign knobs; each knob sends the fixed controller listed below.

## Assign and automation

| Assign knob | Control | CC |
| --- | --- | --- |
| 1 | BD Tune | 22 |
| 2 | BD Decay | 23 |
| 3 | SD Tune | 29 |
| 4 | SD Decay | 30 |
| 5 | Filter | 20 |
| 6 | Drive | 21 |
| 7 | Delay Level | 12 |
| 8 | Morph | 89 |

The **24 empty automation lanes** cover Tune and Decay for each of the ten drum tracks, then Filter, Drive, Delay Level and Morph. Add any other named controller as a lane when needed. A name alone does not create automation data.

No explicit parameter defaults are sent by this file. Hapax cannot read the current kit's values from this definition; turning a knob or playing automation can move a parameter away from its stored setting. `MAXRATE 24` sets the maximum automation update rate to 24 pulses per quarter note **per lane**, rather than a total bandwidth limit. Reduce lane rates if many simultaneous curves saturate a DIN connection.

## Clock, transport and returned CCs

The definition does not configure clock or transport. For Hapax as the clock source, use **MENU > PROJECT > GENERAL > Tempo Sync = MIDI** for MIDI IN, or **USB** for a USB MIDI connection. **Auto** follows an incoming clock and gives USB precedence when both arrive; **INT** uses the TR-1000's own tempo. These choices are documented on **printed p. 48** of the reference manual.

Set **SYSTEM > MIDI > Rx Start Stop Cont = ON** if Hapax should also start, stop and continue the TR-1000 sequencer. Otherwise leave that reception off for the initial external-note test. Keep the internal pattern empty when transport is linked. Clock routing and any timing adjustment need their own hardware check.

For an initial one-way connection, leave **Tx Edit Data** off. Enable it only when returning panel CC movements to a Hapax MIDI input for recording, and configure that input separately. **Tx Edit Data** controls outgoing CCs; **Rx Edit Data** controls reception. Avoid sending the returned data back around a loop: leave **Soft Through** and **USB MIDI Through** off unless the routing specifically requires them, and check Hapax's MIDI routing. Verify recording and replay before relying on the return path.

## MIDI modes and program changes

**Each Track Ch.**, added in system program 1.20, is a different note/channel layout. Roland's chart assigns BD layers to channels 1-2, SD to 3-4, LT to 5-6 and HT to 7-8, with the remaining tracks on 9-15. Its normal triggers use note 40, layer-specific triggers use note 89 where listed, and notes 0-31 have slice roles depending on the channel. This file's Single Ch. notes and spare rows do not apply to that mode. Use the chart for a separate slice/per-track setup; changing only the track type or channel cannot convert this definition.

The MIDI chart recognizes program-change wire values **0-127**. The factory **Pattern Channel is 10** and **Kit Channel is 1**. A program change on the pattern channel selects a pattern and can also change its kit; one on the kit channel switches only the kit. Project **Kit Select** settings affect pattern-linked kit changes (reference manual, p. 48). Do not change Kit Ch. to 10 merely to match this drum definition.

The file contains no program-change favorites or bank-selection commands. If adding favorites later, Hapax's named PC entries use **1-128**, while MIDI wire values use **0-127**. No NRPN, CC-pair or SysEx setup is included; the reviewed TR-1000 chart supplies ordinary CCs and marks SysEx as unsupported.

## Sources and versions

Checked **October 20, 2026**:

- [Roland MIDI Implementation Chart](https://static.roland.com/assets/media/pdf/TR-1000_MIDIImpleChart_eng02_W.pdf), **version 1.20, April 14, 2026**: page **1** supplies the 66 CCs and program-change support; page **2** supplies Single Ch. factory notes, alternate/layer notes and Each Track Ch. slice mappings. Both pages were rendered and visually checked.
- [Roland Reference Manual](https://static.roland.com/assets/media/pdf/TR-1000_reference_eng03_W.pdf), **version 1.20**: printed p. **47** covers system MIDI settings, p. **48** covers project tempo/kit behavior, and p. **49** covers trigger output. These pages were rendered and visually checked. The manual/chart menu location for MIDI Mode differs from the update-history wording, as recorded under Setup.
- [Roland system program release history](https://www.roland.com/us/support/by_product/tr-1000/updates_drivers/1c5d302a-98c4-49b4-beb6-6d788f4b91fe/): **1.22, September 2026**, is the current listed release. Both 1.21 and 1.22 report no specification changes; the documented MIDI baseline remains 1.20. Check the installed version through **MENU > UTILITY > INFO**. This definition does not install firmware.
- [Hapax release history](https://squarp.net/hapax/firmware/) and [current definition template](https://squarp.net/hapax/instr_def/template.txt): HapaxOS **3.22**, definition **VERSION 1**, 16 drum rows and at most 64 automation lanes. Loading follows [Squarp's track manual](https://squarp.net/hapax/manual/modetrack/).

## Hardware acceptance

1. Record the TR-1000 and Hapax versions, connection, MIDI mode and customized Inst Note settings. Load on a fresh track and confirm `Definition v0-1`, DRUM type, the output port, track channel 10 and all row channels.
2. Play rows 1-10 individually and confirm the matching tracks, including closed/open hi-hat behavior. Test note-on velocities such as 32, 64 and 127 on a kit with velocity-sensitive sounds. Test row 11 separately with an appropriate trigger destination and `Trig Out = Trig`; silence at the audio outputs is expected for that row. Confirm rows 12-16 are silent in Single Ch. mode.
3. Repeat a kick and then a snare while sweeping Assign knobs 1-4. Confirm that Tune and Decay change only the intended track. Exercise knobs 5-8 with analog effects, delay and a prepared Morph target enabled as needed. Confirm CC numbers and channel with a MIDI monitor if available.
4. Check every other named CC against the corresponding panel control. Try raw values 0, 64 and 127 and compare their displayed endpoints. Test CTRL functions with known assignments. Determine Master FX On and Analog FX On thresholds separately before using switch automation; record actual behavior rather than assuming a 64 split.
5. Record a short Hapax Assign curve, play it back, then save and reload the project. Confirm the row map, routing, eight assignments and automation destinations persist. If recording panel movements, repeat with Tx Edit Data enabled and a return connection, checking that each CC appears once and does not loop.
6. If linking transport, test start, stop and continue with an empty internal pattern; verify tempo and timing through the chosen clock path. Check any future program changes separately because they can replace the current pattern or kit.

Revision **0.1.0** is the initial Single Ch. definition. Static checks verify the documented syntax subset, bounds, references and source-map agreement; they do not run Squarp's parser. **Hardware parser acceptance, MIDI response, value scaling and save/reload results remain pending.**
