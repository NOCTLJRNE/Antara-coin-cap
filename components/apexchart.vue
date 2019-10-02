<template></template>

<script>
import VueApexCharts from 'vue-apexcharts'
Vue.use(VueApexCharts)
Vue.component('apexchart', VueApexCharts)
export default {
  name: 'Vue Chart',
  data: function() {
    return {
      chartOptions: {
        chart: {
          id: 'vuechart-example'
        },
        xaxis: {
          categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998]
        }
      },
      series: [
        {
          name: 'Vue Chart',
          data: [30, 40, 45, 50, 49, 60, 70, 81]
        }
      ]
    }
  }
}
</script>

<style>
</style>