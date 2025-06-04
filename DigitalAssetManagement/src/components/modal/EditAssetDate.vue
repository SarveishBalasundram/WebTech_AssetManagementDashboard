<template>
    <!-- Dialog for updating asset purchase date -->
    <el-dialog :model-value="visible" title="Update Asset Purchase Date" width="50%" @close="resetForm"
        @update:model-value="handleVisibilityChange">
        <!-- Form for selecting asset and purchase date -->
        <el-form :model="formData" label-position="top" ref="formRef">
            <el-row :gutter="20">
                <el-col :span="24">
                    <el-form-item label="Select Asset" prop="selectedAssetId">
                        <el-select v-model="selectedAssetId" placeholder="Select asset" style="width: 100%"
                            @change="handleAssetChange">
                            <el-option v-for="asset in props.assets" :key="asset.id" :label="asset.name"
                                :value="asset.id" />
                        </el-select>
                    </el-form-item>
                </el-col>
            </el-row>

            <el-row :gutter="20">
                <el-col :span="24">
                    <el-form-item label="Purchase Date" prop="purchaseDate">
                        <el-date-picker v-model="formData.purchaseDate" type="date" placeholder="Select date"
                            style="width: 100%" />
                    </el-form-item>
                </el-col>
            </el-row>
        </el-form>

        <!-- Dialog footer with action buttons -->
        <template #footer>
            <span class="dialog-footer">
                <el-button @click="close">Cancel</el-button>
                <el-button type="primary" @click="submitForm">
                    Submit
                </el-button>
            </span>
        </template>
    </el-dialog>
</template>

<script setup>
import { ref, computed } from 'vue' // Import Vue 3 composition API utilities
import { ElMessage } from 'element-plus' // Import Element Plus message component for notifications

// Define component props for dialog visibility and asset data
const props = defineProps({
    visible: {
        type: Boolean,
        default: false, // Dialog is hidden by default
    },
    assets: {
        type: Array,
        default: () => [], // Default to empty array if no assets provided
    },
})

// Define custom events for visibility updates and form submission
const emit = defineEmits(['update:visible', 'submit'])

// Reactive reference to the form element for resetting fields
const formRef = ref(null)
// Reactive variable to store selected asset ID
const selectedAssetId = ref('')
// Reactive object to store form data (purchase date)
const formData = ref({
    purchaseDate: '', // Initialize as empty string
})

// Handle dialog visibility changes and emit update
const handleVisibilityChange = value => {
    emit('update:visible', value)
}

// Update purchase date when an asset is selected
const handleAssetChange = assetId => {
    const selectedAsset = props.assets.find(asset => asset.id === assetId)
    if (selectedAsset) {
        formData.value.purchaseDate = selectedAsset.purchaseDate ? new Date(selectedAsset.purchaseDate) : new Date()
    }
}

// Reset form fields and close dialog
const resetForm = () => {
    selectedAssetId.value = ''
    formData.value.purchaseDate = ''
    formRef.value?.resetFields()
    close()
}

// Close dialog by emitting visibility update
const close = () => {
    emit('update:visible', false)
}

// Handle form submission
const submitForm = () => {
    if (!selectedAssetId.value) {
        ElMessage.warning('Please select an asset')
        return
    }

    if (!formData.value.purchaseDate) {
        ElMessage.warning('Please select a purchase date')
        return
    }

    const updatedAsset = {
        id: selectedAssetId.value,
        purchaseDate: formData.value.purchaseDate.toISOString().split('T')[0], // Format as YYYY-MM-DD
    }

    // Emit updated asset data to parent component
    emit('submit', updatedAsset)
    resetForm()
}
</script>

<style scoped>
.dialog-footer {
    display: flex;
    justify-content: flex-end;
}
</style>