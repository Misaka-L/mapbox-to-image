<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div class="the-app">
    <header class="header-bar">
      <nav>
        <RouterLink to="/">Home</RouterLink>
      </nav>
    </header>

    <main class="app-content">
      <RouterView />
    </main>
  </div>
</template>

<style scoped>
.the-app {
  height: 100%;
  max-height: 50rem;

  display: flex;
  flex-direction: column;

  .app-content {
    flex: 1;
  }
}
</style>
