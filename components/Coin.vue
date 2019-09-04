<template>
  <!-- <div>
    <p>Name:{{ name }}, Rank: {{rank}}, MarketCap: {{marketCap}}, Sym: {{ symbol }}, Price: ${{ price }}, Vol: {{volume}}, CSupply:{{supply}}, Change 24h : {{change24h}}</p>
  </div>-->

  <v-container fluid>
    <v-card>
      <v-row>
        <v-col cols="1">
          <p class="text-center">{{ rank }}</p>
        </v-col>
        <!-- <v-col cols="2">
          <div class="text-center d-flex">
            <v-img :src="img_src" height="50" width="50"></v-img>
            {{ name }}
          </div>
        </v-col>-->
        <v-col cols="1">
          <v-img :src="imgPath" height="50" width="50"></v-img>
          <!-- <v-img :src="require(""+imgPathArray.KMD)" height="50" width="50"></v-img> -->
        </v-col>
        <v-col cols="1">
          <nuxt-link :to="`/coins/${name}`">{{ name }}</nuxt-link>
        </v-col>
        <v-col cols="2">
          <p class="text-center">{{marketCap}}</p>
        </v-col>
        <v-col cols="1">
          <p class="text-center">{{ price }}</p>
        </v-col>
        <v-col cols="2">
          <p class="text-center">{{volume}}</p>
        </v-col>
        <v-col cols="2">
          <p class="text-center">{{supply}}</p>
        </v-col>
        <v-col cols="1">
          <p class="text-center" :class="[change24h < 0 ? red : green]">{{change24h}}</p>
        </v-col>
        <v-col cols="1">
          <p class="text-center">Graph</p>
        </v-col>
      </v-row>
    </v-card>
  </v-container>
</template>

<script>
export default {
  props: {
    coin: {
      required: true
    },
    index: {
      required: true
    }
  },
  data() {
    return {
      //img_src: require(this.imgPath),
      imgPathArray: {
        KMD: './kmd.png',
        DEX: './dex.png',
        ARRR: './arrr.png',
        VRSC: './vrsc.png',
        RFOX: './rfox.png',
        OOT: './oot.png',
        ZEXO: './zexo.png',
        K64: './k64.png',
        KOIN: './koin.png',
        ZILLA: './zilla.png'
      },
      red: 'red--text',
      green: 'green--text'
    }
  },
  computed: {
    name() {
      return this.coin.ticker.name
    },
    rank() {
      return this.coin.ticker.rank
    },
    supply() {
      return this.coin.supply.toFixed(3)
    },
    price() {
      return this.coin.ticker.quotes.USD.price.toFixed(3)
    },
    symbol() {
      return this.coin.ticker.symbol
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
      let path = this.imgPathArray[this.symbol]
      return require('' + path)
    }
  }
}
</script>

<style></style>
