<template>
  <div class="settings-page">
    <div>
      <h1>Settings</h1>
    </div>
    <!-- settings form -->
    <form>
      <div>
        <label for="work-length-input">Work Length (minutes)</label>
        <input type="number" id="work-length-input" v-model="workLength" />
      </div>
      <div>
        <label for="break-length-input">Break Length (minutes)</label>
        <input type="number" id="break-length-input" v-model="breakLength" />
      </div>
      <div>
        <label for="dark-mode-toggle">Dark Mode</label>
        <input
          type="checkbox"
          id="dark-mode-toggle"
          v-model="darkModeEnabled"
        />
      </div>
    </form>
    <div>
      <q-btn
        elevated
        color="positive"
        @click="saveSettings"
        label="Save"
        icon="save"
      />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      workLength: 25, // default work length is 25 minutes
      breakLength: 5, // default break length is 5 minutes
      darkModeEnabled: false, // dark mode is disabled by default
    };
  },
  methods: {
    saveSettings() {
      // save the settings to IndexedDB or a server
      // code to save the settings goes here
    },
  },
};
</script>
