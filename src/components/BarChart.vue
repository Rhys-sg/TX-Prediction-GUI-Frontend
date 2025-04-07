<template>
  <div class="chart-container">
    <Scatter
      id="my-scatter-id"
      :options="chartOptions"
      :data="chartData"
    />
  </div>
</template>

<script>
import { Scatter } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  PointElement,
  LinearScale
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, PointElement, LinearScale)

export default {
  name: 'ScatterChart',
  components: { Scatter },
  props: {
    predicted_TX: {
      type: Number,
      required: true,
    },
    observed_TX: {
      type: Number,
      required: true,
    },
    predicted_TX_color: {
      type: String,
      default: '#747475',
    },
    observed_TX_color: {
      type: String,
      default: '#747475',
    }
  },
  data() {
    return {
      predictedData: [],
      observedData: [],
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          x: {
            grid: {
              display: false
            }
          },
          y: {
            grid: {
              display: false
            }
          }
        },
        plugins: {
          legend: {
            display: false
          },
          title: {
            display: false
          }
        }
      }
    }
  },
  computed: {
    chartData() {
      return {
        datasets: [
          {
            data: this.predictedData,
            backgroundColor: this.predicted_TX_color,
            showLine: false
          },
          {
            data: this.observedData,
            backgroundColor: this.observed_TX_color,
            showLine: false
          }
        ]
      }
    }
  },
  watch: {
    predicted_TX: {
      immediate: true,
      handler(val) {
        this.predictedData.push({ x: this.predictedData.length, y: val });
      }
    },
    observed_TX: {
      immediate: true,
      handler(val) {
        this.observedData.push({ x: this.observedData.length, y: val });
      }
    }
  }
}
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: 100%;
  position: relative;
}

.chart-container canvas {
  height: 100% !important;
}
</style>
