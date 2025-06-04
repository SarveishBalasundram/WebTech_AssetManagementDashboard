<script setup>
import { ref, onMounted } from 'vue'
import Dashboard from './components/Dashboard.vue'
import assetService from './api/assetService'
import { ElNotification } from 'element-plus'
import './assets/app.css';

const assetData = ref({
  assets: [],
  categories: [],
  departments: [],
  statusOptions: ['In Use', 'Storage', 'Under Repair', 'Disposal'],
  totalValue: 0
})

// Fetch initial data
const fetchData = async () => {
  try {
    const [assets, categories, departments] = await Promise.all([
      assetService.getAssets(),
      assetService.getCategories(),
      assetService.getDepartments()
    ])
    
    assetData.value = {
      ...assetData.value,
      assets,
      categories,
      departments,
      totalValue: assets.reduce((sum, asset) => sum + (parseFloat(asset.value) || 0), 0)
    }
  } catch (error) {
    console.error('Error loading initial data:', error)
    ElNotification({
      title: 'Error',
      message: 'Failed to load asset data',
      type: 'error'
    })
  }
}

// Update asset department
// In App.vue's updateAsset method
const updateAsset = async (updatedAsset) => {
  try {
    // Use the standard update method instead of specialized endpoint
    const updated = await assetService.updateAsset(updatedAsset.id, {
      department: updatedAsset.newDepartment
    })
    
    // Update local state
    const index = assetData.value.assets.findIndex(a => a.id === updated.id)
    if (index !== -1) {
      assetData.value.assets[index] = updated
      assetData.value.totalValue = assetData.value.assets.reduce(
        (sum, asset) => sum + (parseFloat(asset.value) || 0), 0
      )
      
      ElNotification({
        title: 'Success',
        message: 'Department updated successfully',
        type: 'success'
      })
    }
  } catch (error) {
    console.error('Error updating asset:', error)
    ElNotification({
      title: 'Error',
      message: error.response?.data?.error || 'Failed to update department',
      type: 'error'
    })
    throw error
  }
}

const updateAssetDept = async (updatedAsset) => {
  try {
    // Use the standard update method instead of specialized endpoint
    const updated = await assetService.changeAssetDepartment(updatedAsset.id, updatedAsset.newDepartment
    )
    
    // Update local state
    const index = assetData.value.assets.findIndex(a => a.id === updated.id)
    if (index !== -1) {
      assetData.value.assets[index] = updated
      assetData.value.totalValue = assetData.value.assets.reduce(
        (sum, asset) => sum + (parseFloat(asset.value) || 0), 0
      )
      
      ElNotification({
        title: 'Success',
        message: 'Department updated successfully',
        type: 'success'
      })
    }
  } catch (error) {
    console.error('Error updating asset:', error)
    ElNotification({
      title: 'Error',
      message: error.response?.data?.error || 'Failed to update department',
      type: 'error'
    })
    throw error
  }
}

// Load data when component mounts
onMounted(() => {
  fetchData()
})
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
      <Dashboard 
        :assetData="assetData" 
        @update-asset="updateAsset" 
        @update-assetDept="updateAssetDept" 
      />
    </el-main>
  </el-container>
</template>