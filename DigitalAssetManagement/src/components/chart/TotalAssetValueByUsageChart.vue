<template>
  <div class="chart-container">
    <h1>Total Asset Value by Usage Type</h1>
    <apexchart
      type="pie"
      :options="chartOptions"
      :series="series"
      width="500"
    />

    <button @click="showModal = true" class="edit-button">Edit Usage Type</button>

    <!-- Modal -->
    <div v-if="showModal">
      <div class="modal-content">
        <EditUsageType
          :assets="currentAssets"
          @update="handleAssetUpdate"
          @close="showModal = false"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import VueApexCharts from 'vue3-apexcharts'
import { assets } from '../../data/assetData'
import EditUsageType from '../modal/EditUsageType.vue'

export default {
  name: 'AssetValueChart',
  components: {
    apexchart: VueApexCharts,
    EditUsageType
  },
  setup() {
    const showModal = ref(false)
    const currentAssets = ref([...assets]) // Make a reactive copy

    // Make all chart data computed from currentAssets
    const usageMap = computed(() => {
      const map = {}
      currentAssets.value.forEach(asset => {
        const usage = asset.usage || 'Unassigned'
        map[usage] = (map[usage] || 0) + (asset.value || 0)
      })
      return map
    })

    const series = computed(() => Object.values(usageMap.value))
    
    const chartOptions = computed(() => ({
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
    }))

    const handleAssetUpdate = (updatedAssets) => {
      currentAssets.value = updatedAssets // This will trigger all computed properties
      showModal.value = false
    }

    return {
      chartOptions,
      series,
      showModal,
      currentAssets,
      handleAssetUpdate
    }
  }
}
</script>

<style scoped>
.chart-container {
  padding: 20px;
}
h1 {
  margin-bottom: 20px;
}
.edit-button {
  margin-top: 20px;
  padding: 8px 16px;
  background-color: #3b82f6;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}
.edit-button:hover {
  background-color: #2563eb;
}
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
.modal-content {
  background: white;
  padding: 30px;
  border-radius: 10px;
  width: 80vw;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.3);
}
</style>
