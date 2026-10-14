# Hapax instrument definition reference

Research date: **2026-10-14**. Target: **HapaxOS 3.22**, the latest public release listed by [Squarp](https://squarp.net/hapax/firmware/). This reference combines the current [2026 template](https://squarp.net/hapax/instr_def/template.txt), official examples and manuals, and community reports. It describes documented behavior and explicitly identifies gaps; it is not a specification extracted from firmware source.

## Firmware and format versions

| Release | Consequence for definitions |
| --- | --- |
| 1.12 | Added CC pairs, `MAXRATE`, extended NRPN address notation, and a 64-lane limit; tightened syntax checking. |
| 1.13 / 1.14 | Fixed CC-pair use in Assign/Automation and CC-pair defaults, respectively. Old bug reports do not establish current limitations. |
| 3.00 | Added `USBD1`-`USBD16` and `USBH1`-`USBH16`; fixed problems importing successive definitions; expanded special-character support without publishing a complete new character whitelist. |
| 3.10 | Expanded drum definitions to 16 rows; added configurable automation row values in the UI. |
| 3.20 | Added `TYPE AFTR` and `TYPE POLYAT`; restored defaults declared in `[CC]`, `[NRPN]`, and `[CC_PAIR]`, which were ignored in 3.00/3.10. |
| 3.22 | Current target; principally AFTR fixes. The file header remains `VERSION 1`. |

Source: [official release history](https://squarp.net/hapax/firmware/). A syntax target is not a hardware-test result or a proven minimum firmware requirement.

## File and lexical rules

Use a UTF-8 plain-text file ending in `.txt`. Place the file directly in the SD card's `HAPAX` directory. Loading is through **2ND + track > Instrument Definition**. Ordinary `#` comments are ignored from that character to the end of the line. A `[COMMENT]` section supplies information that can be viewed on the device by holding the Instrument Definition encoder. See [the track manual](https://squarp.net/hapax/manual/modetrack/).

Write `VERSION 1` as the first active line. Use uppercase command/section keywords, one command per line, and matching opening/closing section tags. Names follow a space; numeric subfields use colons. Do not enclose names in quotes. An empty section can be omitted. Most track settings can be omitted or set to `NULL` to preserve existing state; `NULL` is not interchangeable with disabling something using `NONE`.

For portable files, this repository uses LF newlines, no BOM, spaces rather than tabs, and the template's conservative name alphabet: letters, digits, spaces, `_`, `-`, `+`. Newer firmware accepts some additional punctuation, but the published template does not fully specify it. Short names are a usability convention, not an invented firmware length limit. Normal `#` comments may contain source URLs and other explanatory punctuation.

The local validation used for these files enforces this conservative style. It may reject legacy shorthand or punctuation that a particular firmware parser accepts.

## Track commands

All rows below describe active lines outside sections. Values are from the [current template](https://squarp.net/hapax/instr_def/template.txt).

| Command | Values / meaning |
| --- | --- |
| `VERSION` | `1`; format version, not a synth or Hapax firmware number |
| `TRACKNAME` | A name using the supported alphabet, or `NULL` |
| `TYPE` | `POLY`, `DRUM`, `MPE`, `AFTR`, `POLYAT`, `NULL`; the last two non-null values name the same per-note-aftertouch type |
| `OUTPORT` | `A`, `B`, `C`, `D`, `USBD`, `USBH`, `USBD1`-`USBD16`, `USBH1`-`USBH16`, `CVG1`-`CVG4`, `CV1`-`CV4`, `G1`-`G4`, `NULL` |
| `OUTCHAN` | `1`-`16` or `NULL`; ignored for a non-MIDI output |
| `INPORT` | `NONE`, `ALLACTIVE`, `A`, `B`, `USBD`, `USBH`, their numbered USB variants, `CVG`, `NULL` |
| `INCHAN` | `1`-`16`, `ALL`, `NULL`; ignored for `NONE`, `ALLACTIVE`, and `CVG` inputs |
| `MAXRATE` | `NULL`, `192`, `96`, `64`, `48`, `32`, `24`, `16`, `12`, `8`, `6`, `4`, `3`, `2`, `1` |

`MAXRATE` sets the default maximum automation update rate in pulses per quarter note, per lane. It is not milliseconds and not a global bandwidth cap. At 120 BPM, a setting of 24 permits at most 48 updates per second per lane. NRPN and CC-pair updates use multiple MIDI controller messages. [Squarp's automation chapter](https://squarp.net/hapax/manual/modeautomation/) explains the rate control.

`ALLACTIVE` listens when the track is selected. A specified input port/channel can listen regardless of track selection. An MPE track requires compatible input/output configuration; selecting `TYPE MPE` is not a complete MPE setup. The 3.20 notes explicitly exclude MIDI DIN as an MPE **input**. Do not turn that into a claim that every MPE output must be USB.

## Sections and exact entry shapes

### Drum lanes

```text
[DRUMLANES]
1:NULL:NULL:36 Kick
2:NULL:NULL:38 Snare
[/DRUMLANES]
```

Entry shape: `ROW:TRIG:CHAN:NOTENUMBER NAME`.

| Field | Range |
| --- | --- |
| `ROW` | 1-16 on current firmware |
| `TRIG` | 0-127 or `NULL` |
| `CHAN` | 1-16, `G1`-`G4`, `CV1`-`CV4`, `CVG1`-`CVG4`, or `NULL` |
| `NOTENUMBER` | 0-127 or `NULL` |
| `NAME` | Supported name or `NULL` |

The section is applied to DRUM tracks. `TRIG` and output note are separate fields: do not compress the four-field structure just because two fields are unused. The current template gives `TRIG`'s accepted range but little semantic explanation; preserve it with `NULL` unless the intended triggering behavior has been established. Old examples with eight rows are valid examples, not the current maximum. Source: [2026 template](https://squarp.net/hapax/instr_def/template.txt).

### Program names

```text
[PC]
1 First Program
9:NULL:2 Bank2 Program9
[/PC]
```

Shapes: `PC NAME` or `PC:MSB:LSB NAME`. **PC is 1-128 in the definition**, while its MIDI wire value is 0-127. Bank bytes are 0-127 or `NULL`; a null bank byte is left unspecified. There may be at most **128 named entries in total**, including entries from different banks. The same PC with different banks can identify different entries.

This names favorite programs; it is not an arbitrary SysEx preset loader and does not itself specify pattern program-change timing. Obtain bank order and values from the instrument's implementation, not its displayed bank labels or another synth's conventions. Source: [template](https://squarp.net/hapax/instr_def/template.txt).

### CC names

```text
[CC]
1 Mod Wheel
74:DEFAULT=64 Brightness
[/CC]
```

Shapes: `CC NAME` or `CC:DEFAULT=value NAME`. Ordinary Hapax destinations use CC numbers **0-119**, with data values **0-127**. CC120-127 are channel-mode messages, not ordinary automation destinations. Defining a name does not automatically create a lane. A synth's display scale does not change the 7-bit MIDI payload. Source: [template's destination constraints](https://squarp.net/hapax/instr_def/template.txt) and [firmware fixes for CC values above 119](https://squarp.net/hapax/firmware/).

### CC pairs

```text
[CC_PAIR]
20:52 Timbre
21:53:DEFAULT=8192 Layer
[/CC_PAIR]
```

Shapes: `CC_MSB:CC_LSB NAME` or `CC_MSB:CC_LSB:DEFAULT=value NAME`. A pair's value is 0-16383. These are **controller numbers**, not an NRPN parameter address. Consult the synth before pairing controllers; do not assume every device implements every conventional MSB/LSB pair. The [official Nord example](https://squarp.net/hapax/instr_def/NordDrum3P_global_mode.txt) contains a concrete pair declaration.

### NRPN names

```text
[NRPN]
8:2:14 Fine Tune
0:2000:14:DEFAULT=100 Aux Control
[/NRPN]
```

Shape: `MSB:LSB:DEPTH[:DEFAULT=value] NAME`. Normal address bytes are 0-127. If the first field is `0` or empty, the second field can instead contain the complete 0-16383 address. Thus `8:2:14`, `0:1026:14`, and `:1026:14` name the same address. For maximum clarity this repository uses explicit byte pairs in Assign/Automation references.

Convert full address `n` using `MSB = n // 128`, `LSB = n % 128`. Depth is **7 or 14**, describing the parameter's **data transmission**, not its address size. Legal defaults are 0-127 or 0-16383, respectively. Confirm which data-entry bytes the instrument requires; a raw parameter range of 0-1 or 0-127 does not by itself prove 7-bit transmission is correct.

Do not create multiple conflicting entries for one address. The [official template](https://squarp.net/hapax/instr_def/template.txt) documents the alternate address forms; [Squarp's developer discussion](https://squarp.community/t/determining-nrpns-and-automation-ccs-for-instrument-definitions/8279) explains their origin. Suggestions in that thread such as underscores around full addresses or `NAMED=` were proposals, not the adopted grammar.

### Assign

```text
[ASSIGN]
1 CC:74
2 NRPN:8:2:14
3 CC_PAIR:20:52
4 CV:1 DEFAULT=1.7V
5 PB
6 AT
7 NULL
[/ASSIGN]
```

Shape: `POT DESTINATION [DEFAULT=value]`, with a **space** before `DEFAULT=`, unlike the colon used in name declarations. Pot numbers are 1-8; unspecified pots are treated as `NULL` by the template. Destination forms:

| Type | Destination shape | Default range |
| --- | --- | --- |
| CC | `CC:0` through `CC:119` | 0-127 |
| NRPN | `NRPN:MSB:LSB:7` or `NRPN:MSB:LSB:14` | 0-127 / 0-16383 |
| CC pair | `CC_PAIR:MSB_CC:LSB_CC` | 0-16383 |
| Pitch bend | `PB` | Template says defaults are ignored in Assign |
| Channel aftertouch | `AT` | Template says defaults are ignored in Assign |
| CV | `CV:1` through `CV:4` | 0-65535, or voltage from `-5V` to `5V` |
| Unassigned | `NULL` | None |

The template says values after `PB`/`AT` are ignored; omit them. `AT` is channel aftertouch, not a per-note-pressure address. The template inconsistently lists a pitch-bend default range and then says PB/AT defaults are ignored; do not depend on an Assign default for either. The **CC_PAIR destination is missing from its type list**, but is supported: the [1.13 release notes](https://squarp.net/hapax/firmware/) describe the fix, and [a user's FH-2 report](https://squarp.community/t/cc-pairs-instrument-defs/11506) demonstrates the syntax in practice.

### Automation

```text
[AUTOMATION]
CC:74
NRPN:8:2:14
CC_PAIR:20:52
PB
AT
CV:1
[/AUTOMATION]
```

Each line creates a lane using the same destination shapes, without a pot number or label. The supported documented set is CC, NRPN, CC_PAIR, PB, AT, CV. There is a **64-lane maximum per track**. More named CC/NRPN parameters can exist than instantiated lanes.

An optional space-separated default is demonstrated by the [official Nord file](https://squarp.net/hapax/instr_def/NordDrum3P_global_mode.txt): for example, `CC:74 DEFAULT=64`. The template's abbreviated Automation grammar omits this option. The 3.20 release notes also explicitly acknowledge this syntax. The repository linter checks the corresponding data-width bounds, but does not simulate firmware default precedence.

The hardware UI exposes other destinations, including effects, velocity, and program change. Their presence in the UI is not evidence for invented `FX:`, `VELOCITY:`, or `PC:` lines in an instrument definition. Add such syntax only when supported by authoritative documentation or a verified hardware example.

Defaults are active behavior: an empty lane can send its default after pattern changes and stop operations. After 3.20, defaults in naming sections are honored again. Avoid arbitrary init-patch values in reusable instrument definitions. Name declarations, encoder assignment, lane creation, and MIDI transmission are distinct steps. [Automation manual](https://squarp.net/hapax/manual/modeautomation/).

### On-device information

```text
[COMMENT]
Device Name
Set Param Receive NRPN
Set MIDI channel manually
[/COMMENT]
```

This is displayed information, unlike ignored `#` comments. Use short plain lines. Put longer sources, value explanations, and unresolved issues in the adjacent README. Source: [template](https://squarp.net/hapax/instr_def/template.txt).

## Limitations that affect real instruments

- **No documented range or enum fields:** `MIN=`, `MAX=`, `RANGE=`, and `NAMED=` are not in the current grammar. Requested syntax in a forum is not implemented syntax. Raw values can exceed the synth's meaningful domain even when they fit the MIDI data width.
- **UI row values are a workaround, not file metadata:** current firmware can set exact values for automation rows. Those settings and interpolation choices must be configured on Hapax; no corresponding definition commands are documented.
- **Incoming NRPN recording is explicitly unsupported** in the current automation manual. A named output destination does not add a decoder for incoming parameter changes. Do not promise panel-knob recording, automatic patch synchronization, or bidirectional state feedback.
- **Definitions cannot invent MIDI capabilities:** a note-triggered analog drum instrument may offer no remotely controllable sound parameters. Physical knobs alone are not evidence of CC/NRPN support.
- **No documented arbitrary SysEx section:** firmware updates, preset dumps, and global configuration procedures are separate from definitions.
- **No complete project or track preset:** there are no documented commands here for every MPE zone, scale, effect rack, default velocity, interpolation choice, program-change pre-send setting, or note pattern.

Sources: [current template](https://squarp.net/hapax/instr_def/template.txt), [automation manual](https://squarp.net/hapax/manual/modeautomation/), and the firsthand [range feature request](https://squarp.community/t/feature-requested-a-min-max-range-for-cc-nrpn-assign/10597). The request also records practical coarse-step problems with small-domain 14-bit NRPNs. Its proposed range syntax remains absent from the current official template.

## Community review and tools

| Resource reviewed | Useful finding / how to use it |
| --- | --- |
| [Squarp definition category](https://squarp.community/c/hapax/instrument-definitions/21) | Locate model-specific files and hardware reports; check dates and revisions. |
| [endlesscoil/hapax-instruments](https://github.com/endlesscoil/hapax-instruments) | Manufacturer-organized collection; contributor guidance removes personal routing. Existing files still need device/firmware checks. |
| [NRPN syntax discussion](https://squarp.community/t/determining-nrpns-and-automation-ccs-for-instrument-definitions/8279) | Direct developer confirmation of the extended-LSB notation; separates adopted syntax from proposals. |
| [CC pairs and definitions](https://squarp.community/t/cc-pairs-instrument-defs/11506) | Working CC_PAIR destination syntax for Assign/Automation. |
| [Older syntax-help discussion](https://squarp.community/t/instrument-definition-syntax-help/8598) | Historical CC-pair/default bugs. One reply reverses the byte-order description; the official order is MSB then LSB. |
| [Range request](https://squarp.community/t/feature-requested-a-min-max-range-for-cc-nrpn-assign/10597) | Real editing limitations; proposed `MIN`/`MAX` fields are not proof of support. |
| [OB-X8 definition](https://squarp.community/t/instrument-definition-oberheim-ob-x8/10760) | Author flags duplicate CC12 in Oberheim's table; a useful corroboration of a source defect. |
| [Digitone II definition discussion](https://squarp.community/t/elektron-digitone-ii-fm-drum/12106) | Illustrates why lint errors, partial drum rows, and corrected MIDI mappings must be evaluated separately. Example fixes can themselves contain placeholder values. |

Squarp's [track chapter](https://squarp.net/hapax/manual/modetrack/) links these community tools: [px-xp's editor](https://px-xp.github.io/track-definitions/), [Gamahachaa's tools](https://gamahachaa.codeberg.page/), and [Max Hodges' tools](https://hapax-tools.maxhodges.com/). The [px-xp source repository](https://github.com/px-xp/track-definitions) is also available. These are useful authoring aids, not a replacement for the current template or a hardware test. This review does not certify each tool's current output against 3.22.

## Repeatable authoring procedure

1. Verify the current public Hapax firmware and download-template version. Record the date and relevant changes.
2. Identify the exact instrument model, hardware revision, installed/target OS, and connection mode. Find the manufacturer's current manual, dedicated MIDI implementation, firmware notes, and addenda.
3. Build a source ledger: each note/controller/address, value range, data width, receive mode, and any layer/bank behavior needs an identifiable source. Page 2 menu indices and patch-file byte offsets are not automatically NRPN addresses.
4. Compare community files and first-person captures with those sources. Preserve provenance, distinguish corroborated corrections from guesses, and explicitly omit unresolved controls.
5. Choose the suitable track type, conservative names, portable routing, useful assignments, and a modest lane set. Add exact ranges as comments when the format cannot express them.
6. Check syntax, address bounds, duplicate entries, references, and lane limits. Independently compare addresses and ranges with the source; a grammar linter cannot detect a plausible but incorrect mapping.
7. Test parser acceptance and actual MIDI behavior on the devices. Exercise minimum/maximum values, 127-to-128 transitions, switches, defaults, multiple patterns, and save/reload where relevant. Record tested versions and connection type.

Keep **source-reviewed**, **statically checked**, and **hardware-verified** as separate evidence states. Revisit definitions after firmware changes, especially when globals, bank layouts, or parameter value encodings change.
