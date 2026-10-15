# Oberheim OB-X8

Use [OB-X8.txt](OB-X8.txt) for polyphonic sequencing and CC performance control on Hapax. **Revision 0.2.0 replaces the original NRPN definition** with eight knobs chosen for pads, sustained chords, and evolving textures. The separate CC filename has been retired.

The target is HapaxOS 3.22 and **OB-X8 Main OS 2.0.0.1**, the latest versions listed by [Squarp](https://squarp.net/hapax/firmware/) and [Oberheim](https://oberheim.com/products/ob-x8/) when checked on October 14, 2026. The file is **source-reviewed and statically checked**. The user reports CC controls working after loading onto a fresh track; full endpoint, recording, and persistence checks remain pending.

## Install and setup

1. Download the raw [OB-X8.txt](OB-X8.txt) and replace `HAPAX/OB-X8.txt` on the SD card. If you copied the temporary `OB-X8-CC.txt` file, it can be removed to avoid duplicate choices.
2. Load the definition on a **fresh, previously unused POLY track**, rather than selecting an empty pattern on the old track. Existing NRPN automation is not converted to CC by loading this file or changing the synth's receive mode. See the migration check below if the old Assign layout remains visible.
3. Set Hapax's MIDI output port and channel to match the OB-X8. Routing fields are `NULL`, so the file preserves existing settings. Use Hapax's USB Host port for a direct USB synth connection, or the connected DIN output.
4. Set **MIDI Param Receive = CC**, **MIDI Control Enable = On**, and **MPE = Off** on the OB-X8. Begin with a single program outside Split/Double mode.
5. For output-only testing, set **MIDI Param Send = Off**. To record the OB-X8's panel movements later, set Send to CC and configure Hapax's input and recording. Send and Receive are independent settings.
6. If the OB-X8 keyboard is routed through Hapax and returned to the synth, set **Local Control = Off** to avoid doubled notes. Choose the appropriate OB-X8 MIDI Out Cable when using that return path.

These globals are documented in the [OB-X8 manual, printed pp. 17-18 and 112-120](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf). Local Control guidance also appears in [Oberheim's troubleshooting guide](https://support.oberheim.com/hc/en-gb/articles/7067213876370-OB-X8-Troubleshooting-Guide).

### Migration check: old Assign controls or unreadable comments

On October 14, 2026, the user reported loading the CC file onto a track that previously used the NRPN definition. Assign still showed values such as 2688 and 3392, numeric parameter labels, and Mod Lever in slot 8; the definition comments were reportedly unreadable. Loading the same definition onto an unused track made the controls appear to work correctly. This isolates the observed problem to the existing-track migration path, but does not establish the underlying firmware cause or a general rule that reloading definitions always fails.

Keep the old track intact and explicitly load `OB-X8.txt` through **2ND + track > Instrument Definition** on an unused POLY track. Restore the intended output port and channel. In **2ND + FILL**, the new layout has **LFO1 Depth1 / CC53 in slot 8**, and all eight controls use **0-127** values. The comment popup should begin with `Oberheim OB-X8 CC` and `Definition v0-2`; full comment-popup verification was not separately reported after the successful fresh-track load.

Assignments belong to the track, so choosing another pattern on that same track does not provide an unused set of assignments. See [Squarp's Assign instructions](https://squarp.net/hapax/manual/). Replacing the SD file alone also does not update saved assignments; a [community report on definition reloads](https://squarp.community/t/instrument-definitions-force-to-reload-recharge/10393) describes the need to load updated files explicitly. No syntax or MIDI mapping change was required for the reported fresh-track success.

## Performance controls

Press **2ND + FILL** to use the assignments.

| Encoder | Control | CC | Performance role |
| --- | --- | --- | --- |
| 1 | Filter cutoff | 33 | Brightness |
| 2 | Filter resonance | 34 | Harmonic emphasis |
| 3 | Filter envelope amount | 31 | Intensity of the filter bloom |
| 4 | Filter attack | 39 | Speed of the filter bloom |
| 5 | Amp attack | 45 | Chord fade-in |
| 6 | Amp release | 48 | Tails and overlapping chords |
| 7 | Vintage | 3 | Variation between voices |
| 8 | LFO 1 depth 1 | 53 | Amount of movement |

The layout follows the user's preference for pads and evolving textures. For knob 8 to introduce slow filter movement, configure a slow LFO on the OB-X8 and enable **Filter** in LFO Destination 1. Other enabled destinations will also respond to Depth 1; disable pitch destinations if pitch movement is unwanted. The definition names the control but does not configure the patch's routing. See the [manual's modulation section, printed pp. 31-33](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf).

The file provides **21 named CCs**, **eight assignments**, and **18 empty automation lanes**. Both filter and amp ADSR controls, LFO rate/depth, Vintage, mod lever, sustain, pitch bend, and channel aftertouch have lanes. Breath, foot, master volume, expression, and brightness are also named. This is a focused performance selection, not a conversion of all 99 NRPN entries in the original file.

There are no explicit defaults, program-name favorites, or drum mappings. `MAXRATE 24` sets each automation lane's maximum update rate to 24 per quarter note; it does not set an encoder range or step size.

## Range audit

**Every mapped CC sends a legal value from 0 to 127.** Hapax's documented definition syntax supplies no custom minimum, maximum, or encoder-step field. The OB-X8 interprets those values; a valid MIDI value alone does not establish full parameter coverage.

| Mapped controls | CC numbers | Range status |
| --- | --- | --- |
| Vintage, master volume, filter envelope amount, resonance, filter/amp sustain, LFO rate, LFO depth 1 | 3, 7, 31, 34, 41, 47, 51, 53 | Published parameter domains are 0-127, matching ordinary CC values. Hardware response remains to be checked. |
| Filter cutoff | 33 | Published parameter domain is 0-175. CC endpoint scaling is unspecified. |
| Filter attack/decay/release and amp attack/decay/release | 39, 40, 42, 45, 46, 48 | Published parameter domains are 0-255. CC endpoint scaling is unspecified. |
| Mod lever, breath, foot, expression, brightness | 1, 2, 4, 11, 74 | Ordinary 0-127 performance-controller data. Audible response depends on the instrument's controller settings and patch routing. |
| Sustain pedal | 64 | 0-63 is off; 64-127 is on. Use 0 and 127 and turn interpolation off for this lane. |

The table accounts for all 21 named CCs. Pitch bend and channel aftertouch use their own MIDI message formats and are not CC range mappings.

Sources: [OB-X8 MIDI implementation, printed pp. 113-116, PDF sheets 123-126](https://oberheim.com/wp-content/uploads/2023/09/OB-X8-Manual-V2_2.pdf), [OS 2.0 addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf), and [Hapax's current definition template](https://squarp.net/hapax/instr_def/template.txt).

**Cutoff and envelope-time coverage is unresolved.** The manual prints wider parameter ranges even in its CC table and recommends NRPN for complete parameter coverage. It does not supply a CC scaling formula. Do not assume CC127 reaches NRPN175/255, values are doubled, or only the lower portion is reachable. CC74 is a configurable modulation controller, not a guaranteed substitute for absolute cutoff CC33.

Switches and selectors with smaller domains are not added as general-purpose performance knobs. Changing Receive to CC does not automatically constrain every other OB-X8 parameter, nor turn an existing NRPN destination into its CC counterpart.

## Why the definition changed

The original file used 14-bit NRPN encoding to address the manufacturer's raw values. On October 14, 2026, the user confirmed Hapax Assign values of **0, 64, 128** on consecutive clicks and found the controls unusable. They reported running the latest firmware from Squarp's site, which listed 3.22; the displayed version and connection type were not independently recorded. A [firsthand community report](https://squarp.community/t/feature-requested-a-min-max-range-for-cc-nrpn-assign/10597/18) describes the same problem on another small-domain NRPN synth.

The underlying OB-X8 NRPN encoding uses CC99/98 for the parameter address and CC6/38 for its value. Changing a definition's depth from 14 to 7 would change the encoding, not correctly scale it. The CC setup avoids the full 14-bit Assign editing range. The earlier NRPN definitions remain in Git history; there is no second active OB-X8 definition.

For exact NRPN playback, Hapax can still use manually configured lanes and exact event values. Its row snap values aid automation editing but do not rescale Assign knobs. Incoming NRPN recording is explicitly unsupported in the [automation manual](https://squarp.net/hapax/manual/modeautomation/). Its modMatrix supports attenuation from a CC source to an NRPN destination, but a precise working range recipe needs hardware verification and cannot be encoded in the published definition syntax.

## Source issues and exclusions

The base MIDI table predates OS 2.0. The [OS 2.0 addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf) and [2.0.0.1 changelog](https://oberheim.com/wp-content/uploads/2024/08/OB-X8-2.0.0.1-Changelog.txt) do not provide a complete replacement address table. Undocumented additions are not guessed.

- CC12 is listed twice for different functions. It is omitted. The [community OB-X8 definition](https://squarp.community/t/instrument-definition-oberheim-ob-x8/10760) independently notes the duplicate.
- CC1 is a mod-wheel controller in the received-message chart, while another table labels it LFO2 bend amount with an impossible 0-255 CC range. The file uses the performance-controller interpretation and does not promise a 256-value parameter mapping.
- CC74 behavior has OS 2.0 patch-routing options. Its label is Brightness, not absolute cutoff.
- The original NRPN research found unresolved entries 48, 56, 81, and 94, plus incomplete OS 2.0 waveform/filter enumerations. These remain unsuitable for silently inferred mappings.
- Bank descriptions are inconsistent across the base manual and newer banks. No favorite bank/program mapping is supplied; ordinary program changes are still possible.
- Split/Double-specific parameter targeting, program-name editing, and complete OS 2.0 parameter coverage are outside this performance file.

## Split, Double, MPE, and poly aftertouch

Use single-program POLY mode for the first test. In OS 2.0 Split mode, Lower uses the global channel and Upper the next channel when the global channel is not All. Double uses the global channel. Confirm parameter targeting before treating either as verified multipart control.

This file does not configure MPE zones or per-note aftertouch. Hapax's AFTR/POLYAT track type is separate from MPE. See the [Oberheim OS 2.0 addendum](https://oberheim.com/wp-content/uploads/2024/07/OB-X8-OS2.0-Addendum.pdf) for the synth's expressive-control settings.

## Hardware acceptance test

**Basic CC Assign operation on a fresh track is user-reported as working.** The migration check above records the preceding failure on an existing NRPN track. The complete checks below, including full endpoint coverage, remain pending; the report does not establish that every controller, recording path, or save/reload case passed. Static validation alone does not test Squarp's parser or the synth's response.

1. Record both displayed OS versions, connection type, channel, and definition revision. Load on a fresh track; confirm eight assignments, 18 lanes, and no parser warnings.
2. Verify notes, note-offs, sustain, pitch bend, and pressure with suitable patch routing.
3. Turn resonance slowly and verify small successive CC values and audible changes. Check Vintage, envelope amount, sustain levels, LFO rate, and LFO depth. Configure the LFO's destinations before evaluating its depth control.
4. Compare cutoff CC0/127 against NRPN0/175, and envelope-time CC0/127 against NRPN0/255, switching Receive mode for each comparison and returning to CC afterwards. Use exact NRPN event values rather than the coarse Assign knobs. Keep the patch otherwise identical and record endpoint differences.
5. Check all eight knobs on sustained chords and overlapping releases. Verify whether amp and filter settings allow the intended tails to remain audible.
6. Test automation recording/replay, stop, pattern changes, and project save/reload. Set sustain interpolation off. No explicit file defaults are supplied, but existing project state can still transmit.
7. Test the intended combination of notes, clock, and simultaneous automation lanes. Reduce lane update rates if the connection becomes congested.
8. Report complete MIDI captures and observed endpoint behavior before promoting any unresolved range to hardware-verified.

## Revision history

- **0.2.0, 2026-10-14:** replace the canonical `OB-X8.txt` with CC control, apply the agreed pad layout, add LFO depth, audit all 21 CC ranges, and retire the temporary CC filename. Load on a fresh track; old NRPN automation is not converted.
- **NRPN 0.1.1 / temporary CC 0.1.0, 2026-10-14:** record the user's 64-unit Assign steps and add a focused CC alternative for testing.
- **0.1.0, 2026-10-14:** initial documented NRPN definition; hardware acceptance pending.
