<template>
  <!-- <v-container>
    <v-row>
  <v-col cols="6">
  <v-card>-->
  <div>
    <ul style="list-style: none;">
      <!-- <li class="listItems01" v-for="(item, i) in itemsCoins" :key="i">
      <v-icon v-text="item.icon"></v-icon>

      <a :href="allLinksObjectComputed[i].link">{{allLinksObjectComputed[i].link}}</a>
      </li>-->
      <li
        class="listItems01 d-none d-md-block"
        v-for="(item, i) in allAdditionalDataComputed"
        :key="i"
      >
        <v-icon v-text="item.icon"></v-icon>
        <a :href="allAdditionalDataComputed[i].link">{{allAdditionalDataComputed[i].text}}</a>
      </li>
    </ul>
    <v-row>
      <v-col
        cols="3"
        class="d-table-row d-md-none d-flex justify-center"
        v-for="(item, i) in allAdditionalDataComputed"
        :key="i"
      >
        <v-icon v-text="item.icon"></v-icon>
        <a :href="allAdditionalDataComputed[i].link">{{allAdditionalDataComputed[i].text}}</a>
      </v-col>
    </v-row>
  </div>

  <!-- </v-card>
  </v-col>
    </v-row>
  </v-container>-->
</template>

<script>
export default {
  props: {
    additionalData: {
      type: Object
    }
  },
  data() {
    return {
      links: 'USEFUL LINK',
      itemsCoins: [
        { text: 'Website', icon: 'mdi-link-variant' },
        { text: 'Explorer', icon: 'mdi-magnify' },
        { text: 'Chat', icon: 'mdi-discord' },
        { text: 'Source Code', icon: 'mdi-github-circle' }
      ]
      // allLinks: [
      //   this.additionalData.links.homepage[0],
      //   this.additionalData.links.blockchain_site[0],
      //   this.additionalData.links.chat_url[0],
      //   this.additionalData.links.repos_url.github[0]
      // ]
    }
  },
  computed: {
    homePage() {
      return this.additionalData.links.homepage
        ? this.additionalData.links.homepage[0]
        : 'https://www.google.com'
    },
    explorer() {
      return this.additionalData.links.blockchain_site
        ? this.additionalData.links.blockchain_site[0]
        : 'https://www.google.com'
    },
    chat() {
      return this.additionalData.links.chat_url
        ? this.additionalData.links.chat_url[0]
        : 'https://www.google.com'
    },
    github() {
      return this.additionalData.links.repos_url.github
        ? this.additionalData.links.repos_url.github[0]
        : 'https://www.google.com'
    },

    itemsCoinsComputed() {
      return this.itemsCoins.map((item) => item)
    },
    allLinksComputed() {
      return [this.homePage, this.explorer, this.chat, this.github]
    },
    allLinksObjectComputed() {
      return [
        { link: this.homePage },
        { link: this.explorer },
        { link: this.chat },
        { link: this.github }
      ]
    },
    allAdditionalDataComputed() {
      var allObjects = this.allLinksObjectComputed
      return this.itemsCoins.map(function(item, index) {
        let temp = { ...item, ...allObjects[index] }
        return temp
      })
    }
  }
}
</script>

<style scoped>
.listItems01 {
  margin-bottom: 5px;
}
</style>