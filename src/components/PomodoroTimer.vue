<template>
  <!-- timer display -->
  <div class="timer">{{ pad(minutes) }}:{{ pad(seconds) }}</div>

  <!-- buttons for starting and stopping the timer -->
  <div class="buttons">
    <button v-if="!running" @click="startTimer" class="start-button">Start</button>
    <button v-if="running" @click="stopTimer" class="stop-button">Stop</button>
  </div>
</template>

<script>
import { ref } from 'vue';
import '../css/custom.css';

export default {
  // set up reactive properties for the timer display
  data () {
    return {
      minutes: ref(25),
      seconds: ref(0),
      running: ref(false)
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
          alert('Timer complete!');
        }
      }, 1000);
    },

    // implement the stopTimer method
    stopTimer() {
      // clear the interval and reset the timer
      clearInterval(this.interval);
      this.minutes = 25;
      this.seconds = 0;
      this.running = false;
    },
    //format the timer display as mm:ss
    pad(n) {
      return n.toString().padStart(2, '0');
    }
  }
};
</script>
