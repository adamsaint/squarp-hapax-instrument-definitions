# Vermona DRM1 MkIV

[DRM1-MkIV.txt](DRM1-MkIV.txt) configures the manufacturer's **factory MIDI map** as ten named drum lanes on **channel 10**. Target: HapaxOS 3.22; using ten rows requires the expanded drum support introduced in 3.10. Status: **source-reviewed and statically checked; hardware test pending**.

The [current manufacturer manual](https://www.vermona.com/fileadmin/user_upload/products/drm1mk4/downloads/drm1mk4_manual_web_en.pdf), checked October 14, 2026, specifies note-on and velocity reception. Its USB interface supports firmware updates, but no current numbered DRM1 MkIV firmware release was identified on the [public support page](https://www.vermona.com/en/support/product/drm1-mkiv-2/). This definition therefore targets the published MkIV protocol without inventing an OS version. The [MkIV change document](https://www.vermona.com/fileadmin/user_upload/products/drm1mk4/downloads/drm1mk4_changelog_en.pdf) describes hardware-generation improvements over MkIII, not a numbered MIDI firmware release.

## Setup and mapping

Copy the raw file to `HAPAX/DRM1-MkIV.txt`, load it on a new track, and select the connected DIN output or USB Host port. The file intentionally sets both track and lane channels to **10**, matching the factory configuration. This is a device-specific choice, not an assumed personal studio channel.

| Hapax row | Sound | MIDI note | Channel |
| --- | --- | --- | --- |
| 1 | Kick | 36 | 10 |
| 2 | Drum 1 | 45 | 10 |
| 3 | Drum 2 | 50 | 10 |
| 4 | Multi | 56 | 10 |
| 5 | Snare | 38 | 10 |
| 6 | Hi-hat 1 closed | 44 | 10 |
| 7 | Hi-hat 1 open/cymbal | 46 | 10 |
| 8 | Clap | 39 | 10 |
| 9 | Hi-hat 2 closed | 49 | 10 |
| 10 | Hi-hat 2 open/cymbal | 51 | 10 |
| 11-16 | `-` spare slots | 0, unused by factory map | 10 |

The eight sound circuits have ten MIDI articulations. The table follows **ascending lane numbers**, restoring the original order. User testing found that loading the definition starts at the lower rows, so the first page contains Kick through Clap in rows 1-8. The second page contains the second hi-hat pair in rows 9-10 and six spare slots in rows 11-16. Higher-numbered rows appear above lower-numbered rows on screen; Kick is at the bottom of the first page. Use the up/down controls in Step Drum to change pages.

The numeric sound mapping comes from **printed p. 20** of the [manual](https://www.vermona.com/fileadmin/user_upload/products/drm1mk4/downloads/drm1mk4_manual_web_en.pdf). Some entries in its separate GM-description column are swapped or inconsistent. Follow the DRM1 instrument column and numeric notes above. Octave labels also vary between sequencers; the MIDI numbers are unambiguous.

### Spare rows and updating an existing track

Hapax has **16 drum slots**. Its default names for rows 9-16 are D1-D8. Revision 0.1.0 left six unused slots above the instruments, labeled D8 through D3; they were spare Hapax rows, not additional DRM1 sounds. See [Squarp's drum-lane overview and default names](https://squarp.net/hapax/manual/modestep/).

Revision **0.3.0** restores the original instrument rows 1-10 while retaining the **`-`** labels for spare rows 11-16. The spares send **note 0 on channel 10**, which is unassigned in the DRM1's factory map. Explicitly setting their notes prevents old sound mappings from surviving when reloading the file. This is not a MIDI mute command; a custom learned map could respond to note 0, so choose another unused note if necessary.

The spare slots remain visible: the current [definition format](https://squarp.net/hapax/instr_def/template.txt) has no documented hide/remove or lane-count command. `NULL` preserves a field; it does not disable a row. A new track also retains all 16 slots.

**The row layout changes from 0.2.0 and restores the instrument positions used in 0.1.x.** Replace `HAPAX/DRM1-MkIV.txt` on the SD card and load it onto a **fresh track** to test. Replacing the SD file alone does not update an already-loaded track. Reloading onto an existing pattern changes the sounds assigned to its rows; the definition does not rearrange pattern events. The MIDI note and channel for each named DRM1 sound remain unchanged.

For manual layout edits on Hapax, select a lane, hold **2ND**, and press **Up/Down** to move it, as described in [Squarp's lane-order instructions](https://squarp.net/hapax/manual/modestep/).

## If your unit has learned different notes

The file cannot detect or change the DRM1's stored map. Edit each affected lane's **third field** (channel) and **fourth field** (note), or match the unit to the factory map. Because channels are explicit per lane, changing only Hapax's track channel is insufficient for a custom-channel version.

In the MkIV's learn mode, a note assigned to either hat channel selects its **open** articulation; the closed articulation is automatically two semitones lower. For example, open note 46 gives closed note 44. Reserve those companion notes to avoid unintentionally layering another drum. Use the MkIV procedures on manual pp. 19-20; MkIII instructions differ.

To restore the factory map intentionally, the manual specifies holding the **KICK + DRUM 1 + CLAP** TRIG buttons together. This also resets the optional trigger-input configuration to enabled/GATE, so it is not merely a MIDI-channel change. Loading this definition performs no reset. If preserving a custom map matters, edit the definition instead.

## Supported control

Use note velocity for dynamics. CC, NRPN, pitch bend, aftertouch, program selection, and sound-parameter automation are not provided by the published MIDI receive implementation. The definition consequently has no CC names, encoder assignments, or automation lanes. Pitch, decay, pan, and other timbral controls remain on the instrument. The physical trigger option is not required for MIDI sequencing.

For a controllable overall velocity transformation inside Hapax, a [DRM1 MkIV user's report](https://squarp.community/t/changing-velocity-of-vermona-drm1-with-external-controller/15566) describes using a Scaler effect on velocity. This is a Hapax effect setup, not a hidden Vermona volume CC. An [older MKIII community definition](https://squarp.community/t/hapaxos-1-10-rc-looking-for-beta-test-volunteers/7681?page=2) uses a custom note/channel layout and the former eight-row limit; it is not the MkIV factory map.

No choke-group behavior is configured in the text file. The hi-hat articulations share their respective hardware sound circuit; test their retrigger/cutoff behavior on the actual module rather than assuming a programmable Hapax choke rule.

## Hardware acceptance test

Initial hardware feedback confirms the original file loads and leaves the six default D3-D8 labels visible above the instrument rows; those extra rows reportedly produced no sound. Further user testing found that loading starts at the lower rows, prompting revision 0.3.0 to restore the original lane order while retaining the dash labels. This feedback establishes the preferred layout; the full checks below still need hardware verification. The test unit's displayed firmware version and connection type have not yet been recorded.

1. Load the file on a fresh track in HapaxOS 3.22 and confirm the lane numbers in the table, each sound's note number, and channel 10. Kick should be at row 1, the second hi-hat pair at rows 9-10, and the six `-` slots at rows 11-16. Check the initial page and confirm the spare rows send note 0 without triggering the factory-mapped DRM1.
2. Trigger each lane alone at a moderate velocity and check the corresponding DRM1 sound and LED. If one note triggers multiple circuits, inspect the unit's learned map.
3. Compare velocities 32, 64, and 127 on each circuit. Adjust the module's audio levels as needed to hear the dynamic response.
4. Alternate open then closed notes on each hat pair. Confirm the short/long articulations and document any retrigger or choke interaction.
5. Play a short pattern using both drum pages, then save and reload the project. Confirm the mapping survives.

Revision **0.3.0**, 2026-10-14: restore the original instrument order in rows 1-10 after user testing found loading starts at the bottom; keep dash labels and unused factory note 0 for spare rows 11-16.

Revision **0.2.0**, 2026-10-14: put the ten instruments at the top in Kick-first order, with spare rows at the bottom mapped to unused factory note 0. This changes row positions used by earlier patterns.

Revision **0.1.1**, 2026-10-14: label the six spare slots `-` and explain the fixed 16-slot layout; instrument mappings unchanged.

Revision **0.1.0**, 2026-10-14: initial factory-map definition.
