<template>
  <main>
    <px-header :links="links" />

    <router-view class="container px-5 sm:px-20 py-20 flex justify-center" />

    <px-footer />
  </main>
</template>

<script>
import PxHeader from "@/components/PxHeader";
import PxFooter from "@/components/PxFooter";

export default {
  name: 'App',

  components: {
    PxHeader,
    PxFooter,
  },

  data() {
    return {
      links: [
        {
          title:'BTC',
          to: { name: 'coin-detail', params: { id: 'bitcoin' } }
        },
        {
          title:'ETH',
          to: { name: 'coin-detail', params: { id: 'ethereum' } }
        },
        {
          title:'XRP',
          to: { name: 'coin-detail', params: { id: 'ripple' } }
        },
      ],
    }
  },
}
</script>

<style></style>
