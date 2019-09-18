<template>
  <div>
    <v-card>
      <v-card-title>{{id}}</v-card-title>
      <v-card-text v-if="loaded">
        Homepage:
        <a :href="homePageComputed">{{homePageComputed}}</a>
      </v-card-text>

      <img :src="imgPath" height="50" width="50" />
    </v-card>
    <div class="container">
      <h3>{{id}} CHART</h3>
      <!-- <chartkmd v-if="loaded" :chartdata="testdata" :options="options" /> -->
      <chartkmd v-if="loaded" :chartdata="coinPriceChartData" :options="options" />
      <chartkmd v-if="loaded" :chartdata="coinMarketCapChartData" :options="options" />
      <chartkmd v-if="loaded" :chartdata="coinVolumeChartData" :options="options" />
    </div>
  </div>
</template>

<script>
import chartkmd from '~/components/chartkmd.vue'
import axios from 'axios'
export default {
  components: {
    chartkmd
  },
  mounted() {
    let priceAPIurl = this.priceAPI[this.id]
    let infoAPIurl = this.infoAPI[this.id]

    axios
      .all([axios.get(priceAPIurl), axios.get(infoAPIurl)])
      .then((response) => {
        this.pricedataraw = response[0].data.prices
        this.marketcapdataraw = response[0].data.market_caps
        this.volumedataraw = response[0].data.total_volumes
        this.coinInfoData = response[1].data
      })
      .catch((error) => {
        console.log(error[0])
        console.log(error[1])
        this.errored = true
      })
      .finally(() => (this.loaded = true))
    // axios
    //   .get(priceAPIurl)
    //   .then((response) => {
    //     this.pricedataraw = response.data.prices
    //     this.marketcapdataraw = response.data.market_caps
    //     this.volumedataraw = response.data.total_volumes
    //   })
    //   .catch((error) => {
    //     console.log(error)
    //     this.errored = true
    //   })
    //   .finally(() => (this.loaded = true))

    // .get(infoAPIurl)
    // .then((response) => {
    //   this.coinInfoData = response
    // })
    // .catch((error) => {
    //   console.log(error)
    //   this.errored = true
    // })
    // .finally(() => (this.loaded = true))
  },
  data() {
    return {
      id: this.$route.params.coinInfo,
      imgPathArray: {
        Komodo: './picture/kmd.png', // work
        DEX: './picture/dex.png',
        Pirate: './picture/arrr.png',
        'Verus Coin': './picture/vrsc.png', // work
        'RedFOX Labs': './picture/rfox.png',
        Utrum: './picture/oot.png',
        Zaddex: './picture/zexo.png',
        Komodore64: './picture/k64.png',
        Koinon: './picture/koin.png',
        ChainZilla: './picture/zilla.png'
      },
      priceAPI: {
        Komodo: 'http://95.217.44.58/api/v1/charts/kmd', // work
        DEX: 'http://95.217.44.58/api/v1/charts/dex',
        Pirate: 'http://95.217.44.58/api/v1/charts/pirate',
        'Verus Coin': 'http://95.217.44.58/api/v1/charts/vrsc', // work
        'RedFOX Labs': 'http://95.217.44.58/api/v1/charts/kmd',
        Utrum: 'http://95.217.44.58/api/v1/charts/oot',
        Zaddex: 'http://95.217.44.58/api/v1/charts/zexo',
        Komodore64: 'http://95.217.44.58/api/v1/charts/k64',
        Koinon: 'http://95.217.44.58/api/v1/charts/koin',
        ChainZilla: 'http://95.217.44.58/api/v1/charts/chain'
      },
      infoAPI: {
        Komodo: 'http://95.217.44.58/api/v1/tickers/kmd', // work
        DEX: 'http://95.217.44.58/api/v1/tickers/dex',
        Pirate: 'http://95.217.44.58/api/v1/tickers/pirate',
        'Verus Coin': 'http://95.217.44.58/api/v1/tickers/vrsc', // work
        'RedFOX Labs': 'http://95.217.44.58/api/v1/tickers/kmd',
        Utrum: 'http://95.217.44.58/api/v1/tickers/oot',
        Zaddex: 'http://95.217.44.58/api/v1/tickers/zexo',
        Komodore64: 'http://95.217.44.58/api/v1/tickers/k64',
        Koinon: 'http://95.217.44.58/api/v1/tickers/koin',
        ChainZilla: 'http://95.217.44.58/api/v1/tickers/chain'
      },
      loaded: false,
      coinInfoData: null,
      pricedataraw: null,
      marketcapdataraw: null,
      volumedataraw: null,
      errored: false,
      options: {
        responsive: true,
        maintainAspectRatio: false,
        legend: {
          display: true
        }
      }
    }
  },
  methods: {
    updatePrice() {
      axios
        .all([axios.get(priceAPIurl), axios.get(infoAPIurl)])
        .then((response) => {
          this.pricedataraw = response[0].data.prices
          this.marketcapdataraw = response[0].data.market_caps
          this.volumedataraw = response[0].data.total_volumes
          this.coinInfoData = response[1].data
        })
        .catch((error) => {
          console.log(error[0])
          console.log(error[1])
          this.errored = true
        })
        .finally(() => (this.loaded = true))

      // axios
      //   .get('http://95.217.44.58/api/v1/charts/kmd')
      //   .then((response) => {
      //     this.pricedataraw = response.data.prices
      //     this.marketcapdataraw = response.data.market_caps
      //     this.volumedataraw = response.data.total_volumes
      //     // console.log("coin cap response:", response.data);
      //   })
      //   .catch((error) => {
      //     // console.log(error);
      //     this.errored = true
      //   })
      //   .finally(() => (this.loaded = true))
    }
  },
  computed: {
    imgPath() {
      //   return this.imgPathArray[this.id]
      let path = this.imgPathArray[this.id]
      return require('' + path)
    },
    timeComputed() {
      return (
        this.pricedataraw
          // .slice(0, 50)
          .map((element) => (element[0] / 1000).toFixed(0))
      )
    },
    dateComputed() {
      return this.pricedataraw.map(function(element) {
        var date = new Date(element[0])
        const months = [
          'Jan',
          'Feb',
          'Mar',
          'Apr',
          'May',
          'Jun',
          'Jul',
          'Aug',
          'Sep',
          'Oct',
          'Nov',
          'Dec'
        ]
        return (
          date.getFullYear() +
          '-' +
          months[date.getMonth()] +
          '-' +
          date.getDate()
        )
      })
    },
    coinPriceComputed() {
      return (
        this.pricedataraw
          // .slice(0, 50)
          .map((element) => element[1])
      )
    },
    coinPriceChartData() {
      return {
        labels: this.dateComputed,
        datasets: [
          {
            label: `${this.id} Price`,
            backgroundColor: '#00ffff',
            data: this.coinPriceComputed,
            pointRadius: 0
          }
        ]
      }
    },
    coinMarketCapComputed() {
      return (
        this.marketcapdataraw
          // .slice(0, 50)
          .map((element) => element[1])
      )
    },
    coinMarketCapChartData() {
      return {
        labels: this.timeComputed,
        datasets: [
          {
            label: `${this.id} Market Cap`,
            backgroundColor: '#ffff00',
            data: this.coinMarketCapComputed,
            pointRadius: 0
          }
        ]
      }
    },
    coinVolumeComputed() {
      return (
        this.volumedataraw
          // .slice(0, 50)
          .map((element) => element[1])
      )
    },
    coinVolumeChartData() {
      return {
        labels: this.timeComputed,
        datasets: [
          {
            label: `${this.id} Volume`,
            backgroundColor: '#ff00ff',
            data: this.coinVolumeComputed,
            pointRadius: 0
          }
        ]
      }
    },
    homePageComputed() {
      return this.coinInfoData.additional_data
        ? this.coinInfoData.additional_data.links.homepage[0]
        : 'Nothing here :('
    }
  }
}
</script>

<style>
</style>