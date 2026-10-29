
<script setup lang="ts">
defineProps<{
  name?: string
  size?: number | string
}>()
</script>

<template>
  <svg
    :width="size ?? 20"
    :height="size ?? 20"
    viewBox="0 0 24 24"
    fill="none"
    stroke="currentColor"
    stroke-width="1.8"
    stroke-linecap="round"
    stroke-linejoin="round"
    aria-hidden="true"
  >
    <template v-if="name === 'graduation-cap'">
      <path d="m2 10 10-5 10 5-10 5-10-5Z" />
      <path d="M6 12v5c4 3 8 3 12 0v-5" />
      <path d="M22 10v7" />
    </template>

    <template v-else-if="name === 'user'">
      <circle cx="12" cy="8" r="4" />
      <path d="M4 21v-2a8 8 0 0 1 16 0v2" />
    </template>

    <template v-else-if="name === 'arrow-right'">
      <path d="M5 12h14m-6-6 6 6-6 6" />
    </template>

    <template v-else-if="name === 'shield'">
      <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10Z" />
      <path d="m9 12 2 2 4-4" />
    </template>

    <template v-else-if="name === 'menu'">
      <path d="M4 6h16M4 12h16M4 18h16" />
    </template>

    <template v-else-if="name === 'bell'">
      <path d="M18 8a6 6 0 0 0-12 0c0 7-3 7-3 9h18c0-2-3-2-3-9Z" />
      <path d="M10 21h4" />
    </template>

    <template v-else>
      <circle cx="12" cy="12" r="9" />
      <path d="m8 12 3 3 5-6" />
    </template>
  </svg>
</template>
