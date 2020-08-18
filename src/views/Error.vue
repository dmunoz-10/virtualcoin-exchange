<template>
  <div class="flex-col items-center">
    <h1 class="text-gray-700 text-6xl uppercase">Error</h1>

    <router-link
      :to="{ name: 'home' }"
      class="mt-5 text-xl text-green-600 hover:underline"
    >
      Go to the main page
    </router-link>
  </div>
</template>