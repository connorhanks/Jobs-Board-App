<script setup>
import { RouterLink } from "vue-router";
</script>

<template>
  <section
    class="text-center flex flex-col justify-center items-center h-96 mt-24"
  >
    <header>
      <i class="mdi mdi-alert text-yellow-400 text-7xl mb-5"></i>
      <h1 class="text-6xl font-bold mb-4">404 Not Found</h1>
      <p class="text-xl mb-5">This page does not exist</p>
    </header>
    <RouterLink
      to="/"
      class="text-white bg-blue-700 hover:bg-green-900 rounded-md px-3 py-2 mt-4"
    >
      Go Back
    </RouterLink>
  </section>
</template>
