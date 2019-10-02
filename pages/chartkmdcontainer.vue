<template>
  <div class="container">
    <h3>KMD CHART</h3>
    <!-- <chartkmd v-if="loaded" :chartdata="testdata" :options="options" /> -->
    <chartkmd v-if="loaded" :chartdata="kmdPriceChartData" :options="options" />
    <chartkmd v-if="loaded" :chartdata="kmdMarketCapChartData" :options="options" />
    <chartkmd v-if="loaded" :chartdata="kmdVolumeChartData" :options="options" />
  </div>
</template>

<script>
import chartkmd from '~/components/chartkmd.vue'
import axios from 'axios'

export default {
  name: 'LineChartContainer',
  components: { chartkmd },
  data: () => ({
    loaded: false,
    pricedataraw: null,
    marketcapdataraw: null,
    volumedataraw: null,
    errored: false,

    // kmdpricedata: {
    //   labels: [],
    //   datasets: [
    //     {
    //       label: 'KMD Price',
    //       backgroundColor: '#00ffff',
    //       data: []
    //     }
    //   ]
    // },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      legend: {
        display: true
      }
    }
  }),
  // async mounted() {
  //   this.loaded = false
  //   try {
  //     const { userlist } = await fetch('http://95.217.44.58/api/v1/charts/kmd')
  //     this.chartdata = userlist
  //     this.loaded = true
  //     console.log(Object.keys(this.chartdata))
  //     // console.log('prices length:', this.chartdata.prices.length)
  //   } catch (e) {
  //     console.error(e)
  //   }
  // }
  mounted() {
    axios
      .get('http://95.217.44.58/api/v1/charts/kmd')
      .then((response) => {
        this.pricedataraw = response.data.prices
        this.marketcapdataraw = response.data.market_caps
        this.volumedataraw = response.data.total_volumes
        // console.log("coin cap response:", response.data);
        // for (let i = 0; i < this.pricedataraw.length; i++) {
        // for (let i = 0; i < 50; i++) {
        //   this.kmdpricedata.labels.push(
        //     (this.pricedataraw[i][0] / 1000).toFixed(0)
        //   )
        //   this.kmdpricedata.datasets[0].data.push(this.pricedataraw[i][1])
        // }
      })
      .catch((error) => {
        console.log(error)
        this.errored = true
      })
      .finally(() => (this.loaded = true))
    // this.interval = setInterval(this.updatePrice, 60000)
  },

  methods: {
    updatePrice() {
      axios
        .get('http://95.217.44.58/api/v1/charts/kmd')
        .then((response) => {
          this.pricedataraw = response.data.prices
          this.marketcapdataraw = response.data.market_caps
          this.volumedataraw = response.data.total_volumes
          // console.log("coin cap response:", response.data);
        })
        .catch((error) => {
          // console.log(error);
          this.errored = true
        })
        .finally(() => (this.loaded = true))
    }
  },
  computed: {
    timeComputed() {
      return (
        this.pricedataraw
          // .slice(0, 50)
          .map((element) => (element[0] / 1000).toFixed(0))
      )
    },
    kmdPriceComputed() {
      return (
        this.pricedataraw
          // .slice(0, 50)
          .map((element) => element[1])
      )
    },
    kmdPriceChartData() {
      return {
        labels: this.timeComputed,
        datasets: [
          {
            label: 'KMD Price',
            backgroundColor: '#00ffff',
            data: this.kmdPriceComputed,
            pointRadius: 0
          }
        ]
      }
    },
    kmdMarketCapComputed() {
      return (
        this.marketcapdataraw
          // .slice(0, 50)
          .map((element) => element[1])
      )
    },
    kmdMarketCapChartData() {
      return {
        labels: this.timeComputed,
        datasets: [
          {
            label: 'KMD Market Cap',
            backgroundColor: '#ffff00',
            data: this.kmdMarketCapComputed,
            pointRadius: 0
          }
        ]
      }
    },
    kmdVolumeComputed() {
      return (
        this.volumedataraw
          // .slice(0, 50)
          .map((element) => element[1])
      )
    },
    kmdVolumeChartData() {
      return {
        labels: this.timeComputed,
        datasets: [
          {
            label: 'KMD Volume',
            backgroundColor: '#ff00ff',
            data: this.kmdVolumeComputed,
            pointRadius: 0
          }
        ]
      }
    }
  }
}
</script>

<style>
</style>