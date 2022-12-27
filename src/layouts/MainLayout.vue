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
      :width="175"
      :breakpoint="500"
    >
      <q-scroll-area class="fit">
        <q-list padding class="menu-list">
          <router-link to="/">
            <q-item active clickable v-ripple>
              <q-item-section avatar>
                <q-icon name="home" />
              </q-item-section>

              <q-item-section> Home </q-item-section>
            </q-item>
          </router-link>

          <router-link to="/settings">
            <q-item clickable v-ripple>
              <q-item-section avatar>
                <q-icon name="settings" />
              </q-item-section>

              <q-item-section> Settings </q-item-section>
            </q-item>
          </router-link>

          <router-link to="/about">
            <q-item clickable v-ripple>
              <q-item-section avatar>
                <q-icon name="info_outline" />
              </q-item-section>

              <q-item-section> Info </q-item-section>
            </q-item>
          </router-link>

          <a href="https://www.github.com/lazrfocus/pomo">
            <q-item clickable v-ripple>
              <q-item-section avatar>
                <q-icon name="code" />
              </q-item-section>

              <q-item-section> Github </q-item-section>
            </q-item>
          </a>
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import data from 'src/components/PomodoroTimer.vue';

const isDarkMode = ref(false);
const isBreakActive = data.breakActive;
const isPaused = data.paused;
const isRunning = data.running;
console.log(isBreakActive, isPaused, isRunning);

const leftDrawerOpen = ref(false);

function toggleLeftDrawer() {
  leftDrawerOpen.value = !leftDrawerOpen.value;
}

function toggleDarkMode() {
  isDarkMode.value = !isDarkMode.value;
}
</script>
