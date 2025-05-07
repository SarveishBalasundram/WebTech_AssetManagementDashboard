<script setup>
import {
  Document,
  Tools,
  Money,
  OfficeBuilding,
  Collection,
  Plus,
  Edit
} from '@element-plus/icons-vue';
import '../assets/dashboard.css';

// Add these imports
import VueApexCharts from 'vue3-apexcharts'
import AssetDistribution from './chart/AssetDistribution.vue'
import AssetByCategory from './chart/AssetByCategory.vue'
import AssetStatusTable from './chart/AssetStatusTable.vue'
import AssetBreakdown from './chart/AssetBreakdown.vue'
import AssetValueByDept from './chart/AssetValueByDept.vue'
import WarrantyExpiryTable from './chart/WarrantyExpiryTable.vue'
import TotalAssetValueByUsageChart from './chart/TotalAssetValueByUsageChart.vue';

// Register the ApexCharts component
const apexchart = VueApexCharts
defineProps({
  assetData: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update-asset'])

// Simply forward the event to parent (App.vue)
const handleAssetUpdate = (updatedAsset) => {
  emit('update-asset', updatedAsset)
}

</script>

<template>
  <div class="dashboard">
    <!-- Dashboard Title -->
    <div class="mb-6">
      <h2 class="text-2xl font-bold text-gray-800">Dashboard</h2>
      <el-divider />
    </div>

    <!-- Summary Cards - 2 cards per row -->
    <el-row :gutter="20" class="mb-6">
      <el-col :xs="24" :sm="12" v-for="(stat, index) in [
        { title: 'Total Assets', value: assetData.assets.length, icon: Collection, color: 'primary', isCurrency: false },
        { title: 'Categories', value: assetData.categories.length, icon: Document, color: 'success', isCurrency: false }
      ]" :key="index">
        <el-card shadow="hover"
          :body-style="{ padding: '20px', display: 'flex', flexDirection: 'column', height: '100%' }"
          class="border-left-4" :style="{ borderLeftColor: `var(--el-color-${stat.color})` }">
          <el-space direction="vertical" alignment="flex-start" class="flex-grow">
            <el-icon :size="40" :color="stat.color">
              <component :is="stat.icon" />
            </el-icon>
            <div class="text-gray-500">{{ stat.title }}</div>
          </el-space>
          <div class="text-right mt-auto">
            <div class="text-4xl font-bold text-gray-800">
              {{ stat.isCurrency ? 'RM' + stat.value.toLocaleString() : stat.value }}
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <!-- Second Row of Summary Cards -->
    <el-row :gutter="20" class="mb-6">
      <el-col :xs="24" :sm="12" v-for="(stat, index) in [
        { title: 'Departments', value: assetData.departments.length, icon: OfficeBuilding, color: 'warning', isCurrency: false },
        { title: 'Total Value', value: assetData.totalValue, icon: Money, color: 'danger', isCurrency: true }
      ]" :key="index">
        <el-card shadow="hover"
          :body-style="{ padding: '20px', display: 'flex', flexDirection: 'column', height: '100%' }"
          class="border-left-4" :style="{ borderLeftColor: `var(--el-color-${stat.color})` }">
          <el-space direction="vertical" alignment="flex-start" class="flex-grow">
            <el-icon :size="40" :color="stat.color">
              <component :is="stat.icon" />
            </el-icon>
            <div class="text-gray-500">{{ stat.title }}</div>
          </el-space>
          <div class="text-right mt-auto">
            <div class="text-4xl font-bold text-gray-800">
              {{ stat.isCurrency ? 'RM' + stat.value.toLocaleString() : stat.value }}
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>


    <el-row :gutter="20" class="mb-6">
      <!-- In your dashboard.vue, replace the Asset Distribution card with this: -->
      <el-col :xs="24" :sm="12">
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '100%' }">
          <h3 class="text-lg font-semibold mb-4">Asset Distribution</h3>
          <AssetDistribution :assetData="assetData" @update-asset="handleAssetUpdate" />
          <AssetBreakdown :assetData="assetData" @update-asset="handleAssetUpdate" />
        </el-card>
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '100%' }">
          <h3 class="text-lg font-semibold mb-4">Total Asset Value by Department</h3>
          <AssetValueByDept :assetData="assetData" @update-asset="handleAssetUpdate" />
        </el-card>
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '100%' }">
          <TotalAssetValueByUsageChart :assetData="assetData" />
        </el-card>

      </el-col>

      
      <el-col :xs="24" :sm="12">
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '100%' }">
          <h3 class="text-lg font-semibold mb-4">Status Overview</h3>
          <!-- Graph placeholder -->
          <AssetByCategory :assetData="assetData" />
        </el-card>
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '100%' }">
          <h3 class="text-lg font-semibold mb-4">Asset Status Summary</h3>
          <AssetStatusTable :assets="assetData.assets" :departments="assetData.departments"
            @update-assets="assetData.assets = $event" />
        </el-card>
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '100%' }">
          <h3 class="text-lg font-semibold mb-4">Upcoming Warranty Expiry</h3>
          <WarrantyExpiryTable :assets="assetData.assets" :departments="assetData.departments"
            @update-asset="handleAssetUpdate" />
        </el-card>
        
      </el-col>
    </el-row>

  </div>
</template>
