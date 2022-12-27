<template>
  <!-- timer display -->
  <div class="timer">
    {{ pad(elapsedMinutes) }}:{{ pad(elapsedSeconds) }}
    <span class="milliseconds">.{{ pad(elapsedMilliseconds) }} </span>
  </div>

  <!-- buttons -->
  <div class="q-gutter-md timer">
    <q-btn
      elevated
      v-if="!running"
      @click="startTimer"
      color="positive"
      icon="play_arrow"
      label="Start"
    />
    <q-btn
      elevated
      v-if="running"
      @click="stopTimer"
      color="warning"
      icon="pause"
      label="Pause"
    />
    <q-btn
      elevated
      @click="resetTimer"
      color="negative"
      icon="refresh"
      label="Restart"
    />
  </div>
</template>

<script>
import { ref } from 'vue';
import '../css/custom.css';
import { defaultTimerParameters } from './defaultSettings.ts';

export default {
  // set up reactive properties for the timer display
  data() {
    return {
      defaultTimerParameters,
      useShortBreak: ref(defaultTimerParameters.useShortBreak),
      elapsedMinutes: ref(defaultTimerParameters.defaultMinutes),
      elapsedSeconds: ref(defaultTimerParameters.defaultSeconds),
      elapsedMilliseconds: ref(0),
      running: ref(false),
      paused: ref(false),
      breakActive: ref(false),
    };
  },

  // implement the startTimer method
  methods: {
    startTimer() {
      if (!this.running && !this.paused) {
        // set timer to initial values if timer is not running
        this.elapsedMinutes = this.totalMinutes =
          defaultTimerParameters.defaultMinutes;
        this.elapsedSeconds = this.totalSeconds =
          defaultTimerParameters.defaultSeconds;
        this.elapsedMilliseconds = ref(0);
      }
      this.running = true; // set the running flag to true
      this.paused = false; // set the paused flag to false
      // console.log('clicked startTimer');
      this.startTimerInterval(); //start timer countdown
    },

    // implement the startBreak method
    startBreak() {
      if (this.useShortBreak) {
        this.elapsedMinutes = this.totalMinutes =
          defaultTimerParameters.defaultShortBreakMinutes; // duration of the break in minutes
        this.elapsedSeconds = this.totalSeconds =
          defaultTimerParameters.defaultShortBreakSeconds; // duration of the break in seconds
        this.elapsedMilliseconds = ref(0);
      } else {
        this.elapsedMinutes = this.totalMinutes =
          defaultTimerParameters.defaultLongBreakMinutes; // set total duration of the break in minutes
        this.elapsedSeconds = this.totalMutes =
          defaultTimerParameters.defaultLongBreakSeconds; // set total duration of the break in seconds
        this.elapsedMilliseconds = ref(0);
      }
      this.running = true; // set the running flag to true
      this.breakActive = true; // set the breakActive flag to true
      this.startTimerInterval(); // start timer countdown
    },

    // implement the stopTimer method
    stopTimer() {
      // clear the interval and reset the timer
      this.running = false;
      this.paused = true;
      clearInterval(this.interval);
    },

    startTimerInterval() {
      let startTime = performance.now(); // get the start time
      let totalTime = this.elapsedMinutes * 60000 + this.elapsedSeconds * 1000; // calculate total time in milliseconds
      this.interval = setInterval(() => {
        let elapsedTime = performance.now() - startTime;
        let remainingTime = totalTime - elapsedTime; // subtract elapsed time from total time
        this.elapsedMilliseconds = Math.floor(remainingTime % 1000);
        this.elapsedSeconds = Math.floor(remainingTime / 1000) % 60;
        this.elapsedMinutes = Math.floor(remainingTime / (1000 * 60)) % 60;
        // if the minutes reach zero, reset the timer and display a notification
        if (remainingTime <= 0) {
          this.running = false;
          this.playSound(
            'http://soundbible.com/mp3/Elevator Ding-SoundBible.com-685385892.mp3'
          );
          clearInterval(this.interval); // clear the interval
          if (this.breakActive) {
            this.startTimer();
            this.breakActive = false; // set the breakActive flag to false
          } else {
            this.startBreak();
          }
        }
      }, 10);
    },

    resetTimer() {
      this.elapsedMinutes = defaultTimerParameters.defaultMinutes;
      this.elapsedSeconds = defaultTimerParameters.defaultSeconds;
      this.elapsedMilliseconds = ref(0);
      this.running = false; // reset the running flag to false
      this.paused = false; // reset the paused flag to false
      this.breakActive = false; // reset the breakActive flag to false
      // clear the interval and reset the timer to its original value
      clearInterval(this.interval);
    },

    playSound(sound) {
      if (sound) {
        var audio = new Audio(sound);
        audio.play();
      }
    },

    //format the timer display as mm:ss
    pad(n) {
      return n.toString().padStart(2, '0');
    },
  },
};
</script>
