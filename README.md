# BITS_DAW

**Alpha v0.17** — a browser-based music workstation for creating fully on-chain, composable music pieces with BITS audio instruments.

The purpose of BITS_DAW is to create music pieces that you can mint on your own artist contract through the [networked.art](https://networked.art) platform. Notes, arrangements, instrument references, effects, and colors form the composition data; the renderer turns that data into playable music and artwork. **The visuals are generated from the music.**

Write notes, play or record a keyboard, arrange ten channels, shape their sound, and preview the composition as audio-reactive artwork. Save editable projects as JSON or export packed composition data for the NetworkedBITSRenderer.

The app is a single HTML file with no build step. The interface is currently labeled **BITS Studio**.

## Getting started

1. Download `BITS_DAW_alpha_v0.17.html` from this repository.
2. Open it in a modern browser with an internet connection. Click **Play** or audition an instrument to enable audio.
3. Select a channel, open **Choose BIT…**, and click an instrument's artwork to audition it. Click **Use sound** to assign it.
4. Double-click in the piano roll to add notes, import a MIDI file, or record a performance.
5. Set the channel's phrase length and arrangement, then press **Play**.
6. Give the project a name under **Project** and click **Save** to download a JSON file.

For MIDI hardware, serve the file from **localhost or HTTPS** and use a browser that supports Web MIDI. If Python 3 is installed, run this from the folder containing the HTML:

```sh
python3 -m http.server 8000
```

Then open `http://localhost:8000` and select the HTML file. Allow MIDI access when the browser asks.

No wallet connection or transaction is needed. The app reads instrument data through an Ethereum mainnet RPC. Change the RPC under **Project → Connection settings** if the default endpoint is unavailable.

## Workspace

| Area | What it does |
| --- | --- |
| Channels | Select and rename ten independent tracks; identify sounds by BIT SVG artwork, mute, solo, and adjust volume plus reverb, delay, and distortion wet/dry knobs. Channels without notes are dimmed. |
| Produce | Edit notes in the piano roll, adjust velocity, play the keyboard, and overlay-record into the selected channel. |
| Arrange | View the full arrangement, toggle play/skip blocks, extend tracks, and seek. |
| Perform | Play ten assignable pads and overlay-record across their channels; switch between this view, Produce, Arrange, and Visual. |
| Visual | Preview the circular composition artwork and live waveform analyzers. |
| Sound inspector | Choose a BIT, inspect its waveform, set the sample start offset, and adjust sampler settings and effects. |
| Arrange inspector | Set phrase length and play/skip slots for the selected channel. |
| Master inspector | Adjust output effects and gain; view stereo peak meters, peak holds, and clip indicators. |
| Data inspector | Inspect or copy note, arrangement, and parameter hex; import from chain or export a renderer artifact. |
| Visual inspector | Set or randomize background, ink, and accent colors; expand the visual preview. |
| Project | Name, save, restore, and import projects; start a blank project or change connection settings. |

Workspace colors are separate from the composition's artwork palette and can be saved or imported independently.

## Notes and performance

- Add notes with a double-click. Drag to move them and drag their right edge to change duration.
- Drag empty space to box-select; hold Shift to add to a selection.
- Cut, copy, paste, duplicate, delete, and undo/redo note edits.
- Choose quarter, eighth, sixteenth, thirty-second, or eighth-note-triplet grids. Hold Alt while dragging to bypass snap.
- Edit velocities in the velocity lane or set a numeric value for selected notes.
- Apply quantize strength and swing, or transpose by a semitone or octave. These commands use selected notes, or all notes when nothing is selected.
- Play the four-octave on-screen keyboard, enable computer keys, or connect a MIDI input.

**Import MIDI** maps note-bearing MIDI parts to editor channels. Choose replacement or merge, and optionally expand phrase lengths to fit the file. Import follows the file's tempo map while leaving the project BPM unchanged; samples and effects stay as configured.

### Perform and overlay recording

In **Produce**, select a channel and instrument, then press **Record**. Recording starts after a one-bar count-in while current notes play as backing. New notes are added to the existing part directly, without a takes dialog. The **Click** control toggles the metronome; Undo restores the previous part.

Open **Perform** for ten pads. Assign each pad a channel and MIDI note (60 plays the original sample pitch). Play pads with the mouse/touchscreen, number keys **1–9, 0**, the matching numpad keys, or MIDI notes **36–45**. Multiple pads can play together. **Prepare pads** loads their instruments; **All notes off** releases held notes.

Perform recording overlays notes onto each pad's assigned channel. Set **Record bars** and velocity before recording; the recording length is at least the longest assigned channel phrase. New notes are merged when recording finishes, and Undo is available per channel.

**Random BITs** loads ten sounds from an editable ID range, defaulting to **1–48**, while keeping notes and channel settings. Save or load a **BIT preset** to reuse the ten sound choices.

Recording captures note pitch, timing, duration, and velocity. It does not record microphone audio or produce an audio mixdown.

## Arrangement and tempo

Each channel has one note phrase and its own phrase length in bars. Arrangement slots either **play** that phrase or **skip** it. Different channels can use different phrase lengths and slot counts.

Use the full arrangement view to toggle blocks, extend tracks, seek from the ruler, or double the composition. The composition loops at the longest arrangement among channels containing note data. Bars contain four beats; notes starting beyond their channel's phrase boundary are not scheduled.

Changing tempo automatically moves and resizes notes to preserve their beat positions and lengths, without a confirmation popup.

## Sound and output

Each channel provides root note, transpose, attack, release, volume, sample start offset, pan, and a voice limit. Defaults are **4 seconds release** and **10 voices**. Choose one voice for monophonic playback, up to 32 voices, or unlimited voices.

Delay time defaults to a **BPM** note division. Uncheck BPM to enter milliseconds. Synced delays follow tempo changes within the channel's one-second delay range. Channel-strip knobs provide volume and reverb, delay, and distortion wet/dry control.

Each channel routes through sampler → delay → reverb → distortion → pan. All channels then feed master distortion → master filter → master gain.

The master filter supports bypass, lowpass, highpass, and bandpass, with cutoff, Q, and rolloff controls. Output meters show stereo sample peaks after master gain.

Sound settings update during playback and performance. Sample/root-note changes rebuild the affected voice, and reverb changes may briefly wait for the effect to prepare.

## Saving and importing

**Save** downloads numbered JSON files such as `My piece-001.json` and also attempts to keep a local browser copy. **Save locally** and **Restore local save** use browser storage. Downloaded JSON files are the portable project backups; browser storage can be cleared or unavailable.

Projects include all ten channels, BIT token IDs, notes, arrangements, sampler/effect parameters, delay-sync settings, performance-pad assignments, channel names, tempo, master settings, colors, and mute/solo settings. Legacy takes remain in project saves. Audio is fetched again from Ethereum using the saved BIT IDs rather than embedded in the JSON.

**Import JSON** lets you select individual channels and choose which data to bring in: notes, arrangements and phrase lengths, sound settings, instruments, and global settings. Parts-only imports preserve the current instruments, phrase lengths, arrangements, and tempo. Note timing remains in milliseconds.

## On-chain workflow

Compose in BITS_DAW, export the renderer artifact, and use it to mint a piece on your own artist contract through networked.art. The editor prepares the composition data; minting happens through the platform.

The pieces use these Ethereum mainnet contracts:

| Contract | Address |
| --- | --- |
| NetworkedBITSRenderer | `0xCA645f14c5e44b7c09f4188dB7536b2D636caE59` |
| BITS audio | `0xB49911F9063154318Dd98848C65c1cb15e43C917` |

The renderer generates the music and its visuals from the composition data, using audio stored in BITS. The visuals reflect the musical structure and respond to playback.

**Import from chain** reads supported Networked Art / BITS compositions using a token link or collection contract and token ID. It offers the same selective import controls. Live values are captured at one block as a fixed snapshot.

**Export artifact** produces a packed version-1 payload for the NetworkedBITSRenderer, including all ten channel slots. Copy the hex, download a `.hex` file, or download a JSON array of `0x`-prefixed chunks for `MintParams.artifact`. Each chunk is at most 24,000 bytes.

Exports contain fixed composition values. Enable **Bake mute/solo** to omit notes from tracks excluded by the current monitor settings; this can change the artwork and loop length. Exporting does not mint or submit a transaction.

### Data formats

| Data | Encoding |
| --- | --- |
| Notes | 7 bytes per event: MIDI note (`uint8`), start milliseconds (`uint24`), duration milliseconds (`uint16`), velocity (`uint8`). Multibyte values are big-endian. |
| Arrangement | V3 play/skip bits, most-significant bit first, followed by a set terminator bit and zero padding. Phrase length is stored separately. |
| Channel settings | 24 parameter bytes; the BIT ID is stored separately in project JSON. |
| Master effects | 8 parameter bytes. |
| Project JSON | `BITS_TEN_CHANNEL_EDITOR_SAVE`, currently schema version **15**. This is separate from app release **v0.17**. |

Older V1 and V2 arrangements are accepted during project import. Renderer artifacts and editable project JSON are different formats.

## Shortcuts

| Shortcut | Action |
| --- | --- |
| Space | Play / stop |
| Enter | Return to the beginning |
| Cmd/Ctrl + S | Download the next numbered project JSON |
| Cmd/Ctrl + X / C / V | Cut / copy / paste notes |
| Cmd/Ctrl + Z / Shift + Z | Undo / redo note edits |
| Delete / Backspace | Delete selected notes |
| Cmd/Ctrl + drag | Copy selected notes |
| Alt + drag | Bypass snap |
| R / T | Zoom piano roll out / in |
| A W S E D F T G Y H U J K | Play notes when computer keys are enabled |
| 1–9, 0 (top row or numpad) | Trigger pads 1–10 in Perform |

Note-editing shortcuts depend on workspace focus. To tap tempo, focus the tempo field and press T repeatedly.

## Dependencies and limitations

- Loads **Tone.js 14.8.49** and **ethers 6.13.5** from public CDNs; instrument reads require an Ethereum mainnet RPC.
- BITS contract: `0xB49911F9063154318Dd98848C65c1cb15e43C917`. Audio comes from `bitToBase64(tokenId)`; artwork comes from the token's assigned renderer.
- MIDI recording requires Web MIDI support and a secure context. MIDI file import does not require hardware access.
- MIDI controller automation, sustain-pedal expression, and pitch bend are not captured by the note protocol.
- Large compositions, high voice counts, and long effects tails can increase CPU and memory use. RPC limits can interrupt sample or chain imports.
- This is an alpha release. Keep downloaded project backups and report bugs with reproduction steps, browser details, and a sample project when possible.
