# Hapax instrument definitions

Documented instrument definitions for the [Squarp Hapax](https://squarp.net/hapax/). Each definition includes its sources, device setup, known limitations, and verification status.

Researched **October 14, 2026** against **[HapaxOS 3.22](https://squarp.net/hapax/firmware/)**, released September 30, 2026, and Squarp's **[2026 definition template](https://squarp.net/hapax/instr_def/template.txt)**. The definition format still uses `VERSION 1`; this is independent of the firmware version.

## Instruments

| Instrument | Definition | Setup and coverage | Status |
| --- | --- | --- | --- |
| Oberheim OB-X8 | [OB-X8.txt](definitions/oberheim/OB-X8.txt) | [OB-X8 guide](definitions/oberheim/README.md) | Assign display issue resolved by reloading; audible CC response under investigation |
| Vermona DRM1 MkIV | [DRM1-MkIV.txt](definitions/vermona/DRM1-MkIV.txt) | [DRM1 MkIV guide](definitions/vermona/README.md) | Source-reviewed; static validation passes; hardware test pending |
| Dreadbox Typhon | [Typhon.txt](definitions/dreadbox/Typhon.txt) | [Typhon guide](definitions/dreadbox/README.md) | Source-reviewed; static validation passes; hardware test pending |
| Nord Drum 3P | [Global kit](definitions/nord/Nord-Drum-3P.txt), [individual voice](definitions/nord/Nord-Drum-3P-Individual.txt) | [Nord Drum 3P guide](definitions/nord/README.md) | Source-reviewed; static validation passes; hardware test pending |
| Groove Synthesis 3rd Wave 24M Desktop | [CC controls](definitions/groove-synthesis/3rd-Wave-Desktop.txt), [NRPN parts 1-4](definitions/groove-synthesis/README.md#choose-a-definition) | [3rd Wave Desktop guide](definitions/groove-synthesis/README.md) | Source-reviewed; static validation passes; hardware test pending |

The OB-X8 definition uses 21 named CCs, eight encoder assignments for pads and evolving textures, and 18 empty automation lanes. Revision 0.2.0 replaces the original NRPN setup after user testing confirmed unusable 64-unit Assign increments. Set the OB-X8 to receive CC parameters and load the new file on a fresh track. Reloading resolved the reported Assign display issue. The user observed CC34 on the intended output and channel in Hapax's monitor, but audible parameter control remains unresolved, including a temporary Master Volume CC7 test. Cutoff and envelope-time endpoint coverage also remains unverified; the guide distinguishes legal MIDI values from confirmed parameter ranges. The target is ordinary polyphonic MIDI operation on OB-X8 OS 2.0.0.1.

The DRM1 MkIV definition provides ten factory-mapped drum articulations, including both open/closed hi-hat pairs. Revision 0.3.0 restores the original order in lanes 1-10, starting with Kick, and keeps `-` labels for spare lanes 11-16. It uses the manufacturer's channel 10 mapping and note velocity for dynamics; the device does not accept MIDI sound-parameter control.

The Typhon definition targets firmware 4.2.1 and its current 98-controller map, with eight encoder assignments, 20 empty automation lanes, and four bank-start favorites. Older Typhon CC charts differ; its guide records the corrected CC32/33 entry and the community provenance of bank selection.

Nord Drum 3P has separate global-kit and individual-voice files for OS 1.24. The global file provides the six factory trigger notes and kit controls; the individual file supports independent voice automation or melodic sequencing. Both use the 14-bit Tone Pitch CC pair.

The 3rd Wave files target the **24M Desktop on OS 2.0d**: one current 100-CC map and four NRPN part definitions with 279 named controls each. The Desktop's CC map differs from the keyboard's. Per-part NRPN operation requires the synth's new **MIDI include part in NRPN** setting; the guide covers routing, raw values, and unresolved manufacturer-table discrepancies.

## Install

1. Download the **raw `.txt` file**, rather than saving a GitHub HTML page.
2. Copy it directly into the SD card's `HAPAX` directory, alongside the project directories. The repository's manufacturer directories are for organization; do not assume Hapax scans them recursively.
3. Insert the card, select a track, then hold **2ND** and press that **track** button. Open **Instrument Definition** using a lower-right encoder and select the file.
4. Set the track's MIDI output port and channel to match the synth. Routing is preserved with `NULL` unless the instrument guide specifies a factory mapping, such as the DRM1's channel 10. Always check the device guide.
5. Apply the device settings in its guide. For the OB-X8, use **MIDI Param Receive = CC**, **MIDI Control Enable = On**, and **MPE = Off**.

These loading steps follow [Squarp's track manual](https://squarp.net/hapax/manual/modetrack/). Start the first hardware check on a new, empty track and save the project after configuring it.

## Format and verification

The [format reference and research findings](docs/instrument-definition-format.md) cover every documented section, current firmware changes, community findings, and gaps in the template.

Static validation checks the documented syntax subset, address/default bounds, duplicate entries, section structure, destination references, and lane limits. It is **not a test with Squarp's parser** and cannot certify how a synth responds. Each instrument guide supplies a hardware acceptance procedure and records unresolved mappings separately.

## Provenance

These files were authored from manufacturer MIDI documentation, with original labels, grouping, and performance choices. Community findings are attributed in the instrument guides, including any mapping based on firsthand reports instead of an official table. Manufacturer manuals and community files are not vendored here. This repository is independent of the instrument manufacturers.
