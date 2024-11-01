<template>
  <div class="progress-circle">
    <svg
        :width="size"
        :height="size"
        viewBox="0 0 36 36"
        class="circular-chart"
        :class="[statusClass, { 'dashboard-type': type === 'dashboard' }]"
    >
      <path
          class="circle-bg"
          d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831"
      />
      <path
          class="circle"
          :stroke="color"
          :stroke-dasharray="`${progress}, 100`"
          d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831"
      />

      <!-- Центрированный текст или иконка -->
      <text v-if="type === 'default'" x="18" y="20.35" class="percentage">{{ progress }}%</text>
      <text v-else x="18" y="20.35" class="icon" :fill="color">
        <!-- Вывод иконки в зависимости от статуса -->
        <tspan v-if="status === 'success'">✔️</tspan>
        <tspan v-else-if="status === 'error'">❌</tspan>
        <tspan v-else-if="status === 'warning'">⚠️</tspan>
        <tspan v-else>ℹ️</tspan>
      </text>
    </svg>
  </div>
</template>

<script>
import { ref, computed, watch, defineComponent } from 'vue';

export default defineComponent({
  props: {
    value: {
      type: Number,
      required: true,
    },
    status: {
      type: String,
      default: 'in-progress',
    },
    size: {
      type: Number,
      default: 120,
    },
    type: {
      type: String,
      default: 'default',
    },
  },
  setup(props) {
    const progress = ref(props.value);
    const color = computed(() => {
      if (progress.value < 30) return '#f44336'; // Красный для низкого прогресса
      else if (progress.value < 70) return '#ffc107'; // Желтый для среднего прогресса
      else return '#4caf50'; // Зеленый для высокого прогресса
    });

    // Обновление прогресса при изменении значения
    watch(
        () => props.value,
        (newVal) => {
          progress.value = newVal;
        },
        { immediate: true }
    );

    const statusClass = computed(() => {
      switch (props.status) {
        case 'success':
          return 'success';
        case 'warning':
          return 'warning';
        case 'error':
          return 'error';
        default:
          return 'in-progress';
      }
    });

    return { progress, color, statusClass };
  },
});
</script>

<style scoped>
.progress-circle {
  display: flex;
  justify-content: center;
  align-items: center;
}

.circular-chart {
  max-width: 100%;
  max-height: 100%;
}

.circle-bg {
  fill: none;
  stroke: #eee;
  stroke-width: 3.8;
}

.circle {
  fill: none;
  stroke-width: 2.8;
  stroke-linecap: round;
  transition: stroke-dasharray 0.6s ease, stroke 0.6s ease;
}

.percentage {
  font-size: 0.5em;
  text-anchor: middle;
  fill: #333;
}

/* Стили для иконок */
.icon {
  font-size: 0.7em;
  text-anchor: middle;
  dominant-baseline: middle;
}

/* Вращение круга при включении типа dashboard */
.dashboard-type .circle {
  transform: rotate(-90deg);
  transform-origin: 50% 50%;
}
</style>
