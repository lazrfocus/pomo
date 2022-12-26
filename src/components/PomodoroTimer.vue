<template>
  <!-- timer display -->
  <div class="timer">
    {{ pad(elapsedMinutes) }}:{{ pad(elapsedSeconds) }}
  </div>
  <!-- <q-display :value="pad(minutes) + ':' + pad(seconds)" color="secondary" size="4xl" font-weight="bold" /> -->

  <!-- buttons for starting and stopping the timer -->
  <div class="progressbar">
    <q-circular-progress show-value size=50px :value="percentageRemaining.toFixed(1)" :label="percentageRemaining" color="warning" :thickness="0.1" class="q-ma-md" >{{ percentageRemaining.toFixed(1) }}% </q-circular-progress>
  </div>
  <div class ="q-gutter-md">
    <q-btn elevated v-if="!running" @click="startTimer" color="positive" icon="play_arrow" label="Start"/>
    <q-btn elevated v-if="running" @click="stopTimer" color="warning" icon="pause" label="Pause"/>
    <q-btn elevated @click="resetTimer" color="negative" icon="refresh" label="Restart"/>
  </div>
  <div class ="q-gutter-md">
    <q-btn icon="settings" aria-label="Settings" @click="this.showSettings = true" class="settings-button"/>
    <q-dialog v-if="showSettings" @close="this.showSettings = false" v-model="showSettings" @hide="this.showSettings = false">
      <q-card>
        <q-card-section class="columns items-center no-wrap">
          <q-input v-model="setMinutes" type="number" label="Minutes" min="1" step="1" />
          <q-input v-model="setSeconds" type="number" label="Seconds" min="0" max="59" step="1" />
          <q-input v-model="setShortBreakMinutes" type="number" label="Short Break Minutes" min="1" step="1" />
          <q-input v-model="setShortBreakSeconds" type="number" label="Short Break Seconds" min="0" max="59" step="1" />
          <q-input v-model="setLongBreakMinutes" type="number" label="Long Break Minutes" min="1" step="1" />
          <q-input v-model="setLongBreakSeconds" type="number" label="Long Break Seconds" min="0" max="59" step="1" />
          <q-toggle v-model="useShortBreak" label="Use short break" />
        </q-card-section>
        <q-card-actions align="right">
          <q-btn @click="cancelSettings" label="Cancel"/>
          <q-btn @click="saveSettings" label="Save"/>
          <q-btn @click="resetSettings" label="Reset"/>
        </q-card-actions>
      </q-card>
    </q-dialog>
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
      running: ref(false),
      useShortBreak: ref(true),
      initMinutes: ref(25),
      initSeconds: ref(0),
      initShortBreakMinutes: ref(5),
      initShortBreakSeconds: ref(0),
      initLongBreakMinutes: ref(15),
      initLongBreakSeconds: ref(0),
      elapsedMinutes: ref(25),
      elapsedSeconds: ref(0),
      totalMinutes: ref(25),
      totalSeconds: ref(0),
      setMinutes: ref(25),
      setSeconds: ref(0),
      setShortBreakMinutes: ref(5),
      setShortBreakSeconds: ref(0),
      setLongBreakMinutes: ref(15),
      setLongBreakSeconds: ref(0)
    }
  },

  computed: {
    percentageRemaining() {
      let totalElapsed = this.elapsedMinutes * 60 + this.elapsedSeconds;
      // let totalTime = this.totalMinutes * 60 + this.totalSeconds;
      // return (100- (totalElapsed / totalTime) * 100);
      let totalTime;
      if (this.breakActive)
        totalTime = this.totalBreakMinutes * 60 + this.totalBreakMinutes;
      else
        totalTime = this.totalMinutes * 60 + this.totalSeconds;
      return (100 - (totalElapsed / totalTime) * 100);      
    }
  },

  watch: {
    percentageRemaining(newValue, oldValue) {
      console.log(`percentageRemaining changed from ${oldValue} to ${newValue}`)
    }
  },
  
  // implement the startTimer method
  methods: {
    startTimer() {
      this.running = true; // set the running flag to true
      // set an interval to update the timer display every second
      this.interval = setInterval(() => {
        // decrement the seconds
        this.elapsedSeconds--;

        // if the seconds reach zero, reset them and decrement the minutes
        if (this.elapsedSeconds < 0) {
          this.elapsedSeconds = 59;
          this.elapsedMinutes--;
        }

        // if the minutes reach zero, reset the timer and display a notification
        if (this.elapsedMinutes < 0) {
          this.elapsedMinutes = 0;
          this.elapsedSeconds = 0;
          this.running = false;
          this.playSound('http://soundbible.com/mp3/Elevator Ding-SoundBible.com-685385892.mp3');
          // alert('Timer complete!');
          clearInterval(this.interval); // clear the interval
          this.startBreak();
        }
      }, 1000);
    },
    
    // implement the startBreak method
    startBreak() {
      if (this.useShortBreak) {
        this.elapsedMinutes = this.setShortBreakMinutes; // duration of the break in minutes
        this.elapsedSeconds = this.setShortBreakSeconds; // duration of the break in seconds
      }
      else {
        this.elapsedMinutes = this.setLongBreakMinutes; // duration of the break in minutes
        this.elapsedSeconds = this.setLongBreakSeconds; // duration of the break in seconds
      }
      this.running = true; // set the running flag to true
      this.breakActive = true; // set the breakActive flag to true
      this.interval = setInterval(() => {
        // decrement the seconds
        this.elapsedSeconds--;

        // if the seconds reach zero, reset them and decrement the minutes
        if (this.elapsedSeconds < 0) {
          this.elapsedSeconds = 59;
          this.elapsedMinutes--;
        }

        // if the minutes reach zero, reset the timer and display a notification
        if (this.elapsedMinutes < 0) {
          this.elapsedMinutes = 0;
          this.elapsedSeconds = 0;
          this.running = false;
          clearInterval(this.interval); // clear the interval
          this.startTimer();
          this.breakActive = false; // set the breakActive flag to false
        }
      }, 1000);

    },

    // implement the stopTimer method
    stopTimer() {
      // clear the interval and reset the timer
      clearInterval(this.interval);
      // this.minutes = this.setMinutes;
      // this.seconds = this.setSeconds;
      this.running = false;
    },

    resetTimer() {
      // clear the interval and reset the timer to its original value
      clearInterval(this.interval);
      this.elapsedMinutes = this.setMinutes;
      this.elapsedSeconds = this.setSeconds;
      this.running = false;
    },

    saveSettings() {
      // Save the changes to the timer variables here
      this.showSettings = false
      this.totalMinutes = this.setMinutes
      this.totalSeconds = this.setSeconds
      
    },

    cancelSettings() {
      // Save the changes to the timer variables here
      this.showSettings = false
    },

    resetSettings() {
      // Save the changes to the timer variables here
      // this.showSettings = false
      this.setMinutes = this.initMinutes
      this.setSeconds = this.initSeconds
      this.shortBreakMinutes = this.initShortBreakMinutes
      this.shortBreakSeconds = this.initShortBreakSeconds
      this.longBreakMinutes = this.initLongBreakMinutes
      this.longBreakSeconds = this.initLongBreakSeconds
    },

    playSound (sound) {
      if(sound) {
        var audio = new Audio(sound);
        audio.play();
      }
    },

    //format the timer display as mm:ss
    pad(n) {
      return n.toString().padStart(2, '0');
    }
  }
};
</script>
