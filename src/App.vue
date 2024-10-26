<script setup lang="ts">
import Sidebar from '@/components/SidebarComponent.vue';
import GoogleMap from '@/components/GoogleMapComponent.vue';
import { ref } from 'vue';

const googleMapRef = ref(null);

const handleDraw = (type: string) => {
  if (googleMapRef.value) {
    googleMapRef.value.handleDraw(type);
  }
};

const handleEdit = () => {
  if (googleMapRef.value) {
    googleMapRef.value.setDrawingMode(null);
  }
};

const saveForms = () => {
  if (googleMapRef.value) {
    googleMapRef.value.saveShapes(); 
  }
};

</script>

<template>
  <div class="app-container">
    <Sidebar :onDraw="handleDraw" :onEdit="handleEdit" :onSave="saveForms" class="sidebar" />
    <GoogleMap ref="googleMapRef" />
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
}

.sidebar {
  width: 20vw;
  padding: 2rem;
}

.router-view {
  flex: 1;
}
</style>
