<template>
    <div>
      <h1>Total Asset Value by Usage Type</h1>
      <apexchart
        type="pie"
        :options="chartOptions"
        :series="series"
        width="500"
      />
    </div>
  </template>
  
  <script>
  import { ref, computed } from 'vue'
  import VueApexCharts from 'vue3-apexcharts'
  import { assets } from '../../data/assetData' // Adjust the path as needed
  
  export default {
    name: 'AssetValueChart',
    components: {
      apexchart: VueApexCharts
    },
    setup() {
      const usageMap = computed(() => {
        const map = {};
        assets.forEach(asset => {
          const usage = asset.usage;
          if (map[usage]) {
            map[usage] += asset.value;
          } else {
            map[usage] = asset.value;
          }
        });
        return map;
      });
  
      const chartOptions = ref({
        labels: Object.keys(usageMap.value),
        legend: {
          position: 'bottom'
        },
        responsive: [{
          breakpoint: 480,
          options: {
            chart: {
              width: 300
            },
            legend: {
              position: 'bottom'
            }
          }
        }]
      });
  
      const series = ref(Object.values(usageMap.value));
  
      return {
        chartOptions,
        series
      };
    }
  }
  </script>
  
  <style scoped>
  h1 {
    margin-bottom: 20px;
  }
  </style>
  