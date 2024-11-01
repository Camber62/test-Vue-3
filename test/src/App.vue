<template>
  <div class="container">
    <div class="progress-container">
      <h1>Прогресс Бар Демонстрация</h1>
      <ProgressCircle :value="progressValue" :status="progressStatus" :type="progressType" />

      <div class="button-group">
        <button class="styled-button" @click="updateProgress">Изменить Прогресс</button>
        <button class="styled-button" @click="toggleProgressType">Сменить Тип</button>
      </div>
    </div>

    <PieChart />
  </div>
</template>

<script>
import { ref } from 'vue';
import ProgressCircle from './components/ProgressCircle.vue';
import PieChart from './components/PieChart.vue';

export default {
  components: {
    ProgressCircle,
    PieChart,
  },
  setup() {
    const progressValue = ref(25);
    const progressStatus = ref('in-progress');
    const progressType = ref('default');

    // Обновление значения прогресса и его статуса
    const updateProgress = () => {
      progressValue.value = Math.floor(Math.random() * 100);
      progressStatus.value = getProgressStatus(progressValue.value);
    };

    // Переключение типа прогресса
    const toggleProgressType = () => {
      progressType.value = progressType.value === 'default' ? 'dashboard' : 'default';
    };

    // Определение статуса прогресса
    const getProgressStatus = (value) => {
      if (value < 30) return 'error';
      if (value < 70) return 'warning';
      return 'success';
    };

    return { progressValue, progressStatus, progressType, updateProgress, toggleProgressType };
  },
};
</script>

<style>
.container {
  display: flex;
  justify-content: space-around;
}

.progress-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.button-group {
  margin-top: 10px;
  display: flex;
  gap: 10px;
}

.styled-button {
  padding: 8px 16px;
  font-size: 14px;
  color: #ffffff;
  background-color: #007bff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.styled-button:hover {
  background-color: #0056b3;
}
</style>
