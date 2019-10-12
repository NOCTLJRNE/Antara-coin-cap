<template>
  <!-- <div>
    <p>Name:{{ name }}, Rank: {{rank}}, MarketCap: {{marketCap}}, Sym: {{ symbol }}, Price: ${{ price }}, Vol: {{volume}}, CSupply:{{supply}}, Change 24h : {{change24h}}</p>
  </div>-->

  <v-container fluid>
    <v-card>
      <v-row>
        <v-col cols="2" md="1">
          <p class="text-center">{{ rank }}</p>
        </v-col>
        <!-- <v-col cols="2">
          <div class="text-center d-flex">
            <v-img :src="img_src" height="50" width="50"></v-img>
            {{ name }}
          </div>
        </v-col>-->
        <v-col cols="2" md="1">
          <v-img :src="imgPath" height="50" width="50"></v-img>
          <!-- <v-img :src="require(""+imgPathArray.KMD)" height="50" width="50"></v-img> -->
        </v-col>
        <v-col cols="2" md="1">
          <!-- <nuxt-link :to="`/coins/${name}`">{{ name }}</nuxt-link> -->
          <nuxt-link :to="`/coins/${symbolLowerCase}`">{{ name }}</nuxt-link>
        </v-col>
        <v-col cols="2" md="2">
          <p class="text-center">{{marketCap | currency('$',0)}}</p>
        </v-col>
        <v-col cols="2" md="1">
          <p class="text-center">{{ price | currency('$',3) }}</p>
        </v-col>
        <v-col cols="2" md="2" class="d-sm-none d-md-block">
          <p class="text-center">{{volume | currency('$',0)}}</p>
        </v-col>
        <v-col cols="2" md="2" class="d-sm-none d-md-block">
          <p class="text-center">{{supply}}</p>
        </v-col>
        <v-col cols="2" md="1">
          <p class="text-center" :class="[change24h < 0 ? red : green]">{{change24h | percentage}}</p>
        </v-col>
        <v-col cols="2" md="1" class="d-sm-none d-md-block">
          <p class="text-center">...</p>
        </v-col>
      </v-row>
    </v-card>
  </v-container>
</template>

<script>
import { percentage } from '~/src/percentage.js'
import { currency } from '~/src/currency.js'
export default {
  props: {
    coin: {
      required: true
    }
  },
  data() {
    return {
      //img_src: require(this.imgPath), //  KSB, Ninja /tickers/{symbol} except /tickers/{name} for pirate, supernet
      // imgPathArray: {
      //   KMD: './img/kmd.png',
      //   DEX: './img/dex.png',
      //   ARRR: './img/arrr.png',
      //   VRSC: './img/vrsc.png',
      //   RFOX: './img/rfox.png',
      //   OOT: './img/oot.png',
      //   ZEXO: './img/zexo.png',
      //   K64: './img/k64.png',
      //   KOIN: './img/koin.png',
      //   ZILLA: './img/zilla.png'
      // },
      red: 'red--text',
      green: 'green--text'
    }
  },
  computed: {
    imgSize() {
      switch (this.$vuetify.breakpoint.name) {
        case 'xs':
          return '25'
        case 'sm':
          return '25'
        case 'md':
          return '50'
        case 'lg':
          return '50'
        case 'xl':
          return '50'
      }
    },
    name() {
      return this.coin.ticker.name
        ? this.coin.ticker.name
        : this.coin.ticker.symbol
    },
    rank() {
      return this.coin.ticker.rank
    },
    supply() {
      return this.coin.supply.toFixed(0)
    },
    price() {
      return this.coin.ticker.quotes.USD.price.toFixed(3)
    },
    symbol() {
      return this.coin.ticker.symbol
    },
    symbolLowerCase() {
      return this.coin.ticker.symbol.toLowerCase()
    },
    volume() {
      return this.coin.ticker.quotes.USD.volume_24h.toFixed(3)
    },
    marketCap() {
      return this.coin.ticker.quotes.USD.market_cap
    },
    change24h() {
      return this.coin.ticker.quotes.USD.percent_change_24h
    },
    imgPath() {
      // let path = this.imgPathArray[this.symbol]
      // return require('' + path)
      return require(`./img/${this.symbolLowerCase}.png`)
    }
  },
  filters: {
    currency: currency,
    percentage: percentage
  }
}
</script>

<style></style>
