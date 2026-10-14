# Groove Synthesis 3rd Wave Desktop

These definitions target the **24-voice Desktop Module (24M)** on **OS 2.0d**, with **HapaxOS 3.22**. The 24M has four parts. Its CC map differs from both the 24K keyboard and the two-part 8M; the files are labeled accordingly.

All five files are **source-reviewed and statically checked; hardware tests are pending**.

## Choose a definition

| File | Use | Contents |
| --- | --- | --- |
| [3rd-Wave-Desktop.txt](3rd-Wave-Desktop.txt) | Convenient current Desktop CC controls | 100 CC names, eight Assign controls, 22 automation lanes |
| [3rd-Wave-Desktop-Part1.txt](3rd-Wave-Desktop-Part1.txt) | Direct NRPN control of part 1 | 279 NRPN names, three performance CCs, eight Assign controls, 23 lanes |
| [3rd-Wave-Desktop-Part2.txt](3rd-Wave-Desktop-Part2.txt) | Direct NRPN control of part 2 | Same coverage, with part 2 addresses |
| [3rd-Wave-Desktop-Part3.txt](3rd-Wave-Desktop-Part3.txt) | Direct NRPN control of part 3 | Same coverage, with part 3 addresses |
| [3rd-Wave-Desktop-Part4.txt](3rd-Wave-Desktop-Part4.txt) | Direct NRPN control of part 4 | Same coverage, with part 4 addresses |

Start with the CC file for straightforward encoder control. Use the part files for broader access and explicit part addressing. Load one definition per Hapax track. All files preserve MIDI ports and channels with `NULL`, declare no arbitrary patch defaults, and create empty automation lanes rather than a note pattern.

## Sources and firmware

Checked **October 14, 2026**:

- The manufacturer's [support page](https://groovesynthesis.com/support/) lists **2.0d, released October 13, 2026**, for both 24-voice models. The keyboard and 24M share a firmware download; the 8M has a separate one.
- The [2.0 release notes](https://groovesynthesis.com/firmware/readme_2_0d.txt) document the expanded Desktop CC map and part-addressed NRPN option introduced in 2.0a. The 2.0d change itself concerns 8M SysEx synchronization; 2.0c added editor/librarian SysEx functionality.
- The current [MIDI CC + SysEx Spec v2.0](https://docs.google.com/document/d/1s2u8hfVNfOSYvnMlD52nWcRQJ5-0IUAyDC80ESRla78/edit), updated September 10, 2026, supplies the **24M CC table**, **NRPN Parameters**, value encodings, and global settings. It is linked from the support page and supersedes older CC charts.
- The [Desktop User Manual v1.9](https://groovesynthesis.com/downloads/3WAVE/pdf/3rd%20Wave%20Desktop%20User%20Manual%20v1.9.pdf) remains the linked main manual. Printed pp. 113-115 cover MIDI setup; these are PDF sheets 122-124 when counted from one. Use the newer MIDI specification and release notes for 2.0 additions.

## MIDI setup

1. Connect a Hapax MIDI output to the Desktop's MIDI input, or use Hapax USB Host with the synth's USB MIDI connection. Choose the corresponding **MIDI in cable** setting on the 3rd Wave and output port on Hapax.
2. Set **MIDI MPE mode = Off** for these ordinary polyphonic tracks. MPE takes precedence over the instrument's multitimbral mode.
3. For the CC file, set **MIDI param receive = CC**. In normal MIDI mode, match Hapax's output channel to the synth's **MIDI in channel** and choose the part/edit context on the synth. Several legacy CCs still follow panel selection.
4. For the NRPN part files, select the **NRPN-enabled MIDI param receive** option and set **MIDI include part in NRPN = On**. The current specification calls receive value 2 `CC+NRPN`, while the manual/menu description calls the option `NRPN`; the requirement here is NRPN reception, not exclusive rejection of CCs.
5. To sequence four parts separately, enable **MIDI multitimbral mode** and configure distinct **MIDI multitimbral ch part 1-4** channels. Load the corresponding part file on each Hapax track and match its output channel. For example, channels 1-4 are a layout you can configure, not an assumed factory mapping.

The NRPN address selects the parameter's part; the MIDI channel still matters for receiving the messages, note routing, pitch bend, aftertouch, and the three ordinary performance CCs. Do not expect a Part 2 filename alone to configure the synth's channel globals. Source: [current MIDI specification, GLOBAL MIDI settings and NRPN Parameters](https://docs.google.com/document/d/1s2u8hfVNfOSYvnMlD52nWcRQJ5-0IUAyDC80ESRla78/edit).

For an initial one-way test, leave **MIDI param send = Off**. If panel recording is needed later, configure the return path deliberately: Hapax's [automation manual](https://squarp.net/hapax/manual/modeautomation/) explicitly excludes incoming NRPN recording. These definitions do not read the current patch or synchronize encoder values with it. Clock reception and whether incoming transport starts the 3rd Wave sequencer are separate device settings.

## Desktop CC differences

The current 24M table retains historical rows and appends their replacements. The later, explicitly versioned entries take precedence:

| Controller | Current 24M function | Difference from older or keyboard mappings |
| --- | --- | --- |
| CC9 / 37 / 39 | LPF cutoff / resonance / envelope amount | Replace CC65 / 66 / 67 from before 1.9a |
| CC23 | Amp envelope attack | Keyboard CC23 is Wave Surfer reset |
| CC29 / 30 / 31 | Oscillator 1 / 2 / 3 pitch | The keyboard's oscillator groups use different addresses |
| CC43-46 | Filter ADSR | Replace old Desktop LFO-selection buttons in 2.0a |
| CC52-54 | Amp decay / sustain / release | Direct envelope controls added in 2.0a |
| CC110 / 111 / 112 | Env 4 sustain / FX2 mix / Env 4 amount | Replace old Desktop oscillator-selection buttons in 2.0a |
| CC113 / 114 | Env 4 release / FX1 mix | Current direct controls; keyboard assignments differ |

Source: [MIDI specification, separate 24K and 24M tables](https://docs.google.com/document/d/1s2u8hfVNfOSYvnMlD52nWcRQJ5-0IUAyDC80ESRla78/edit).

The selected-oscillator CC24-28, shared-envelope CC70-73/75-77/118, and selected-effect CC103-107 remain named for completeness. Their meaning depends on panel focus. The Assign controls and pre-created lanes use direct parameter mappings instead. Button destinations are also named, but have no pre-created automation; their pressed/released values describe button events, not necessarily a persistent switch state.

## Encoders

| Encoder | CC definition | NRPN part definitions, base address |
| --- | --- | --- |
| 1 | LPF cutoff, CC9 | LPF cutoff, 283 |
| 2 | LPF resonance, CC37 | LPF resonance, 284 |
| 3 | LPF envelope amount, CC39 | Filter envelope amount, 129 |
| 4 | Wave Surfer, CC22 | Wave Surfer, 396 |
| 5 | Filter attack, CC43 | Filter attack, 117 |
| 6 | Filter release, CC46 | Filter release, 125 |
| 7 | FX1 mix, CC114 | FX1 mix, 323 |
| 8 | FX2 mix, CC111 | FX2 mix, 327 |

## NRPN part addressing and values

With **MIDI include part in NRPN = On**, the documented **Multi** parameters use these offsets:

| Part | Address calculation | LPF cutoff address | Hapax address bytes |
| --- | --- | --- | --- |
| 1 | Base | 283 | `2:27:14` |
| 2 | Base + 1000 | 1283 | `10:3:14` |
| 3 | Base + 2000 | 2283 | `17:107:14` |
| 4 | Base + 3000 | 3283 | `25:83:14` |

Each part file includes 272 usable Multi parameters plus seven **Single** parameters that already have explicit per-part addresses: unison enable, voice count, key mode, detune, legato, poly mode, and Hold. **Do not add 1000 to those Single addresses.** For example, part 2 Unison On is 398. Globals, patch-name characters, edit-selection commands, internal sequence data, and unused FX slots 345-347 are omitted.

All NRPNs use **14-bit data transmission**, including parameters whose meaningful range is only 0-1 or 0-127. The manufacturer's protocol specifies CC99/98 for the address and CC6/38 for the data. For part 2 cutoff = 100, send CC99=10, CC98=3, CC6=0, CC38=100. Data width does not establish the useful parameter range. Source: [NRPN protocol and parameter table](https://docs.google.com/document/d/1s2u8hfVNfOSYvnMlD52nWcRQJ5-0IUAyDC80ESRla78/edit).

Use the raw ranges in the file comments. In particular:

- Envelope and modulation amounts commonly use **0-254, with 127 representing zero**. Raw 128 is +1, not the neutral value.
- Oscillator fine tuning and Part Tune use **0-100, with 50 neutral**. Oscillator pitch uses **0-127, with 64 neutral**.
- Wave Surfer uses **0-126, with 63 neutral**. Wavetable position uses 0-63.
- Oscillator wave selection is packed as **type × 128 + index**: type 0 = P, 1 = U, 2 = analog, 3 = WaveMaker, 4 = sample. Analog indices are 0-6; available P/U/sample indices depend on loaded data. WaveMaker ignores the index.

Hapax definitions cannot constrain these ranges or attach enums. Use fine editing and explicit automation row values, and disable interpolation for selectors and switches. The NRPN files use `MAXRATE 12`; this is per lane, not a shared bandwidth limit. Start with a few active curves and reduce rates if necessary, especially when four parts share DIN MIDI.

## Specification discrepancies and deliberate omissions

The current [MIDI specification](https://docs.google.com/document/d/1s2u8hfVNfOSYvnMlD52nWcRQJ5-0IUAyDC80ESRla78/edit) still contains these inconsistencies. Address coverage is separate from verification of every value:

| Issue | Treatment in these definitions |
| --- | --- |
| CC32 lists banks 0-3, while the manual describes five banks and the SysEx format uses 0-4 | CC32 is named, but no `[PC]` favorites are supplied until the bank-select behavior is confirmed. SysEx bank indices alone do not prove the CC encoding. A full 500-program list would also exceed Hapax's 128-name limit. |
| Shimmer was added in 2.0a; Desktop CC103 and NRPN231 still stop at effect type 12 | Keep the documented selectors and flag the incomplete range. The keyboard table's Shimmer value 13 is evidence to test, not proof of the Desktop/NRPN encoding. Set Shimmer on the panel initially; its documented FX2 parameter 7 at base 466 is included. |
| LFO reset 177-180 lists only 0-1 despite the newer Mono mode | Include the addresses; do not invent the missing mode value. |
| Envelope retrigger 439 lists only 0-1 despite the new fast reset mode | Include the address and document the incomplete enum. |
| Mod-source rows 181-196 say 0-26; the source list extends to 29 | Preserve that discrepancy in comments. The extended list identifies 27 = MPE bend, 28 = MPE Y, 29 = Breath; confirm extended values on hardware. |
| A worked SysEx example calls 254 Oscillator 1 Level; the parameter table calls 254 Oscillator 3 Fine and 255 Oscillator 1 Level | Follow the canonical parameter table, explicitly pending a hardware check of these two addresses. |
| Oscillator 2 Fine at 253 has category `Mutli` | Treat this as the obvious `Multi` spelling error, supported by the adjacent oscillator fine-tuning rows. |

The [2.0 release notes](https://groovesynthesis.com/firmware/readme_2_0d.txt) also describe Shimmer's processing limits: parts 1/3 share one effect core, and parts 2/4 share another. Shimmer and other heavy effects can restrict what the paired part can run. A rejected effect combination is not necessarily an address error.

## Community comparison

The [community Desktop definition for firmware 1.9b](https://squarp.community/t/groove-synthesis-3rd-wave-desktop-fw-1-9b/15552) was reviewed as a historical comparison. Its posted text includes a commented-out `VERSION` line, a program list exceeding the current 128-entry limit, repeated CC37 assignments, and controls that differ from the current Desktop map. Its custom drum rows and patch defaults are not device requirements.

These files were authored from the current manufacturer tables, with original labels and performance assignments. Community work is useful for discovering questions to test, but an older filename alone does not establish compatibility with OS 2.0.

## Hardware acceptance test

All checks remain pending:

1. Load each definition on a fresh Hapax track. Confirm the file appears, the Assign controls are named, and the expected 22 or 23 lanes load. Check access to the full named NRPN list, not just the pre-created lanes.
2. On OS 2.0d, confirm Desktop CC23 changes amp attack, CC43 changes filter attack, and CC110 changes Env 4 sustain. Confirm the independent oscillator controls and both direct effect mixes.
3. With part-addressed NRPN enabled, test cutoff on all four parts and monitor the bytes shown above. Change the synth's selected edit part and verify that the named part remains the target.
4. Play and automate at least two parts on distinct MIDI channels. Confirm note, sustain, pitch-bend, and pressure routing as well as NRPN isolation.
5. For part 2 Filter Amount, address 1129 (`8:105`), test raw 127 and 128. CC6/38 should change from `0/127` to `1/0`; the displayed amount should move from zero to +1.
6. On a saved test patch, verify base 254 changes Oscillator 3 Fine and base 255 changes Oscillator 1 Level. Check packed wave selection with a known loaded wave and test switch values 0/1 using fine editing.
7. Confirm any extended enums or bank selection before relying on them in a set. Record the actual bytes and firmware version when resolving an erratum.
8. Test simultaneous automation load, pattern changes, project save/reload, and the effect-core restrictions if using Shimmer. No arbitrary default values should be introduced by the files.

Revision **0.1.0**, 2026-10-14: current 24M CC definition and four part-addressed NRPN definitions.
