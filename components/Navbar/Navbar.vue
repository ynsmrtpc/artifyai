<template>

  <nav class="d-flex justify-content-between">
    <NuxtLink class="text-white text-decoration-none badge bg-primary text-wrap p-2" style="opacity: .7;" to="/improve">
      Improve Your Image
    </NuxtLink>
    <NuxtLink class="text-white text-decoration-none badge bg-primary text-wrap p-2" style="opacity: .7;"
      to="/texttoimage">Text To Image
    </NuxtLink>
    <NuxtLink class="text-white text-decoration-none badge bg-primary text-wrap p-2" style="opacity: .7;"
      to="/texttomusic">Text To Music
    </NuxtLink>
  </nav>
</template>

<style scoped>
.badge:hover {
  opacity: 1 !important;
}
</style>