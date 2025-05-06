<template>
   
      <!-- Department Filter and Edit Button -->
      <div class="flex items-center justify-between mb-4">
        <el-select
          v-model="selectedDepartments"
          multiple
          collapse-tags
          collapse-tags-tooltip
          placeholder="All Departments"
          style="width: 50%; margin-right:16px;"
          @change="handleDepartmentChange"
        >
          <template #tag>
            <span v-if="isAllSelected" style="color: #888;">&nbsp; All Departments</span>
          </template>
  
          <el-option label="All Departments" :value="'__ALL__'" />
          <el-option
            v-for="dept in allDepartments"
            :key="dept"
            :label="dept"
            :value="dept"
          />
        </el-select>
  
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
        :assets="props.assets"
        :departments="departments"
        :categories="categoryList"
        :usage-types="usageTypes"
        @update-assets="updatedAssets => emit('update-assets', updatedAssets)"
      />

  </template>
  
  <script setup>
  import { ref, computed, onMounted } from 'vue'
  import { Edit } from '@element-plus/icons-vue'
  import ApexCharts from 'vue3-apexcharts'
  import EditAssetStatus from '../modal/EditAssetStatus.vue'
  
  const props = defineProps({
    modelValue: Boolean,
    assets: Array,
    categories: {
      type: Array,
      default: () => []
    },
    departments: {
      type: Array,
      default: () => []
    },
    usageTypes: {
      type: Array,
      default: () => []
    }
  })
  
  const emit = defineEmits(['update-assets'])
  
  const showEditModal = ref(false)
  
  const categoryList = computed(() =>
    [...new Set(props.assets.map(asset => asset.category))].filter(Boolean)
  )
  
  const allDepartments = computed(() =>
    [...new Set(props.assets.map(asset => asset.department))].filter(Boolean)
  )
  
  const selectedDepartments = ref([])
  
  onMounted(() => {
    selectedDepartments.value = [...allDepartments.value]
  })
  
  const isAllSelected = computed(() =>
    selectedDepartments.value.length === allDepartments.value.length &&
    allDepartments.value.every(dept => selectedDepartments.value.includes(dept))
  )
  
  const handleDepartmentChange = (value) => {
    const all = '__ALL__'
    if (value.includes(all)) {
      if (!isAllSelected.value) {
        selectedDepartments.value = [...allDepartments.value]
      }
    } else {
      selectedDepartments.value = value
    }
  }
  
  const filteredAssets = computed(() => {
    if (selectedDepartments.value.length === 0) {
      return props.assets
    }
    return props.assets.filter(asset =>
      selectedDepartments.value.includes(asset.department)
    )
  })
  
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
  
  const statusSummary = computed(() => {
    const counts = calculateStatusCounts()
    return Object.entries(counts).map(([status, count]) => ({
      status,
      count
    }))
  })
  
  const chartSeries = computed(() => {
    const counts = calculateStatusCounts()
    return [
      counts['In Use'],
      counts['Storage'],
      counts['Under Repair'],
      counts['Disposal']
    ]
  })
  
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
  
