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

The eight sound circuits have ten MIDI articulations. The first page keeps kick, drums, multi, snare, the first hat pair, and clap together; the second hat pair occupies rows 9-10. Use the up/down controls in Step Drum to reach the second page.

### Extra rows D8-D3

Hapax has **16 drum slots**. Its default names for rows 9-16 are D1-D8. The ten DRM1 mappings replace rows 1-10, leaving six spare slots above them in the display. In revision 0.1.0 these retained the names **D8 through D3**, as reported during initial hardware use. They are spare Hapax slots, not additional DRM1 sounds. See [Squarp's drum-lane overview and default names](https://squarp.net/hapax/manual/modestep/).

Revision **0.1.1** explicitly labels rows 11-16 with a single **`-`**. The slots remain visible: the current [definition format](https://squarp.net/hapax/instr_def/template.txt) has no documented hide/remove or lane-count command. `NULL` preserves a field; it does not disable a row. These six entries change only the labels, leaving their routing intact, and do not mute or erase any existing events. An empty track avoids inherited events but does not remove the spare slots.

To apply the label change, replace `HAPAX/DRM1-MkIV.txt` on the SD card and **reload the definition** on the track. Replacing the SD file alone does not change a definition already loaded into a project. The ten instrument rows keep their existing order and MIDI mapping.

The numeric mapping comes from **printed p. 20** of the [manual](https://www.vermona.com/fileadmin/user_upload/products/drm1mk4/downloads/drm1mk4_manual_web_en.pdf). Some entries in its separate GM-description column are swapped or inconsistent. Follow the DRM1 instrument column and numeric notes above. Octave labels also vary between sequencers; the MIDI numbers are unambiguous.

## If your unit has learned different notes

The file cannot detect or change the DRM1's stored map. Edit each affected lane's **third field** (channel) and **fourth field** (note), or match the unit to the factory map. Because channels are explicit per lane, changing only Hapax's track channel is insufficient for a custom-channel version.

In the MkIV's learn mode, a note assigned to either hat channel selects its **open** articulation; the closed articulation is automatically two semitones lower. For example, open note 46 gives closed note 44. Reserve those companion notes to avoid unintentionally layering another drum. Use the MkIV procedures on manual pp. 19-20; MkIII instructions differ.

To restore the factory map intentionally, the manual specifies holding the **KICK + DRUM 1 + CLAP** TRIG buttons together. This also resets the optional trigger-input configuration to enabled/GATE, so it is not merely a MIDI-channel change. Loading this definition performs no reset. If preserving a custom map matters, edit the definition instead.

## Supported control

Use note velocity for dynamics. CC, NRPN, pitch bend, aftertouch, program selection, and sound-parameter automation are not provided by the published MIDI receive implementation. The definition consequently has no CC names, encoder assignments, or automation lanes. Pitch, decay, pan, and other timbral controls remain on the instrument. The physical trigger option is not required for MIDI sequencing.

For a controllable overall velocity transformation inside Hapax, a [DRM1 MkIV user's report](https://squarp.community/t/changing-velocity-of-vermona-drm1-with-external-controller/15566) describes using a Scaler effect on velocity. This is a Hapax effect setup, not a hidden Vermona volume CC. An [older MKIII community definition](https://squarp.community/t/hapaxos-1-10-rc-looking-for-beta-test-volunteers/7681?page=2) uses a custom note/channel layout and the former eight-row limit; it is not the MkIV factory map.

No choke-group behavior is configured in the text file. The hi-hat articulations share their respective hardware sound circuit; test their retrigger/cutoff behavior on the actual module rather than assuming a programmable Hapax choke rule.

## Hardware acceptance test

Initial hardware feedback confirms the original file loads and leaves the six default D3-D8 labels visible; those extra rows reportedly produced no sound. The revised placeholder labels and the full checks below still need hardware verification. The test unit's firmware version and connection type have not yet been recorded.

1. Load the file on a fresh track in HapaxOS 3.22 and confirm all ten instrument labels, note numbers, and channel 10. Verify rows 9-10 on the second drum page and that the six spare rows remain visible with `-` labels.
2. Trigger each lane alone at a moderate velocity and check the corresponding DRM1 sound and LED. If one note triggers multiple circuits, inspect the unit's learned map.
3. Compare velocities 32, 64, and 127 on each circuit. Adjust the module's audio levels as needed to hear the dynamic response.
4. Alternate open then closed notes on each hat pair. Confirm the short/long articulations and document any retrigger or choke interaction.
5. Play a short pattern using both drum pages, then save and reload the project. Confirm the mapping survives.

Revision **0.1.1**, 2026-10-14: label the six spare slots `-` and explain the fixed 16-slot layout; instrument mappings unchanged.

Revision **0.1.0**, 2026-10-14: initial factory-map definition.
