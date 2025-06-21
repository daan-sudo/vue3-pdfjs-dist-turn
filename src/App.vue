<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <div id="App">
    <RouterView />
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
}
#App {
  min-width: 100vw;
  min-height: 100vh;
  height: 100vh;
  width: 100vw;
  background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
  overflow: hidden;
}
</style>
