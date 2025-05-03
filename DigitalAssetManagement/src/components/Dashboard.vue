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

defineProps({
  assetData: {
    type: Object,
    required: true
  }
})

defineEmits(['update-asset', 'add-asset'])
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
        <el-card 
          shadow="hover" 
          :body-style="{ padding: '20px', display: 'flex', flexDirection: 'column', height: '100%' }"
          class="border-left-4"
          :style="{ borderLeftColor: `var(--el-color-${stat.color})` }"
        >
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
        <el-card 
          shadow="hover" 
          :body-style="{ padding: '20px', display: 'flex', flexDirection: 'column', height: '100%' }"
          class="border-left-4"
          :style="{ borderLeftColor: `var(--el-color-${stat.color})` }"
        >
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
      <el-col :xs="24" :sm="12">
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '300px' }">
          <h3 class="text-lg font-semibold mb-4">Asset Distribution</h3>
          <!-- Graph placeholder -->
          <div class="h-full flex items-center justify-center bg-gray-100 rounded">
            <span class="text-gray-400">Chart Placeholder</span>
          </div>
        </el-card>
      </el-col>
      <el-col :xs="24" :sm="12">
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '300px' }">
          <h3 class="text-lg font-semibold mb-4">Status Overview</h3>
          <!-- Graph placeholder -->
          <div class="h-full flex items-center justify-center bg-gray-100 rounded">
            <span class="text-gray-400">Chart Placeholder</span>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <el-row :gutter="20" class="mb-6">
      <el-col :xs="24" :sm="12" :md="8" v-for="(graph, index) in 3" :key="index">
        <el-card shadow="hover" :body-style="{ padding: '20px', height: '300px' }">
          <h3 class="text-lg font-semibold mb-4">Graph {{ index + 3 }}</h3>
          <!-- Graph placeholder -->
          <div class="h-full flex items-center justify-center bg-gray-100 rounded">
            <span class="text-gray-400">Graph {{ index + 3 }} Placeholder</span>
          </div>
        </el-card>
      </el-col>
    </el-row>

     <!-- Asset Status Summary Card -->
 <div class="mb-6">
    <el-card shadow="hover" :body-style="{ padding: '20px' }">
      <div class="flex justify-between items-center mb-4">
        <!-- Left: Document Icon + Title -->
        <div class="flex items-center gap-2">
          <h3 class="text-lg font-semibold m-0">Asset Status Summary</h3>
 </div>

    <!-- Table Cards - 2 per row -->
    <el-row :gutter="20" class="mb-6">
      <el-col :xs="24" :sm="12" v-for="(table, index) in 2" :key="index">
        <el-card shadow="hover" :body-style="{ padding: '20px' }">
          <h3 class="text-lg font-semibold mb-4">Table {{ index + 1 }}</h3>
          <!-- Table placeholder -->
          <div class="h-64 flex items-center justify-center bg-gray-100 rounded">
            <span class="text-gray-400">Table {{ index + 1 }} Placeholder</span>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>


