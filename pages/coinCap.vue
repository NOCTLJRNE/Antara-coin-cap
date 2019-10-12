<template>
  <div>
    <div>
      <h1>{{testData}}</h1>
    </div>
    <v-container fluid>
      <v-card>
        <v-row>
          <v-col cols="2" md="1">
            <p class="text-center">#</p>
          </v-col>
          <v-col cols="4" md="2">
            <p class="text-center">Symbol & Name</p>
          </v-col>
          <v-col cols="2" md="2">
            <p class="text-center">Market Cap</p>
          </v-col>
          <v-col cols="2" md="1">
            <p class="text-center">Price</p>
          </v-col>
          <v-col cols="2" md="2" class="d-sm-none d-md-block">
            <p class="text-center">Volume 24h</p>
          </v-col>
          <v-col cols="2" md="2" class="d-sm-none d-md-block">
            <p class="text-center">Supply</p>
          </v-col>
          <v-col cols="2" md="1">
            <p class="text-center">Change</p>
          </v-col>
          <v-col cols="2" md="1" class="d-sm-none d-md-block">
            <p class="text-center">...</p>
          </v-col>
        </v-row>
      </v-card>
    </v-container>
    <!-- <v-container>
    <div>-->
    <v-progress-linear buffer-value="0" stream :active="loading" color="cyan accent-4"></v-progress-linear>
    <Coin v-for="(coin,index) in sortByMarketCap" v-bind:coin="coin" :key="coin.ticker.symbol" />
    <Coin v-for="(coin,index) in priceNull" v-bind:coin="coin" :key="coin.ticker.symbol" />
    <!-- </div>
    </v-container>-->
    <!-- <v-layout>
      <div>
        <Coin v-for="coin in sortByMarketCap" v-bind:coin="coin" :key="coin.ticker.symbol" />
      </div>
    </v-layout>-->
  </div>
</template>
<script>
import axios from 'axios'
import Coin from '../components/Coin.vue'
export default {
  components: {
    Coin
  },
  data() {
    return {
      testData: 'KOMODO Coin Cap',
      coinCapList: [],
      errored: false,
      loading: true
    }
  },
  mounted() {
    axios
      .get('http://95.217.44.58/api/v1/tickers')
      .then((response) => {
        this.coinCapList = response.data
        // console.log("coin cap response:", response.data);
      })
      .catch((error) => {
        // console.log(error);
        this.errored = true
      })
      .finally(() => (this.loading = false))
    this.interval = setInterval(this.updatePrice, 60000)
  },

  methods: {
    updatePrice() {
      axios
        .get('http://95.217.44.58/api/v1/tickers')
        .then((response) => {
          this.coinCapList = response.data
          // console.log("coin cap response:", response.data);
        })
        .catch((error) => {
          // console.log(error);
          this.errored = true
        })
        .finally(() => (this.loading = false))
    }
  },
  computed: {
    priceNotNull() {
      return this.coinCapList.filter(
        (coin) => coin.ticker.quotes.USD.price != '0'
      )
    },
    priceNull() {
      return this.coinCapList.filter(
        (coin) => coin.ticker.quotes.USD.price === 0
      )
    },
    sortByMarketCap() {
      return this.priceNotNull.sort(
        (a, b) =>
          b.ticker.quotes.USD.market_cap - a.ticker.quotes.USD.market_cap
      )
    },
    sortByMarketCapFull() {
      return this.priceNotNull.sort(
        (a, b) =>
          b.ticker.quotes.USD.market_cap - a.ticker.quotes.USD.market_cap
      )
    },
    symbols() {
      return this.coinCapList.map((coin) => coin.ticker.symbol)
    }
  }
}
</script>
