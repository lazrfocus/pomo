<template>
  <!-- <div>
    <ChildComponent
      :breakActive="isBreakActive"
      :paused="isPaused"
      :running="isRunning"
    />
  </div> -->
  <q-layout
    view="hHh lpR fFf"
    :class="{
      dark: isDarkMode,
      break: isBreakActive,
      paused: isPaused,
      running: isRunning,
    }"
  >
    <q-header reveal elevated class="bg-dark text-white">
      <q-toolbar>
        <q-btn dense flat round icon="menu" @click="toggleLeftDrawer" />

        <q-toolbar-title>
          <q-avatar>
            <img src="src/assets/pomo_logo.png" />
          </q-avatar>
          Pomo
        </q-toolbar-title>

        <!-- toggle dark mode button -->
        <q-btn
          elevated
          @click="toggleDarkMode"
          :color="isDarkMode ? 'dark' : 'gray'"
          :icon="isDarkMode ? 'nightlight' : 'lightbulb_outline'"
        />
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" side="left" overlay bordered>
      <q-list>
        <q-item-label header> Pomo </q-item-label>
        <EssentialLink
          v-for="link in pomoLinks"
          :key="link.title"
          v-bind="link"
        />
        <q-item-label header> External Links </q-item-label>
        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container
      :class="{
        dark: isDarkMode,
        break: isBreakActive,
        paused: isPaused,
        running: isRunning,
      }"
    >
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import EssentialLink from 'components/EssentialLink.vue';
import PomodoroTimerVue from 'src/components/PomodoroTimer.vue';

const isDarkMode = ref(false);
const isBreakActive = PomodoroTimerVue.breakActive;
const isPaused = PomodoroTimerVue.paused;
const isRunning = PomodoroTimerVue.running;
console.log(isBreakActive, isPaused, isRunning);
const pomoLinks = [
  {
    title: 'Home',
    caption: 'Home',
    icon: 'home',
    link: '#',
  },
];

const essentialLinks = [
  {
    title: 'Github',
    caption: 'github.com/lazrfocus/pomo',
    icon: 'code',
    link: 'https://github.com/lazrfocus/pomo',
  },
];

const leftDrawerOpen = ref(false);

function toggleLeftDrawer() {
  leftDrawerOpen.value = !leftDrawerOpen.value;
}

function toggleDarkMode() {
  isDarkMode.value = !isDarkMode.value;
  document.body.classList.toggle('dark', isDarkMode.value);
}
</script>
