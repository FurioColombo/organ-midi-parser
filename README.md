# organ-midi-parser
**THIS README IS CHATGPT GENERATED**

## Setup

1. **Clone Repository**
   - Clone the repository to your local machine:
     ```bash
     git clone <repository_url>
     ```

2. **Install Dependencies**
   - Ensure you have Node.js installed.
   - Install project dependencies using npm or yarn:
     ```bash
     npm install
     # or
     yarn install
     ```

## Description

This app is a simple MIDI file player built with Vue.js and Tone.js, allowing users to load MIDI files, parse their tracks, and play them.

## Methods

- `openFileInput()`
  - Opens the file input dialog to select a MIDI file for loading.

- `handleFileChange(event)`
  - Handles the file change event and loads the selected MIDI file.

- `loadMidiFile(file)`
  - Reads and loads the MIDI file data using FileReader.

- `midiNoteToSerial(midiNote)`
  - Converts MIDI note to serial format if needed.

- `parseAndDisplayTracks()`
  - Parses the loaded MIDI file tracks and filters notes based on the specified MIDI note range and note duration.
  - Displays the parsed MIDI data.

- `playMidi()`
  - [TODO] Fix broken playback method.
  - Triggers the playback of the loaded MIDI file using Tone.js.
  - Creates synthesizers for each track and schedules notes to be played.

- `togglePlay()`
  - Toggles the MIDI file playback between play and pause states.

## Usage

1. Click "Load MIDI" to select a MIDI file.
2. Click "Parse MIDI" to parse and display the MIDI file data.
3. Use the "Play" button to start playing the MIDI file.
4. The "Play" button toggles between play and pause states.

## Dependencies

- Vue.js - JavaScript framework for building user interfaces.
- Tone.js - Web Audio framework for creating interactive music in the browser.
- @tonejs/midi - MIDI parser and synthesizer for Tone.js.

## TODO

- [ ] Fix the broken playback method.
- [ ] Create a player interface for controlling playback.
- [ ] Implement functionality to send MIDI data to an Arduino via serial messages.
