<template>
  <v-card>
      <v-card-title>{{id}}</v-card-title>
      </v-card>
</template>

<script>
export default {
    data(){
        return {
            id: this.$route.params.coinInfo
        }
    }
}
</script>

<style>

</style>