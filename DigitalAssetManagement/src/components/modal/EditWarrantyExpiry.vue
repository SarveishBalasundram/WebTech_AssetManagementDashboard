<template>
  <el-dialog :model-value="visible" title="Update Warranty Expiry" width="60%" @close="resetForm"
    @update:model-value="handleVisibilityChange">
    <el-form :model="formData" label-position="top" ref="formRef">
      <el-row :gutter="20">
        <el-col :span="24">
          <el-form-item label="Department" prop="department">
            <el-select v-model="currentDepartment" placeholder="Select department" style="width: 100%"
              @change="handleDepartmentChange">
              <el-option v-for="dept in departments" :key="dept" :label="dept" :value="dept" />
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row :gutter="20">
        <el-col :span="24">
          <el-form-item label="Select Asset" prop="selectedAssetId">
            <el-select v-model="selectedAssetId" placeholder="Select asset" style="width: 100%"
              @change="handleAssetChange" :disabled="!currentDepartment">
              <el-option v-for="asset in filteredAssets" :key="asset.id" :label="asset.name" :value="asset.id" />
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="Name" prop="name">
            <el-input v-model="formData.name" disabled />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="Category" prop="category">
            <el-input v-model="formData.category" disabled />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="Current Warranty Expiry" prop="currentWarrantyExpiry">
            <el-input v-model="formData.currentWarrantyExpiry" disabled />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="New Warranty Expiry" prop="newWarrantyExpiry">
            <el-date-picker
              v-model="formData.newWarrantyExpiry"
              type="date"
              placeholder="Select new date"
              format="YYYY-MM-DD"
              value-format="YYYY-MM-DD"
              style="width: 100%"
              :disabled-date="disablePastDates"
            />
          </el-form-item>
        </el-col>
      </el-row>

      <el-divider v-if="formData.currentWarrantyExpiry" content-position="left">Warranty Timeline</el-divider>
      
      <el-row v-if="formData.currentWarrantyExpiry" :gutter="20">
        <el-col :span="24">
          <div class="warranty-timeline">
            <el-steps :active="getTimelineActive()" finish-status="success">
              <el-step title="Purchase Date" :description="formData.purchaseDate || 'N/A'" />
              <el-step title="Current Warranty" :description="formData.currentWarrantyExpiry || 'N/A'" />
              <el-step title="New Warranty" :description="formData.newWarrantyExpiry || 'Select date'" />
            </el-steps>
          </div>
        </el-col>
      </el-row>
    </el-form>

    <template #footer>
      <span class="dialog-footer">
        <el-button @click="close">Cancel</el-button>
        <el-button type="primary" @click="submitForm" :disabled="!isFormValid">
          Submit
        </el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { ElMessage } from 'element-plus'

const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  },
  departments: {
    type: Array,
    default: () => []
  },
  assets: {
    type: Array,
    default: () => []
  },
  selectedAsset: {
    type: Object,
    default: null
  }
})

const emit = defineEmits(['update:visible', 'submit'])

const formRef = ref(null)
const currentDepartment = ref('')
const selectedAssetId = ref('')
const filteredAssets = ref([])

const formData = ref({
  name: '',
  category: '',
  purchaseDate: '',
  currentWarrantyExpiry: '',
  newWarrantyExpiry: ''
})

// Compute if form is valid
const isFormValid = computed(() => {
  return selectedAssetId.value &&
    formData.value.newWarrantyExpiry &&
    formData.value.newWarrantyExpiry !== formData.value.currentWarrantyExpiry
})

const handleVisibilityChange = (value) => {
  emit('update:visible', value)
}

const handleDepartmentChange = (dept) => {
  // Filter assets by selected department
  filteredAssets.value = props.assets.filter(asset => asset.department === dept)
  selectedAssetId.value = ''
  resetFormData()
}

const handleAssetChange = (assetId) => {
  const selectedAsset = props.assets.find(asset => asset.id === assetId)
  if (selectedAsset) {
    formData.value = {
      name: selectedAsset.name || '',
      category: selectedAsset.category || '',
      purchaseDate: selectedAsset.purchaseDate || '',
      currentWarrantyExpiry: selectedAsset.warrantyExpiry || '',
      newWarrantyExpiry: selectedAsset.warrantyExpiry || ''
    }
  }
}

const resetFormData = () => {
  formData.value = {
    name: '',
    category: '',
    purchaseDate: '',
    currentWarrantyExpiry: '',
    newWarrantyExpiry: ''
  }
}

const close = () => {
  currentDepartment.value = ''
  selectedAssetId.value = ''
  filteredAssets.value = []
  resetFormData()
  emit('update:visible', false)
}

const resetForm = () => {
  formRef.value?.resetFields()
  close()
}

const getTimelineActive = () => {
  if (!formData.value.currentWarrantyExpiry) return 0
  if (!formData.value.newWarrantyExpiry) return 1
  return 2
}

const disablePastDates = (date) => {
  // Disable dates before today
  return date < new Date(new Date().setHours(0, 0, 0, 0))
}

const submitForm = () => {
  if (!selectedAssetId.value) {
    ElMessage.warning('Please select an asset')
    return
  }

  if (formData.value.newWarrantyExpiry === formData.value.currentWarrantyExpiry) {
    ElMessage.warning('Please select a different warranty expiry date')
    return
  }

  // Find the original asset to preserve its properties
  const originalAsset = props.assets.find(asset => asset.id === selectedAssetId.value)

  // Create updatedAsset with ALL original properties plus the updated ones
  const updatedAsset = {
    ...originalAsset,  // Preserve all original properties
    warrantyExpiry: formData.value.newWarrantyExpiry
  }

  emit('submit', updatedAsset)
  ElMessage.success('Warranty expiry date updated successfully')
  close()
}

// Initialize form if a selected asset was provided
watch(() => props.visible, (newVisible) => {
  if (newVisible && props.selectedAsset) {
    currentDepartment.value = props.selectedAsset.department
    handleDepartmentChange(currentDepartment.value)
    selectedAssetId.value = props.selectedAsset.id
    handleAssetChange(selectedAssetId.value)
  }
}, { immediate: true })
</script>

<style scoped>
.dialog-footer {
  display: flex;
  justify-content: flex-end;
}

.warranty-timeline {
  margin-top: 10px;
  margin-bottom: 20px;
}
</style>