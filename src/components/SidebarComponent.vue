<script setup lang="ts">
import { ref, defineProps } from 'vue';

const props = defineProps<{
  onDraw: (type: string) => void;
  onEdit: () => void;
  onSave: () => void;
}>();

const activeButton = ref('');

const handleButtonClick = (type: string) => {
  activeButton.value = type;
  props.onDraw(type);
};

const handleEditClick = () => {
  activeButton.value = 'EDIT';
  props.onEdit();
};
</script>

<template>
  <aside class="sidebar">
    <h2 class="sidebar-title"><i class="pi pi-map" style="font-size: 2.5rem;padding: 0.5rem;"></i>Map drawing</h2>
    <h3 class="sub-tittle">Ferramentas:</h3>
    <nav class="menu">
      <button class="menu-button" :class="{ active: activeButton === 'POLYGON' }" @click="handleButtonClick('POLYGON')">
        <i class="pi pi-pencil"></i> Polígono
      </button>
      <button class="menu-button" :class="{ active: activeButton === 'RECTANGLE' }"
        @click="handleButtonClick('RECTANGLE')">
        <i class="pi pi-expand"></i> Retângulo
      </button>
      <button class="menu-button" :class="{ active: activeButton === 'CIRCLE' }" @click="handleButtonClick('CIRCLE')">
        <i class="pi pi-circle"></i> Círculo
      </button>
      <button class="menu-button" :class="{ active: activeButton === 'MARKER' }" @click="handleButtonClick('MARKER')">
        <i class="pi pi-map-marker"></i> Marcador
      </button>
      <button class="menu-button" :class="{ active: activeButton === 'EDIT' }" @click="handleEditClick">
        <i class="pi pi-pencil"></i> Editar
      </button>
      <button class="menu-button save-button" @click="onSave">
        <i class="pi pi-save"></i> Salvar
      </button>
    </nav>
  </aside>
</template>


<style scoped>
.sidebar {
  display: flex;
  flex-direction: column;
  background-color: #f8f8ff;
  color: rgb(80, 120, 185);
  
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.2);
}

.sidebar-title {
  margin-bottom: 3rem;
  font-weight: 600;
  font-size: 2.5rem;
  
}
.sub-tittle{
  font-weight: 500;
  padding-bottom: 1rem;
}
.menu {
  display: flex;
  flex-direction: column;
  /* flex-wrap: nowrap; */
  gap: 12px;
}

.menu-button {
  width: 20rem;
  padding: 15px 30px;
  border: none;
  font-weight: 600;
  border-radius: 5px;
  background-color: #ecefff;
  border: 1px solid #d3daff;
  color: rgb(105, 112, 154);
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s;
}

.menu-button:hover {
  background-color: #c6e8ff;
}

.save-button {
  background-color: #27ae60;
  color: white;
}

.save-button:hover {
  background-color: #219653;
}

.active {
  background-color: #298fd7;
  color: white;
}
</style>
