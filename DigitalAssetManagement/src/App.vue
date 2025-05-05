<script setup>
import { ref, computed } from 'vue'
import Dashboard from './components/Dashboard.vue'
import { assets, categories } from './data/assetData.js'
import './assets/app.css';

// Use a ref for the raw assets data
const rawAssets = ref(assets)

const assetData = ref({
  assets: rawAssets,
  categories: categories,
  departments: computed(() => [...new Set(rawAssets.value.map(asset => asset.department))]),
  statusOptions: ['In Use', 'Storage', 'Under Repair', 'Disposal'],
  totalValue: computed(() => rawAssets.value.reduce((sum, asset) => sum + asset.value, 0)),
  assetCount: computed(() => rawAssets.value.length)
})

const updateAsset = (updatedAsset) => {
  const index = rawAssets.value.findIndex(a => a.id === updatedAsset.id)
  if (index !== -1) {
    // Update the asset properties based on what's provided in updatedAsset
    
    // Handle department update
    if (updatedAsset.newDepartment) {
      rawAssets.value[index].department = updatedAsset.newDepartment
    }
    
    // Handle value update
    if (updatedAsset.value !== undefined) {
      rawAssets.value[index].value = updatedAsset.value
      
      // Store reason if provided
      if (updatedAsset.valueChangeReason) {
        rawAssets.value[index].valueChangeReason = updatedAsset.valueChangeReason
      }
    }

    // Handle value update
    if (updatedAsset.warrantyExpiry !== undefined) {
      rawAssets.value[index].warrantyExpiry = updatedAsset.warrantyExpiry
      
      // Store reason if provided
      if (updatedAsset.valueChangeReason) {
        rawAssets.value[index].valueChangeReason = updatedAsset.valueChangeReason
      }
    }
    
    // Handle purchase date updates
    if (updatedAsset.purchaseDate) {
      rawAssets.value[index].purchaseDate = updatedAsset.purchaseDate
    }

    // NOTE: You can add more property updates here as needed
  }
}
</script>

<template>
  <el-container class="app-container">
    <!-- HEADER -->
    <el-header height="60px" class="app-header">
      <div class="header-content">
        <div class="logo-section">
          <img src="/images/companyLogo.png" class="logo" alt="Company Logo">
        </div>
        <h1 class="title">DIGITAL ASSET MANAGEMENT SYSTEM</h1>
      </div>
    </el-header>

    <!-- MAIN CONTENT -->
    <el-main class="app-main">
      <Dashboard :assetData="assetData" @update-asset="updateAsset" />
    </el-main>
  </el-container>
</template>
