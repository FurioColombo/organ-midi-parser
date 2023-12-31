<template>
  <div class="main-container">
    <input type="file" @change="handleFileChange" ref="fileInput" style="display: none" accept=".mid">
    <button @click="openFileInput" class="load-midi-button">Load MIDI</button>

    <!-- Play button -->
    <!--<button @click="playMidi" class="play-midi-button">Play</button>-->
    <button @click="parseAndDisplayTracks" class="parse-midi-button">Parse MIDI</button>
    <!-- Play toggle button -->
    <button @click="togglePlay" class="play-toggle-button">
      {{ isPlaying ? 'Pause' : 'Play' }}
    </button>

    <!-- Display parsed MIDI data -->
    <div v-if="parsedMidi" class="parsed-midi">
      <h2>Parsed MIDI Data:</h2>
      <div class="channels-container">
        <div v-for="(channel, index) in channels" :key="index" class="channel-column">
          <h3>Channel {{ index + 1 }}:</h3>
          <pre>{{ channel }}</pre>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {Midi} from '@tonejs/midi'
import * as Tone from 'tone';
export default {
  name: 'MainPage',
  props: {
    msg: String
  },
  data() {
    return {
      loadedMidi: null,
      parsedMidi: null,
      channels: [], // Array to store MIDI channels data
      player: null, // Store Tone.js player instance
      lowestNote: 24,
      highestNote: 83,
      isPlaying: false,
    };
  },
  created() {
    Tone.start(); // Start the audio context on user interaction
  },
  methods: {
    openFileInput() {
      this.$refs.fileInput.click();
    },

    handleFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.loadMidiFile(file);
      }
    },
    loadMidiFile(file) {
      // read the file
      const reader = new FileReader();
      reader.onload = (e) => {
        try {
          this.loadedMidi = new Midi(e.target.result);
          console.log('Loaded MIDI:', this.loadedMidi);
        } catch (error) {
          console.error('Error while loading MIDI:', error);
        }
      };
      reader.readAsArrayBuffer(file);
    },
    midiNoteToSerial(midiNote) {
      const serialNote = midiNote;
        return serialNote;
    },

    parseAndDisplayTracks() {
      if (this.loadedMidi) {
        try {
          this.channels = []; // Clear previous data
          const tracks = this.loadedMidi.tracks || [];
          tracks.forEach((track) => {
            const notes = (track?.notes || []).map((note) => ({
              midi: note.midi,
              // name: note.name,
              // ticks: note.ticks !== undefined ? note.ticks : null,
              time: Tone.Ticks(note.ticks).toMilliseconds(),
              // durationTicks: note.durationTicks,
              duration: Tone.Ticks(note.durationTicks).toMilliseconds(),
              velocity: note.velocity,
              serialNote: this.midiNoteToSerial(note.midi),
              // Add more properties as needed
            }));
            const filteredNotes = notes
                .filter(note => (
                    note.midi >= this.lowestNote &&
                    note.midi <= this.highestNote &&
                    note.duration > 0
                ));
            if(filteredNotes.length > 0){
              this.channels.push(filteredNotes);
            }
            this.parsedMidi = true;

          });
          console.log('Channels Data:', this.channels);
        } catch (error) {
          console.error('Error while accessing MIDI tracks:', error);
        }
      } else {
        console.error('No MIDI file loaded yet.');
      }
    },
    playMidi() {
      if (this.loadedMidi) {
        try {
          const now = Tone.now() + 0.5; // Adding a delay before starting to play

          // Array to hold the synthesizers
          const synths = [];

          // Loop through each track in the loaded MIDI file
          this.loadedMidi.tracks.forEach((track) => {
            // Create a PolySynth for each track with specified envelope settings
            const synth = new Tone.PolySynth(Tone.Synth, {
              envelope: {
                attack: 0.02,
                decay: 0.1,
                sustain: 0.3,
                release: 1,
              },
            }).toDestination();

            // Push the created synth into the synths array
            synths.push(synth);

            // Schedule each note in the track
            track.notes.forEach((note) => {
              synth.triggerAttackRelease(
                  note.name,
                  Tone.Ticks(note.durationTicks).toMilliseconds(),
                  Tone.Ticks(note.ticks).toMilliseconds() + now,
                  note.velocity
              );
            });
          });

          // Event listener for the play-toggle event using ref
          this.$refs.togglePlay.$on("play", (playing) => {
            if (!playing) {
              // Dispose of synths when stopped
              synths.forEach((synth) => {
                synth.dispose();
              });
            }
          });

        } catch (error) {
          console.error('Error while playing MIDI:', error);
        }
      } else {
        console.error('No MIDI file loaded yet.');
      }
    },
    togglePlay() {
      if (!this.isPlaying) {
        this.playMidi();
      } else {
        // Pause MIDI playback or implement logic for pause functionality
        // Example: pause playback using Tone.js if needed
      }
      this.isPlaying = !this.isPlaying;
    },

  }
}
</script>

<style scoped>
/* Your button styles */
.load-midi-button {
  padding: 10px 20px;
  background-color: #3498db;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.parse-midi-button {
  margin-top: 10px; /* Adjust as needed */
  /* Additional styling for the parse button */
  padding: 10px 20px;
  background-color: #2ecc71;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
.play-midi-button {
  margin-top: 10px;
  /* Additional styling for the play button */
  padding: 10px 20px;
  background-color: #e74c3c;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
.play-toggle-button {
  margin-top: 10px;
  /* Additional styling for the play button */
  padding: 10px 20px;
  background-color: #e74c3c;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

/* Additional styles for displaying parsed MIDI data */
.parsed-midi {
  margin-top: 50px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.channels-container {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}
.channel-column {
  flex: 1;
  margin-right: 20px;
}
.channel-column h3 {
  margin-top: 0;
}
.channel-column pre {
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  white-space: pre-wrap;
  font-family: 'Courier New', Courier, monospace;
}
</style>
