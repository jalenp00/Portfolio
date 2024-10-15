<template>
  <div class="app">
    <hero />
  </div>
</template>

<script setup lang="ts">
import hero from './components/hero.vue';
</script>

<style>

.app {
  font-family: 'VT323', monospace;
  width: 100vw;
  height: 100vh;
  display: flex;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}
</style>
