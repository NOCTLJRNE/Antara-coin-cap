<template>
  <div>
    <!-- <v-container class="grey lighten-5">
      <v-row no-gutters>
        <v-col cols="12" sm="6" md="8">
          <v-card class="pa-2" outlined tile>.col-12 .col-sm-6 .col-md-8</v-card>
        </v-col>
        <v-col cols="6" md="4">
          <v-card class="pa-2" outlined tile>.col-6 .col-md-4</v-card>
        </v-col>
      </v-row>
    </v-container>-->
    <v-container>
      <v-row>
        <v-col cols="3">
          <v-card>
            <img :src="imgPath" height="50" width="50" />
            <v-card-title>{{id}}</v-card-title>
            <usefulLinks v-if="loaded" :additionalData="{...coinInfoData.additional_data}" />
          </v-card>
        </v-col>
        <v-col cols="9">
          <v-card v-if="loaded">
            <v-card-title>
              {{ price | currency('$',3) }}
              <span
                :class="[change24h < 0 ? red : green]"
              >&nbsp;({{change24h | percentage}})</span>
            </v-card-title>
            <v-simple-table>
              <thead>
                <tr>
                  <th class="text-left font-weight-bold title">Market Cap</th>
                  <th class="text-left font-weight-bold title">Volume (24h)</th>
                  <th class="text-left font-weight-bold title">Circulating Supply</th>
                  <th class="text-left font-weight-bold title">Max Supply</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td>{{marketCap | currency('$',0)}}</td>
                  <td>{{volume | currency('$',0)}}</td>
                  <td>{{supply}}</td>
                  <td>200,000,000</td>
                </tr>
              </tbody>
            </v-simple-table>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
    <!-- <v-card>
      <img :src="imgPath" height="50" width="50" />
      <v-card-title>{{id}}</v-card-title>

      <usefulLinks v-if="loaded" :additionalData="{...coinInfoData.additional_data}" />
    </v-card>-->
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
import usefulLinks from '~/components/usefulLinks.vue'
import axios from 'axios'
import { percentage } from '~/src/percentage.js'
import { currency } from '~/src/currency.js'
export default {
  components: {
    chartkmd,
    usefulLinks
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
        'RedFOX Labs': 'http://95.217.44.58/api/v1/charts/rfox',
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
        'RedFOX Labs': 'http://95.217.44.58/api/v1/tickers/rfox',
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
      },
      item: 1,
      items: [
        { text: 'Real-Time', icon: 'mdi-clock' },
        { text: 'Audience', icon: 'mdi-account' },
        { text: 'Conversions', icon: 'mdi-flag' }
      ],
      red: 'red--text',
      green: 'green--text'
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
    },
    name() {
      return this.coinInfoData.ticker.name
    },
    rank() {
      return this.coinInfoData.ticker.rank
    },
    supply() {
      return this.coinInfoData.supply.toFixed(3)
    },
    price() {
      return this.coinInfoData.ticker.quotes.USD.price
    },
    symbol() {
      return this.coinInfoData.ticker.symbol
    },
    volume() {
      return this.coinInfoData.ticker.quotes.USD.volume_24h
    },
    marketCap() {
      return this.coinInfoData.ticker.quotes.USD.market_cap
    },
    change24h() {
      return this.coinInfoData.ticker.quotes.USD.percent_change_24h
    },
    additionalData() {
      let temp = { ...this.coinInfoData.additional_data }
      console.log(temp)
      return
      {
        temp
      }
    }
  },
  filters: {
    currency: currency,
    percentage: percentage
  }
}
</script>

<style>
</style>