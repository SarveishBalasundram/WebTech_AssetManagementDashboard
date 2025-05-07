<template>
  <el-dialog :model-value="visible" title="Edit Asset Value" width="60%" @close="resetForm"
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
          <el-form-item label="Current Value (RM)" prop="currentValue">
            <el-input v-model="formData.currentValue" disabled />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="New Value (RM)" prop="newValue">
            <el-input-number v-model="formData.newValue" :min="0" :step="100" style="width: 100%" />
          </el-form-item>
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
import { ref, computed, watch } from 'vue'
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
  currentValue: '',
  newValue: 0
})

// Compute if form is valid
const isFormValid = computed(() => {
  return selectedAssetId.value &&
    formData.value.newValue > 0 &&
    formData.value.newValue !== formData.value.currentValue
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
      currentValue: selectedAsset.value || 0,
      newValue: selectedAsset.value || 0
    }
  }
}

const resetFormData = () => {
  formData.value = {
    name: '',
    category: '',
    currentValue: '',
    newValue: 0
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

const submitForm = () => {
  if (!selectedAssetId.value) {
    ElMessage.warning('Please select an asset')
    return
  }

  if (formData.value.newValue === formData.value.currentValue) {
    ElMessage.warning('Please change the asset value')
    return
  }

  // Find the original asset to preserve its properties
  const originalAsset = props.assets.find(asset => asset.id === selectedAssetId.value)

  // Create updatedAsset with ALL original properties plus the updated ones
  const updatedAsset = {
    ...originalAsset,  // Preserve all original properties
    value: formData.value.newValue
  }

  emit('submit', updatedAsset)
  ElMessage.success('Asset value updated successfully')
  close()
}
</script>

<style scoped>
.dialog-footer {
  display: flex;
  justify-content: flex-end;
}
</style>