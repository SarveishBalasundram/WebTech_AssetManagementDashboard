<template>
    <div class="modal-overlay">
      <div class="modal-content">
        <h2>Edit Usage Type</h2>
  
        <!-- Filter bar -->
        <div class="filters">
          <input v-model="search" placeholder="Search asset..." />
          <select v-model="categoryFilter">
            <option value="">All Categories</option>
            <option v-for="cat in categories" :key="cat">{{ cat }}</option>
          </select>
          <select v-model="deptFilter">
            <option value="">All Departments</option>
            <option v-for="dept in departments" :key="dept">{{ dept }}</option>
          </select>
          <select v-model="usageFilter">
            <option value="">All Usage Types</option>
            <option v-for="u in usageTypes" :key="u">{{ u }}</option>
          </select>
        </div>
  
        <!-- Editable Table -->
        <table>
          <thead>
            <tr>
              <th>Asset Name</th>
              <th>Category</th>
              <th>Department</th>
              <th>Status</th>
              <th>Usage Type</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="asset in filteredAssets" :key="asset.id">
              <td>{{ asset.name }}</td>
              <td>{{ asset.category }}</td>
              <td>{{ asset.department }}</td>
              <td>{{ asset.status }}</td>
              <td>
                <select v-model="asset.usage">
                  <option v-for="type in usageTypes" :key="type" :value="type">{{ type }}</option>
                </select>
              </td>
            </tr>
          </tbody>
        </table>
  
        <!-- Buttons -->
        <div class="modal-actions">
          <button class="save" @click="saveChanges">Save Changes</button>
          <button class="close" @click="closeModal">Close</button>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, watch } from 'vue';
  
  const props = defineProps({
    assets: {
      type: Array,
      required: true,
      default: () => []
    }
  });
  
  const emit = defineEmits(['update', 'close']);

  const localAssets = ref([...props.assets]);

  watch(() => props.assets, (newVal) => {
  localAssets.value = [...newVal];
  }, { deep: true });
  
  const search = ref('');
  const categoryFilter = ref('');
  const deptFilter = ref('');
  const usageFilter = ref('');
  
  const usageTypes = ['Personal', 'Shared', 'Critical', 'Backup', 'Testing'];
  const categories = [...new Set(props.assets.map(a => a.category))];
  const departments = [...new Set(props.assets.map(a => a.department))];
  
  const filteredAssets = computed(() => {
    return props.assets.filter(asset =>
      asset.name.toLowerCase().includes(search.value.toLowerCase()) &&
      (categoryFilter.value === '' || asset.category === categoryFilter.value) &&
      (deptFilter.value === '' || asset.department === deptFilter.value) &&
      (usageFilter.value === '' || asset.usage === usageFilter.value)
    );
  });
  
  function saveChanges() {
    emit('update', [...props.assets]);
    emit('close');
  }
  
  function closeModal() {
    emit('close');
  }
  </script>
  
  <style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 9999;
  }
  
  .modal-content {
    background: white;
    padding: 25px;
    border-radius: 10px;
    width: 60vw; /* 60% of screen width */
    max-height: 90vh;
    overflow-y: auto;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  }
  
  .filters {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 15px;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 15px;
  }
  
  th, td {
    padding: 10px;
    border-bottom: 1px solid #ccc;
    text-align: left;
  }
  
  .modal-actions {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
  }
  
  button.save {
    background-color: #4CAF50;
    color: white;
    padding: 8px 16px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  button.close {
    background-color: #f44336;
    color: white;
    padding: 8px 16px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  </style>
  