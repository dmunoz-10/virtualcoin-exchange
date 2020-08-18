<template>
  <div class="flex-col">
    <div class="flex justify-center">
      <bounce-loader :loading="isLoading" :color="'#68d391'" :size="100" />
    </div>

    <template v-if="asset.id && !isLoading">
      <div class="flex flex-col sm:flex-row justify-around items-center">
        <div class="flex flex-col items-center">
          <img
            class="w-20 h-20 mr-5"
            :src="`https://static.coincap.io/assets/icons/${asset.symbol.toLowerCase()}@2x.png`"
            :alt="asset.name"
          />
          <h1 class="text-5xl">
            {{ asset.name }}
            <small class="sm:mr-2 text-gray-500">{{ asset.symbol }}</small>
          </h1>
        </div>

        <div class="my-10 flex flex-col">
          <ul>
            <li class="flex justify-between">
              <b class="text-gray-600 mr-10 uppercase">Ranking</b>
              <span>#{{ asset.rank }}</span>
            </li>
            <li class="flex justify-between">
              <b class="text-gray-600 mr-10 uppercase">Actual price</b>
              <span>{{ asset.priceUsd | dollar }}</span>
            </li>
            <li class="flex justify-between">
              <b class="text-gray-600 mr-10 uppercase">Lowest price</b>
              <span>{{ min | dollar }}</span>
            </li>
            <li class="flex justify-between">
              <b class="text-gray-600 mr-10 uppercase">Highest price</b>
              <span>{{ max | dollar }}</span>
            </li>
            <li class="flex justify-between">
              <b class="text-gray-600 mr-10 uppercase">Average price</b>
              <span>{{ avg | dollar }}</span>
            </li>
            <li class="flex justify-between">
              <b class="text-gray-600 mr-10 uppercase">24Hs Varation</b>
              <span>{{ asset.changePercent24Hr | percent }}</span>
            </li>
          </ul>
        </div>

        <div class="my-10 sm:mt-0 flex flex-col justify-center text-center">
          <button
            @click="toggleConverter"
            class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounder"
          >
            {{ fromUsd ? `USD to ${asset.symbol}` : `${asset.symbol} to USD` }}
          </button>

          <div class="flex flex-row my-5">
            <label class="w-full" for="convertValue">
              <input
                v-model="convertValue"
                id="convertValue"
                type="number"
                min="0"
                class="text-center bg-white focus:outiline-none focus:shadow-outline border border-gray-300 rounded-lg py-2 px-4 block w-full appearance-none leading-normal"
                :placeholder="fromUsd ? 'USD' : asset.symbol"
              />
            </label>
          </div>

          <span class="text-xl">{{ convertResult }} {{ fromUsd ? asset.symbol : 'USD' }}</span>
        </div>
      </div>

      <line-chart
        class="my-10"
        :colors="['orange']"
        :min="min"
        :max="max"
        :download="true"
        prefix="$ "
        thousands=","
        :data="history.map(h => [h.date, parseFloat(h.priceUsd).toFixed(2)])"
      />

      <h3 class="text-xl my-10">Best exchanges offers</h3>
      <table>
        <tr
          class="border-b"
          v-for="market in markets"
          :key="`${market.exchangeId}-${market.priceUsd}`"
        >
          <td>
            <b>{{ market.exchangeId }}</b>
          </td>
          <td>{{ market.priceUsd | dollar }}</td>
          <td>{{ market.baseSymbol }} / {{ market.quoteSymbol }}</td>
          <td>
            <px-button
              :is-loading="market.isLoading || false"
              v-if="market.url === undefined"
              @click="getWebSite(market)"
            >
              <slot>Get Url</slot>
            </px-button>
            <span v-else-if="market.url === null">It seems there isn't url :(</span>
            <a 
              v-else 
              class="hover:cursor-pointer hover:underline text-green-600"
              :href="market.url"
              target="_blanck"
            >
              {{ market.url }}
            </a>
          </td>
        </tr>
      </table>
    </template>
  </div>
</template>

<script>
import api from "@/api";
import PxButton from "@/components/PxButton";

export default {
  name: "CoinDetail",

  components: {
    PxButton,
  },
  
  data() {
    return {
      asset: {},
      history: [],
      markets: [],
      isLoading: false,
      fromUsd: true,
      convertValue: null,
    }
  },

  computed: {
    convertResult() {
      if (!this.convertValue) { return 0 }

      const result = this.fromUsd
      ? this.convertValue / this.asset.priceUsd
      : this.convertValue * this.asset.priceUsd;

      return result.toFixed(4);
    },

    min() {
      return Math.min(
        ...this.history.map(h => parseFloat(h.priceUsd).toFixed(2))
      )
    },

    max() {
      return Math.max(
        ...this.history.map(h => parseFloat(h.priceUsd).toFixed(2))
      )
    },

    avg() {
      return this.history.reduce((a, b) => a + parseFloat(b.priceUsd), 0) / this.history.length;
    },
  },

  created() {
    this.getCoin()
  },

  methods: {
    getCoin() {
      this.isLoading = true;
      const id = this.$route.params.id;

      Promise.all([
        api.getAsset(id),
        api.getAssetHistory(id),
        api.getMarkets(id),
      ])
      .then(([asset, history, markets]) => {
        this.asset = asset;
        this.history = history;
        this.markets = markets;
      })
      .finally(() => this.isLoading = false);
    },


    getWebSite(exchange) {
      this.$set(exchange, 'isLoading', true);
      return api.getExchange(exchange.exchangeId).then(res => {
        if (res) {
          this.$set(exchange, 'url', res.exchangeUrl);
        } else {
          this.$set(exchange, 'url', null);
        }
      })
      .finally(() => this.$set(exchange, 'isLoading', false));
    },

    toggleConverter() {
      this.fromUsd = !this.fromUsd;
    },
  },

  watch: {
    $route() {
      this.getCoin();
    }
  },
}
</script>

<style scoped>
td {
  padding: 10px;
  text-align: center;
}
</style>
