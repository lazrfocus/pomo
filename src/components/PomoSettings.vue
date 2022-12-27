<template>
  <div>
    <!-- settings form -->
    <form>
      <label for="dark-mode-toggle">Dark Mode</label>
      <input type="checkbox" id="dark-mode-toggle" v-model="darkModeEnabled" />
    </form>
    <q-btn @click="saveSettings" label="Save" />
  </div>
</template>

<script lang="ts">
import { ref } from 'vue';
import * as idb from 'idb';

// open an IndexedDB database
const dbPromise = idb.openDB('pomo-app-db', 1, {
  upgrade(upgradeDB) {
    // create the 'settings' object store if it doesn't exist
    if (!upgradeDB.objectStoreNames.contains('settings')) {
      upgradeDB.createObjectStore('settings');
    }
    // // create the 'tasks' object store if it doesn't exist
    // if (!upgradeDB.objectStoreNames.contains('tasks')) {
    //   upgradeDB.createObjectStore('tasks', { keyPath: 'name' });
    // }
  },
});

export default {
  data() {
    return {
      tasks: [],
      darkModeEnabled: ref(false),
    };
  },
  async created() {
    // load the settings and tasks from IndexedDB when the component is created
    const db = await dbPromise;
    const tx = db.transaction(['settings', 'tasks'], 'readonly');
    const settingsStore = tx.objectStore('settings');
    // const tasksStore = tx.objectStore('tasks');
    this.darkModeEnabled = await settingsStore.get('darkModeEnabled');
    // this.tasks = await tasksStore.getAll();
  },
  methods: {
    async saveSettings() {
      // save the settings to IndexedDB when the user clicks the save button
      const db = await dbPromise;
      const tx = db.transaction(['settings'], 'readwrite');
      const store = tx.objectStore('settings');
      store.put({ key: 'darkModeEnabled', value: this.darkModeEnabled });
    },
    // async addTask(taskName: string) {
    //   // add a new task to the tasks array and save it to IndexedDB
    //   this.tasks.push({ taskName, count: 0 });
    //   const db = await dbPromise;
    //   const tx = db.transaction(['tasks'], 'readwrite');
    //   const store = tx.objectStore('tasks');
    //   store.put({ taskName, count: 0 });
    // },
  },
};
</script>
