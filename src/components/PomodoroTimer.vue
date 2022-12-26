<template>
  <!-- timer display -->
  <div class="timer">
    {{ pad(minutes) }}:{{ pad(seconds) }}
  </div>
  <!-- <q-display :value="pad(minutes) + ':' + pad(seconds)" color="secondary" size="4xl" font-weight="bold" /> -->

  <!-- buttons for starting and stopping the timer -->
  <div class ="q-gutter-md">
    <q-btn elevated v-if="!running" @click="startTimer" color="positive" icon="play_arrow" label="Start"/>
    <q-btn elevated v-if="running" @click="stopTimer" color="warning" icon="pause" label="Pause"/>
    <q-btn elevated @click="resetTimer" color="negative" icon="refresh" label="Restart"/>
  </div>
  <div class ="q-gutter-md">
    <q-btn icon="settings" aria-label="Settings" @click="showSettings = true" class="settings-button"/>
    <q-modal v-if="showSettings" @close="showSettings = false" v-model="showSettings" @hide="showSettings = false">
      <q-card>
        <q-card-section>
          <q-input v-model="minutes" type="number" label="Minutes" min="1" step="1" />
          <q-input v-model="seconds" type="number" label="Seconds" min="0" max="59" step="1" />
          <q-input v-model="shortBreakMinutes" type="number" label="Short Break Minutes" min="1" step="1" />
          <q-input v-model="shortBreakSeconds" type="number" label="Short Break Seconds" min="0" max="59" step="1" />
          <q-input v-model="longBreakMinutes" type="number" label="Long Break Minutes" min="1" step="1" />
          <q-input v-model="longBreakSeconds" type="number" label="Long Break Seconds" min="0" max="59" step="1" />
        </q-card-section>
        <q-card-actions align="right">
          <q-btn @click="showSettings = false" label="Cancel"/>
          <q-btn @click="saveChanges" label="Save"/>
        </q-card-actions>
      </q-card>
    </q-modal>
  </div>
</template>

<script>
import { ref } from 'vue';
import '../css/custom.css';

export default {
  // set up reactive properties for the timer display
  data () {
    return {
      showSettings: false,
      minutes: ref(25),
      seconds: ref(0),
      running: ref(false),
      shortBreakMinutes: ref(5),
      shortBreakSeconds: ref(0),
      longBreakMinutes: ref(15),
      longBreakSeconds: ref(0),
    }
  },

  // implement the startTimer method
  methods: {
    startTimer() {
      // set the running flag to true
      this.running = true;

      // set an interval to update the timer display every second
      this.interval = setInterval(() => {
        // decrement the seconds
        this.seconds--;

        // if the seconds reach zero, reset them and decrement the minutes
        if (this.seconds < 0) {
          this.seconds = 59;
          this.minutes--;
        }

        // if the minutes reach zero, reset the timer and display a notification
        if (this.minutes < 0) {
          this.minutes = 0;
          this.seconds = 0;
          clearInterval(this.interval);
          this.running = false;
          // alert('Timer complete!');
          this.startBreak();
        }
      }, 1000);
    },
    
    // implement the startBreak method
    startBreak() {
      this.minutes = this.shortBreakMinutes; // duration of the break in minutes
      this.seconds = this.shortBreakSeconds; // duration of the break in seconds
      this.running = true; // set the running flag to true
      this.interval = setInterval(() => {
        // decrement the seconds
        this.seconds--;

        // if the seconds reach zero, reset them and decrement the minutes
        if (this.seconds < 0) {
          this.seconds = 59;
          this.minutes--;
        }

        // if the minutes reach zero, reset the timer and display a notification
        if (this.minutes < 0) {
          this.minutes = 0;
          this.seconds = 0;
          clearInterval(this.interval);
          this.running = false;
          alert('Break complete!');
        }
      }, 1000);

    },

    // implement the stopTimer method
    stopTimer() {
      // clear the interval and reset the timer
      clearInterval(this.interval);
      this.minutes = this.minutes;
      this.seconds = this.seconds;
      this.running = false;
    },

    resetTimer() {
      // clear the interval and reset the timer to its original value
      clearInterval(this.interval);
      this.minutes = 25;
      this.seconds = 0;
      this.running = false;
    },

    saveChanges() {
      // Save the changes to the timer variables here
      this.showSettings = false
    },

    //format the timer display as mm:ss
    pad(n) {
      return n.toString().padStart(2, '0');
    }
  }
};
</script>
