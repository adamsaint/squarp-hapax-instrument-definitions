# Nord Drum 3P

Two definitions cover the instrument's distinct MIDI workflows:

| File | Use | Contents |
| --- | --- | --- |
| [Nord-Drum-3P.txt](Nord-Drum-3P.txt) | Whole kit on its global MIDI channel | Six drum rows, 34 CC names, one CC pair, eight Assign controls, four kit-effect lanes, eight bank-start favorites |
| [Nord-Drum-3P-Individual.txt](Nord-Drum-3P-Individual.txt) | One voice on its own MIDI channel | 27 CC names, one CC pair, eight Assign controls, 14 automation lanes; ordinary note track for melodic playing |

Target: **HapaxOS 3.22**, **Nord Drum 3P OS 1.24**. Both files are **source-reviewed and statically checked; hardware tests are pending**.

## Current sources

Checked October 14, 2026. Nord's [downloads page](https://www.nordkeyboards.com/products/nord-drum-3p/downloads/) and [release history](https://www.nordkeyboards.com/update-history/drum-3p-update-history/) still identify **1.24, released October 14, 2016**, as the latest instrument OS. The version of Nord Drum 3 Manager is separate. OS 1.24 adds kit banks E-H; the older manual's four-bank description is therefore incomplete.

The [English manual, OS 1.x Edition C](https://www.nordkeyboards.com/wt/documents/97/Nord%20Drum%203P%20English%20User%20Manual%20v1.x%20Edition%20C.pdf) documents the settings on printed p. 12, the two MIDI approaches on p. 14, and all controller addresses on p. 15.

## Global kit setup

1. Connect a Hapax DIN output to Nord MIDI In. Nord Drum 3P has DIN MIDI; it has no direct USB MIDI port.
2. Load the global definition on a fresh track and set that DIN output. The file sets track and lane channels to **10**, Nord's factory global channel.
3. Under Nord's MIDI settings, set the global channel to 10, individual channels to Off for this initial setup, and **CC receive** to `CC.r` or `CC.Sr`.
4. Set **PC receive** to `PC.r` or `PC.Sr` to use the kit favorites.
5. Match the six `nt` note settings below. These are factory numbers, not a command to reset the instrument.

| Hapax row / Nord voice | MIDI note | MIDI channel |
| --- | --- | --- |
| 1 | 60 | 10 |
| 2 | 62 | 10 |
| 3 | 64 | 10 |
| 4 | 65 | 10 |
| 5 | 67 | 10 |
| 6 | 69 | 10 |

The labels are voice numbers because each voice can hold any sound and physical pad layout can change. Unconfigured rows retain the track's other settings. Edit both track and lane channels if using a different global channel. Source: [manual pp. 12-14](https://www.nordkeyboards.com/wt/documents/97/Nord%20Drum%203P%20English%20User%20Manual%20v1.x%20Edition%20C.pdf).

### Voice selection affects parameter control

Global note numbers address all six voices, but most sound-control CCs address **the voice currently selected on Nord**. Merely selecting a different drum row on Hapax does not select a new CC destination on Nord.

Encoder 1 sends CC70 to choose that voice. The remaining encoders control its tone pitch, spectra, tone decay, noise cutoff, noise decay, delay send, and reverb send. Disable Nord's Pad Follow while editing a fixed voice if pad strikes would change the focus.

A [controller owner's MIDI mapping](https://www.elektronauts.com/t/nord-drum-3p-hardware-controller-concept/213652/19) reports these CC70 intervals:

| Nord voice | CC70 interval | Convenient exact value |
| --- | --- | --- |
| 1 | 0-21 | 0 |
| 2 | 22-42 | 32 |
| 3 | 43-63 | 53 |
| 4 | 64-84 | 71 |
| 5 | 85-106 | 96 |
| 6 | 107-127 | 127 |

The [manual's own example](https://www.nordkeyboards.com/wt/documents/97/Nord%20Drum%203P%20English%20User%20Manual%20v1.x%20Edition%20C.pdf) confirms **71 selects voice 4**. The other intervals have community provenance and remain part of the hardware acceptance test.

The global file pre-creates only the four **kit-wide** reverb/delay controls. It does not imply independent sound automation on six rows. Use the individual definition for simultaneous automation on multiple voices; otherwise a shared CC70 focus can send changes to the wrong voice. If deliberately sequencing CC70, use discrete values with interpolation off and ensure selection arrives before the affected parameter messages.

## Individual voice setup

Enable an exclusive individual MIDI channel for each Nord voice you want to control. For example, assign voices 1-6 to channels 1-6 while keeping global channel 10. This is a proposed layout to configure manually, not a factory assumption.

Load the individual definition on one Hapax track per voice, choose the same DIN output, and set each output channel to its corresponding Nord individual channel. There is no per-destination channel field in an automation entry; separate tracks supply that separation.

You can retain the global drum track for all note triggers and use the individual tracks only for parameter automation. Alternatively, put melodic notes on the individual tracks: note numbers then control that voice's tone pitch. Nord advises setting its Tone Pitch to **60** for keyboard-scale correspondence. `TYPE POLY` is Hapax's ordinary note-track mode; each Nord voice remains a single voice. Kit selection and the shared effect controls stay in the global definition.

| Encoder | Individual-voice control |
| --- | --- |
| 1 | Tone Pitch, CC31/63 pair |
| 2 | Tone Spectra, CC30 |
| 3 | Tone Decay, CC50 |
| 4 | Noise Cutoff, CC14 |
| 5 | Noise Decay, CC21 |
| 6 | Dist Amount, CC23 |
| 7 | Delay Send, CC47 |
| 8 | Reverb Send, CC48 |

For pad or panel recording, connect MIDI Out back to Hapax and select the input deliberately. With MIDI echo active, Nord Local Off prevents doubled hits; **Local resets to On at power-up**, so recheck it. With Local Off and no return routing, the panel and pads cannot directly operate the internal engine. These behaviors are documented in the [manual](https://www.nordkeyboards.com/wt/documents/97/Nord%20Drum%203P%20English%20User%20Manual%20v1.x%20Edition%20C.pdf).

## Tone pitch, scaling, and kit names

Tone Pitch uses the documented **CC31 MSB / CC63 LSB** pair. A [user's MIDI capture and successful controller setup](https://forums.steinberg.net/t/building-a-device-panel-but-msb-lsb-are-not-working-as-expected-solved/842874) shows whole semitone values in CC31 and half-semitone steps represented by CC63 = 64. Thus a displayed 60.0 corresponds to raw paired value **7680**, and 60.5 to **7744**. This conversion follows those observed bytes; it is not a claim that every raw value yields distinct audible resolution. Hapax exposes the pair's 0-16383 data range.

Other CCs use 0-127 data even when the panel displays a different scale. Mode, wave, type, and group selections need discrete values. No min/max, enum, pitch quantization, or interpolation settings are embedded in the definition. CC7 is the selected **voice level**, not the unprogrammable master-volume knob.

The eight global favorites name the first kit of A-H using **MSB 0, LSB 0-7, definition PC 1**. A [Nord/Hapax owner's report](https://www.norduserforum.com/viewtopic.php?t=19556) confirms LSB bank selection and subsequent PC; the same user's [controller discussion](https://www.elektronauts.com/t/nord-drum-3p-hardware-controller-concept/213652/20) confirms that bank selection takes effect with a program change. A01 therefore sends CC0=0, CC32=0, then MIDI program byte 0. E-H require OS 1.24. They are empty user banks until populated, so a location label does not promise a factory sound. A full 400-location catalog exceeds Hapax's 128-name limit; add selected favorites if needed.

## Comparison with existing definitions

[Squarp's hosted global example](https://squarp.net/hapax/instr_def/NordDrum3P_global_mode.txt) demonstrates the CC pair and automation-default syntax, but uses a custom channel 11 note map, two extra pitched ghost voices, and an explicit distortion default. Those are performance choices, not Nord's factory configuration. Its bank example also does not establish Nord's bank byte order.

[Ringmod's global and melodic definitions](https://squarp.community/t/instrument-definition-nord-drum-3p/10974) corroborate the factory note map and two-workflow approach. These files were authored from the manufacturer table, with a combined Tone Pitch pair and no arbitrary sound defaults. SysEx kit transfer, firmware updates, and complete device configuration remain separate operations.

## Hardware acceptance test

All checks remain pending:

1. Load both files on fresh tracks; confirm six global rows, the pair destination, and each file's Assign/lane counts.
2. Trigger all six global notes and verify voice LEDs. Test several velocities and check the configured physical pad layout separately.
3. Select each voice with CC70, change Noise Cutoff, and verify that only that voice changes. Check the reported interval boundaries and the manual's value 71.
4. On an individual track, change tone pitch from 7680 to 7744; a MIDI monitor should show CC31=60 and CC63 changing from 0 to 64. Test both sides of a whole-semitone transition.
5. Automate two individual voices at once while the global drum track plays. Confirm independence from the panel's selected voice.
6. Verify all populated bank-start favorites, including a bank in E-H. Save any edits before sending a program change.
7. Test return-path recording, power-cycle Local behavior, project save/reload, and any Nord Mono Group choking used by the kit.

Revision **0.1.0**, 2026-10-14: global factory-kit and individual-voice definitions.
