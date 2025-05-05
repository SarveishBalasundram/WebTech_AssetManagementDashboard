<template>
    <el-dialog
      :model-value="modelValue"
      title="Edit Asset Status"
      width="80%"
      @update:model-value="val => emit('update:modelValue', val)"
    >
      <!-- Filters (Element Plus Grid Layout) -->
      <el-row :gutter="20" class="mb-4">
        <el-col :span="7">
          <el-input
            v-model="searchQuery"
            placeholder="Search asset name..."
            clearable
          />
        </el-col>
        <el-col :span="5">
          <el-select
            v-model="selectedCategory"
            placeholder="Filter by category"
            clearable
            style="width: 100%"
          >
            <el-option
              v-for="category in categories"
              :key="category"
              :label="category"
              :value="category"
            />
          </el-select>
        </el-col>
        <el-col :span="5">
          <el-select
            v-model="selectedDepartment"
            placeholder="Filter by department"
            clearable
            style="width: 100%"
          >
            <el-option
              v-for="dept in departments"
              :key="dept"
              :label="dept"
              :value="dept"
            />
          </el-select>
        </el-col>
  
        <!-- New Usage Type Filter -->
        <el-col :span="5">
          <el-select
            v-model="selectedUsageType"
            placeholder="Filter by Usage Type"
            clearable
            style="width: 100%"
          >
            <el-option label="Personal" value="Personal" />
            <el-option label="Shared" value="Shared" />
            <el-option label="Testing" value="Testing" />
            <el-option label="Backup" value="Backup" />
            <el-option label="Critical" value="Critical" />
          </el-select>
        </el-col>
      </el-row>
  
      <!-- Table -->
      <div class="asset-list-container" style="margin-top: 20px;">
      <el-table :data="filteredAssets" style="width: 100%" border height="400" class="bold-table-headers">
        <el-table-column prop="name" label="Asset Name" />
        <el-table-column prop="category" label="Category" />
        <el-table-column prop="department" label="Department" />
        <el-table-column prop="usage" label="Usage Type" />
        <el-table-column prop="status" label="Status">
          <template #default="scope">
            <el-select
              v-model="scope.row.status"
              placeholder="Select"
              @change="onStatusChange(scope.row)"
              size="small"
              style="width: 120px"
            >
              <el-option label="In Use" value="In Use" />
              <el-option label="Storage" value="Storage" />
              <el-option label="Under Repair" value="Under Repair" />
              <el-option label="Disposal" value="Disposal" />
            </el-select>
          </template>
        </el-table-column>
      </el-table>
      </div>
  
      <template #footer>
        <el-button @click="emit('update:modelValue', false)">Close</el-button>
      </template>
    </el-dialog>
  </template>
  
  <script setup>
  import { ref, computed } from 'vue'
  
  const props = defineProps({
    modelValue: Boolean,
    assets: Array,
    categories: {
      type: Array,
      default: () => []
    },
    departments: Array
  })
  
  const emit = defineEmits(['update:modelValue', 'filter-assets'])
  
  const searchQuery = ref('')
  const selectedCategory = ref('')
  const selectedDepartment = ref('')
  const selectedUsageType = ref('')
  
  const filteredAssets = computed(() => {
    return props.assets.filter(asset => {
      const matchesSearch = asset.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      const matchesCategory = selectedCategory.value
        ? asset.category === selectedCategory.value
        : true
      const matchesDepartment = selectedDepartment.value
        ? asset.department === selectedDepartment.value
        : true
      const matchesUsageType = selectedUsageType.value
        ? asset.usage === selectedUsageType.value
        : true  // This matches if no usageType filter is applied
      
      return matchesSearch && matchesCategory && matchesDepartment && matchesUsageType
    })
  })
  
  function onStatusChange(asset) {
    // Handle status change (if needed)
    console.log('Status changed for asset:', asset)
  }
  </script>
  
<style scoped>
/*Table list title style*/
:deep(.bold-table-headers th.el-table__cell) {
  font-weight: bold;
  color: #494747;
  background-color: rgb(238, 248, 245);
}
</style>