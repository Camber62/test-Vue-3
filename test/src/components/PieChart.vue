<template>
  <div>
    <h2>Круговая диаграмма</h2>
    <div class="chart-container">
      <PieChartComponent :chartData="chartData" />
    </div>

    <button @click="toggleForm" class="toggle-form-btn">
      {{ showForm ? 'Закрыть' : 'Добавить сектор' }}
    </button>

    <form v-if="showForm" @submit.prevent="addOrUpdateSector" class="sector-form">
      <label>Наименование</label>
      <input type="text" v-model="formData.label" required />

      <label>Значение</label>
      <input type="number" v-model.number="formData.value" min="0" required />

      <label>Цвет</label>
      <Chrome v-model="formData.color" />

      <button type="submit">{{ isEditing ? 'Обновить' : 'Добавить' }}</button>
      <button type="button" @click="resetForm">Отмена</button>
    </form>

    <ul class="sectors-list">
      <li v-for="(sector, index) in sectors" :key="index">
        {{ sector.label }} - {{ sector.value }}%
        <button @click="editSector(index)">Редактировать</button>
        <button @click="removeSector(index)">Удалить</button>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, computed, onMounted, onUnmounted } from 'vue';
import PieChartComponent from './PieChartComponent.vue';
import { Chrome } from '@ckpack/vue-color';

export default {
  components: { PieChartComponent, Chrome },
  setup() {
    const sectors = ref([]);
    const showForm = ref(false);
    const isEditing = ref(false);
    const editingIndex = ref(null);
    const formData = ref({ label: '', value: 1, color: '#2f67da' });

    const normalizeColor = (color) => (typeof color === 'string' ? color : color.hex || '#2f67da');

    const chartData = computed(() => ({
      labels: sectors.value.map((sector) => sector.label),
      datasets: [{
        data: sectors.value.map((sector) => sector.value),
        backgroundColor: sectors.value.map((sector) => normalizeColor(sector.color)),
      }],
    }));

    const addOrUpdateSector = () => {
      formData.value.color = normalizeColor(formData.value.color);
      if (isEditing.value && editingIndex.value !== null) {
        sectors.value[editingIndex.value] = { ...formData.value };
      } else {
        sectors.value.push({ ...formData.value });
      }
      resetForm();
    };

    const editSector = (index) => {
      isEditing.value = true;
      editingIndex.value = index;
      formData.value = { ...sectors.value[index] };
      showForm.value = true;
    };

    const removeSector = (index) => {
      sectors.value.splice(index, 1);
    };

    const resetForm = () => {
      formData.value = { label: '', value: 1, color: '#2f67da' };
      isEditing.value = false;
      editingIndex.value = null;
      showForm.value = false;
    };

    const toggleForm = () => {
      showForm.value = !showForm.value;
    };

    // Debounce function to limit how often the scroll handler is called
    const debounce = (func, delay) => {
      let timeout;
      return (...args) => {
        if (timeout) clearTimeout(timeout);
        timeout = setTimeout(() => {
          func.apply(this, args);
        }, delay);
      };
    };

    const handleScroll = debounce(() => {
      console.log('Прокрутка страницы');
    }, 200);

    onMounted(() => {
      window.addEventListener('scroll', handleScroll, { passive: true });
    });

    onUnmounted(() => {
      window.removeEventListener('scroll', handleScroll);
    });

    return {
      sectors,
      showForm,
      isEditing,
      formData,
      chartData,
      addOrUpdateSector,
      editSector,
      removeSector,
      resetForm,
      toggleForm,
    };
  },
};
</script>

<style scoped>
.chart-container {
  max-width: 400px;
  margin: auto;
}

.sector-form {
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
  border: 1px solid #ddd;
  border-radius: 8px;
  max-width: 300px;
  margin: 1em auto;
  background-color: #f9f9f9;
}

.toggle-form-btn {
  display: block;
  margin: 1em auto;
  padding: 0.5em 1em;
  background-color: #4f46e5;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.toggle-form-btn:hover {
  background-color: #3b3a9d;
}

input[type="text"],
input[type="number"] {
  padding: 0.5em;
  border: 1px solid #ddd;
  border-radius: 4px;
}

button[type="submit"] {
  background-color: #4f46e5;
  color: #fff;
  padding: 0.5em;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 1em;
}

button[type="button"] {
  background-color: #f44336;
  color: #fff;
  padding: 0.5em;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 0.5em;
}

button:hover {
  opacity: 0.9;
}

.color-picker {
  width: 100%;
  margin-top: 0.5em;
}

.sectors-list {
  list-style-type: none;
  padding: 0;
}

.sectors-list li {
  display: flex;
  justify-content: space-between;
  padding: 0.5em;
  border-bottom: 1px solid #ddd;
}

.sectors-list button {
  background: none;
  border: none;
  color: #4f46e5;
  cursor: pointer;
}

.sectors-list button:hover {
  text-decoration: underline;
}
</style>
