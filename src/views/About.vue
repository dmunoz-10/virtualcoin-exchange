<template>
  <div class="flex-col items-center">
    <h1 class="text-gray-700 text-6xl">Virtualcoin Exchange v1.0</h1>
    <p>
      Project to get the prices of the cryptocoins more important througout the API REST of Coincap.
    </p>

    <router-link
      :to="{ name: 'home' }"
      class="mt-5 text-xl text-green-600 hover:underline"
    >
      Go to the main page
    </router-link>
  </div>
</template>
