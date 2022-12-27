<template>
  <q-layout
    view="hHh lpR fFf"
    :class="{
      dark: isDarkMode,
      break: isBreakActive,
      paused: isPaused,
    }"
  >
    <q-header reveal elevated class="bg-dark text-white">
      <q-toolbar>
        <q-btn dense flat round icon="menu" @click="toggleLeftDrawer" />

        <q-toolbar-title>
          <q-avatar>
            <img src="~assets/pomo_logo.png" />
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

    <q-drawer
      v-model="leftDrawerOpen"
      side="left"
      overlay
      bordered
      class="bg-dark text-white"
    >
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

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import EssentialLink from 'components/EssentialLink.vue';
import data from 'src/components/PomodoroTimer.vue';

const isDarkMode = ref(false);
const isBreakActive = data.breakActive;
const isPaused = data.paused;
const isRunning = data.running;
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
}
</script>
