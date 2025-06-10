<template> 
  <div class="flex items-center justify-between mb-4">
    <!-- Department Filter Dropdown -->
    <el-select
      v-model="selectedDepartments"
      multiple
      collapse-tags
      collapse-tags-tooltip
      placeholder="All Departments"
      style="width: 50%; margin-right:16px;"
      clearable
    >
      <el-option
        label="All Departments"
        :value="allDepartments"
        @click="selectAllDepartments"
        key="all-departments"
      />
      <el-option
        v-for="dept in allDepartments"
        :key="dept"
        :label="dept"
        :value="dept"
      />
    </el-select>

    <!-- Edit Button -->
    <el-button type="primary" @click="showEditModal = true" :icon="Edit">
      Edit
    </el-button>
  </div>

  <!-- Asset Summary Table -->
  <el-table
    :data="statusSummary"
    stripe
    border
    class="black-text-table mt-6"
    style="width: 100%; table-layout: auto;"
  >
    <el-table-column prop="status" label="Status" />
    <el-table-column prop="count" label="Total Assets" />
  </el-table>

  <!-- Donut Chart -->
  <div style="margin-top: 30px; width: 100%; display: flex; justify-content: center;">
    <apexchart
      type="donut"
      :options="chartOptions"
      :series="chartSeries"
      height="400"
    />
  </div>

  <!-- Edit Modal -->
  <EditAssetStatus
    v-model="showEditModal"
    :assets="assets"
    :departments="departments"
    :categories="categories"
    :usage-types="usageTypes"
    @update-assets="handleAssetUpdate"
  />
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { Edit } from '@element-plus/icons-vue'
import ApexCharts from 'vue3-apexcharts'
import EditAssetStatus from '../modal/EditAssetStatus.vue'
import assetService from '../../api/assetService'

// State
const showEditModal = ref(false)
const assets = ref([])
const departments = ref([])
const categories = ref([])
const usageTypes = ref([]) // optional: you can fetch this if applicable

const selectedDepartments = ref([])

// Fetch data on mount
onMounted(async () => {
  try {
    assets.value = await assetService.getAssets()
    departments.value = await assetService.getDepartments()
    categories.value = await assetService.getCategories()
    // optionally set usageTypes.value here too
  } catch (err) {
    console.error('Failed to load initial data:', err)
  }
})

// Utility: All departments from fetched data
const allDepartments = computed(() =>
  [...new Set(assets.value.map(asset => asset.department))].filter(Boolean)
)

// Select all departments
const selectAllDepartments = () => {
  selectedDepartments.value = [...allDepartments.value]
}

// Filter assets by selected departments
const filteredAssets = computed(() => {
  if (selectedDepartments.value.length === 0) {
    return assets.value
  }
  return assets.value.filter(asset =>
    selectedDepartments.value.includes(asset.department)
  )
})

// Count assets by status
const calculateStatusCounts = () => {
  const counts = {
    'In Use': 0,
    'Storage': 0,
    'Under Repair': 0,
    'Disposal': 0
  }

  filteredAssets.value.forEach(asset => {
    const status = asset.status || 'Unknown'
    if (counts[status] !== undefined) {
      counts[status]++
    }
  })

  return counts
}

// Table summary
const statusSummary = computed(() => {
  const counts = calculateStatusCounts()
  return Object.entries(counts).map(([status, count]) => ({
    status,
    count
  }))
})

// Chart series
const chartSeries = computed(() => {
  const counts = calculateStatusCounts()
  return [
    counts['In Use'],
    counts['Storage'],
    counts['Under Repair'],
    counts['Disposal']
  ]
})

// Donut chart options
const chartOptions = computed(() => ({
  chart: {
    id: 'asset-status-donut-chart',
    toolbar: { show: false }
  },
  labels: ['In Use', 'Storage', 'Under Repair', 'Disposal'],
  plotOptions: {
    pie: {
      donut: {
        size: '60%'
      }
    }
  },
  colors: ['#FF6384', '#36A2EB', '#FFCE56', '#4BC0C0'],
  responsive: [
    {
      breakpoint: 600,
      options: {
        chart: {
          width: '100%'
        },
        legend: {
          position: 'bottom'
        }
      }
    }
  ]
}))

// Refresh assets after edit
const handleAssetUpdate = async (updatedAssets) => {
  try {
    // Re-fetch fresh asset list after updates
    assets.value = await assetService.getAssets()
  } catch (error) {
    console.error('Failed to refresh assets after update:', error)
  }
}
</script>

<script>
export default {
  components: {
    apexchart: ApexCharts
  }
}
</script>

<style scoped>
.mt-6 {
  margin-top: 1.5rem;
}

.black-text-table :deep(.el-table__cell) {
  color: #444444 !important;
}

.black-text-table :deep(th.el-table__cell) {
  font-weight: bold;
  color: #5c5858;
}
</style>
