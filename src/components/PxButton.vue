<template>
  <div>
    <beat-loader :loading="isLoading" :color="'#68d391'" :size="8" />
    <button
      @click="click"
      v-show="!isLoading"
      class="bg-transparent hover:bg-green-500 text-green-700 font-semibold hover:text-white py-2 px-2 border border-green-500 hover:border-transparent rounded"
    >
      <slot></slot>
    </button>
  </div>
</template>

<script>
export default {
  name: "PxButton",

  props: {
    isLoading: {
      type: Boolean,
      default: false,
    },
  },

  methods: {
    click() {
      this.$emit('click')
    }
  }
}
</script>
