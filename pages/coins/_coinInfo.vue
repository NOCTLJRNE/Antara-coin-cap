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
            <v-card-title>{{symbolToName[id]}}</v-card-title>
            <v-card-text v-if="!loaded">Loading...</v-card-text>
            <usefulLinks
              v-else-if="loaded && coinInfoData.additional_data"
              :additionalData="{...coinInfoData.additional_data}"
            />
            <v-card-text v-else>Nothing here :[</v-card-text>
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
                  <td>{{maxSupply}}</td>
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
      <h3>
        {{id.toUpperCase()}} CHARTS
        <span v-if="!loaded">Loading...</span>
        <span v-if="noChartsData">Data not found :[</span>
      </h3>
      <!-- <chartkmd v-if="loaded" :chartdata="testdata" :options="options" /> -->
      <!-- <chartkmd v-if="loaded" :chartdata="coinPriceChartData" :options="options" />
      <chartkmd v-if="loaded" :chartdata="coinMarketCapChartData" :options="options" />
      <chartkmd v-if="loaded" :chartdata="coinVolumeChartData" :options="options" />-->
      <apexchart
        v-if="loaded"
        width="100%"
        height="350"
        type="line"
        :options="apexoptionsprice"
        :series="apexseriesprice"
      ></apexchart>
      <apexchart
        v-if="loaded"
        width="100%"
        height="350"
        type="line"
        :options="apexoptionscap"
        :series="apexseriescap"
      ></apexchart>
      <apexchart
        v-if="loaded"
        width="100%"
        height="350"
        type="line"
        :options="apexoptionsvolume"
        :series="apexseriesvolume"
      ></apexchart>
    </div>
  </div>
</template>

<script>
import chartkmd from '~/components/chartkmd.vue'
import usefulLinks from '~/components/usefulLinks.vue'
import axios from 'axios'
// import VueApexCharts from 'vue-apexcharts'
import { percentage } from '~/src/percentage.js'
import { currency } from '~/src/currency.js'
import colors from '~/node_modules/vuetify/lib/util/colors'

export default {
  components: {
    chartkmd,
    usefulLinks,
    // apexchart: VueApexCharts
    apexchart: () => import('vue-apexcharts')
  },
  mounted() {
    this.updatePrice()
    this.interval = setInterval(this.updatePrice, 60000)
  },
  data() {
    return {
      id: this.$route.params.coinInfo,
      symbolToName: {
        kmd: 'Komodo',
        dex: 'DEX',
        arrr: 'Pirate',
        vrsc: 'Verus Coin',
        rfox: 'RedFox Labs',
        oot: 'Utrum',
        zexo: 'Zaddex',
        k64: 'Komodore64',
        zilla: 'ChainZilla',
        unity: 'SuperNet',
        koin: 'Koinon',
        bet: 'BET',
        dsec: 'DSEC',
        bntn: 'BNTN',
        dion: 'DION',
        our: 'OUR',
        etomic: 'ETOMIC',
        sec: 'SEC',
        glxt: 'GLXT',
        wmc: 'WLC',
        chain: 'ChainMakers',
        pgt: 'PGT',
        hodl: 'HODL',
        bots: 'Bots',
        coqui: 'CoquiCash',
        axo: 'AXO',
        crypto: 'Crypto',
        pangea: 'Pangea',
        mgw: 'MGW',
        ceal: 'CEAL',
        ccl: 'CCL',
        ksb: 'KSB',
        iln: 'ILN',
        kv: 'KV',
        revs: 'REVS',
        btch: 'BTCH',
        rick: 'Rick',
        mshark: 'MShark',
        jumblr: 'JUMBLR',
        ninja: 'Ninja',
        mesh: 'Mesh',
        kmdice: 'KMDice',
        prlpay: 'PRLPay'
      },
      // imgPathArray: {
      //   Komodo: './img/kmd.png', // work
      //   DEX: './img/dex.png',
      //   Pirate: './img/arrr.png',
      //   'Verus Coin': './img/vrsc.png', // work
      //   'RedFOX Labs': './img/rfox.png',
      //   Utrum: './img/oot.png',
      //   Zaddex: './img/zexo.png',
      //   Komodore64: './img/k64.png',
      //   Koinon: './img/koin.png',
      //   ChainZilla: './img/zilla.png'
      // },
      // priceAPI: {
      //   Komodo: 'http://95.217.44.58/api/v1/charts/kmd', // work
      //   DEX: 'http://95.217.44.58/api/v1/charts/dex',
      //   Pirate: 'http://95.217.44.58/api/v1/charts/pirate',
      //   'Verus Coin': 'http://95.217.44.58/api/v1/charts/vrsc', // work
      //   'RedFOX Labs': 'http://95.217.44.58/api/v1/charts/rfox',
      //   Utrum: 'http://95.217.44.58/api/v1/charts/oot',
      //   Zaddex: 'http://95.217.44.58/api/v1/charts/zexo',
      //   Komodore64: 'http://95.217.44.58/api/v1/charts/k64',
      //   Koinon: 'http://95.217.44.58/api/v1/charts/koin',
      //   ChainZilla: 'http://95.217.44.58/api/v1/charts/chain'
      // },
      // infoAPI: {
      //   Komodo: 'http://95.217.44.58/api/v1/tickers/kmd', // work
      //   DEX: 'http://95.217.44.58/api/v1/tickers/dex',
      //   Pirate: 'http://95.217.44.58/api/v1/tickers/pirate',
      //   'Verus Coin': 'http://95.217.44.58/api/v1/tickers/vrsc', // work
      //   'RedFOX Labs': 'http://95.217.44.58/api/v1/tickers/rfox',
      //   Utrum: 'http://95.217.44.58/api/v1/tickers/oot',
      //   Zaddex: 'http://95.217.44.58/api/v1/tickers/zexo',
      //   Komodore64: 'http://95.217.44.58/api/v1/tickers/k64',
      //   Koinon: 'http://95.217.44.58/api/v1/tickers/koin',
      //   ChainZilla: 'http://95.217.44.58/api/v1/tickers/chain'
      // },

      // priceAPI: {
      //   kmd: 'http://95.217.44.58/api/v1/charts/kmd', // work
      //   dex: 'http://95.217.44.58/api/v1/charts/dex',
      //   arrr: 'http://95.217.44.58/api/v1/charts/pirate',
      //   vrsc: 'http://95.217.44.58/api/v1/charts/vrsc', // work
      //   rfox: 'http://95.217.44.58/api/v1/charts/rfox',
      //   oot: 'http://95.217.44.58/api/v1/charts/oot',
      //   zexo: 'http://95.217.44.58/api/v1/charts/zexo',
      //   k64: 'http://95.217.44.58/api/v1/charts/k64',
      //   koin: 'http://95.217.44.58/api/v1/charts/koin',
      //   zilla: 'http://95.217.44.58/api/v1/charts/chain'
      // },
      // infoAPI: {
      //   kmd: 'http://95.217.44.58/api/v1/tickers/kmd', // work
      //   dex: 'http://95.217.44.58/api/v1/tickers/dex',
      //   arrr: 'http://95.217.44.58/api/v1/tickers/pirate',
      //   vrsc: 'http://95.217.44.58/api/v1/tickers/vrsc', // work
      //   rfox: 'http://95.217.44.58/api/v1/tickers/rfox',
      //   oot: 'http://95.217.44.58/api/v1/tickers/oot',
      //   zexo: 'http://95.217.44.58/api/v1/tickers/zexo',
      //   k64: 'http://95.217.44.58/api/v1/tickers/k64',
      //   koin: 'http://95.217.44.58/api/v1/tickers/koin',
      //   zilla: 'http://95.217.44.58/api/v1/tickers/chain'
      // },
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
      let coin = ''
      if (this.id === 'arrr') {
        coin = 'pirate'
      } else if (this.id === 'unity') {
        coin = 'supernet'
      } else {
        coin = this.id
      }
      // let priceAPIurl = this.priceAPI[this.id]
      // let infoAPIurl = this.infoAPI[this.id]
      let priceAPIurl = `http://95.217.44.58/api/v1/charts/${coin}`
      let infoAPIurl = `http://95.217.44.58/api/v1/tickers/${coin}`
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
      // this.$myInjectedFunction('test plugins')
    }
  },
  computed: {
    imgPath() {
      // let path = this.imgPathArray[this.id]
      // return require('' + path)
      return require(`./img/${this.id}.png`)
    },
    noChartsData() {
      return this.loaded && !this.pricedataraw
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
        labels: this.dateComputed,
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
        labels: this.dateComputed,
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
    maxSupply() {
      return this.coinInfoData.ticker.max_supply
        ? this.coinInfoData.ticker.max_supply
        : '???'
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
    },
    apexoptionsprice() {
      return {
        colors: [colors.green.accent3],
        chart: {
          foreColor: '#373d3f',
          id: 'apex-price',
          animations: {
            initialAnimation: {
              enabled: false
            }
          }
        },
        // dataLabels: {
        //   style: {
        //     colors: ['#FFFFFF']
        //   }
        // },
        grid: {
          // row: {
          //   colors: ['#FFFFFF']
          // },
          // column: {
          //   colors: ['#FFFFFF']
          // }
        },
        stroke: {
          show: true,
          // curve: 'smooth',
          // lineCap: 'butt',
          // colors: undefined,
          width: 2
          // dashArray: 0,
        },
        title: {
          text: 'Price',
          // align: 'left',
          // margin: 10,
          // offsetX: 0,
          // offsetY: 0,
          // floating: false,
          style: {
            fontSize: '16px',
            color: colors.green.accent3
            // color: '#76FF03'
          }
        },
        tooltip: {
          enabled: true,
          theme: 'dark',
          x: {
            show: true,
            format: 'dd MMM HH:mm'
          }
        },
        xaxis: {
          type: 'datetime',
          labels: {
            style: {
              colors: colors.grey.lighten1
              // colors: '#BDBDBD'
            }
          }
          // categories: [...this.dateComputed]
        },
        yaxis: {
          labels: {
            formatter: function(value) {
              // return '$' + value.toFixed(3)
              return currency(value, '$', 3)
            },
            // formatter: currency(value,'$', 3)
            style: {
              color: colors.grey.lighten1
              // colors: '#BDBDBD'
            }
          }
        }
      }
    },
    apexseriesprice() {
      return [
        {
          name: 'Price',
          // data: [...this.coinVolumeComputed]
          data: this.pricedataraw
        }
      ]
    },
    apexoptionscap() {
      return {
        colors: [colors.cyan.accent2],
        chart: {
          foreColor: '#373d3f',
          id: 'apex-cap',
          animations: {
            initialAnimation: {
              enabled: false
            }
          }
        },

        stroke: {
          show: true,

          width: 2
        },
        title: {
          text: 'Market Cap',

          style: {
            fontSize: '16px',
            color: colors.cyan.accent2
          }
        },
        tooltip: {
          enabled: true,
          theme: 'dark',
          x: {
            show: true,
            format: 'dd MMM HH:mm'
          }
        },
        xaxis: {
          type: 'datetime',
          labels: {
            style: {
              colors: colors.grey.lighten1
            }
          }
        },
        yaxis: {
          labels: {
            formatter: (value) => currency(value, '$', 0),

            style: {
              color: colors.grey.lighten1
            }
          }
        }
      }
    },
    apexseriescap() {
      return [
        {
          name: 'Market Cap',
          // data: [...this.coinVolumeComputed]
          data: this.marketcapdataraw
        }
      ]
    },
    apexoptionsvolume() {
      return {
        colors: [colors.orange.accent2],
        chart: {
          foreColor: '#373d3f',
          id: 'apex-volume',
          animations: {
            initialAnimation: {
              enabled: false
            }
          }
        },

        stroke: {
          show: true,

          width: 2
        },
        title: {
          text: 'Volume',

          style: {
            fontSize: '16px',
            color: colors.orange.accent2
          }
        },
        tooltip: {
          enabled: true,
          theme: 'dark',
          x: {
            show: true,
            format: 'dd MMM HH:mm'
          }
        },
        xaxis: {
          type: 'datetime',
          labels: {
            style: {
              colors: colors.grey.lighten1
            }
            // format: 'dd/MM'
          }
        },
        yaxis: {
          labels: {
            formatter: (value) => currency(value, '$', 0),

            style: {
              color: colors.grey.lighten1
            }
          }
        }
      }
    },
    apexseriesvolume() {
      return [
        {
          name: 'Volume',
          // data: [...this.coinVolumeComputed]
          data: this.volumedataraw
        }
      ]
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