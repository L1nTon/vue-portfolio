<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import Header from './components/Header.vue'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <Header />

  <main>
    <RouterView />
  </main>
</template>
