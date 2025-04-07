<template>
  <div class="chart-container">
    <Scatter
      id="scatter-chart"
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
  LinearScale,
  LogarithmicScale
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, PointElement, LinearScale, LogarithmicScale)

export default {
  name: 'ScatterPlot',
  components: { Scatter },
  props: {
    predicted_TX: {
      type: Number,
      required: true
    },
    observed_TX: {
      type: Number,
      required: true
    },
    predicted_TX_color: {
      type: String,
      default: '#747475'
    },
    observed_TX_color: {
      type: String,
      default: '#747475'
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
            title: {
              display: true,
              text: 'Measurement Order'
            },
            grid: {
              display: false
            }
          },
          y: {
            type: 'logarithmic',
            min: 1,
            title: {
              display: true,
              text: 'Relative Fluorescence'
            },
            ticks: {
              callback: function (value) {
                const exp = Math.log10(value);
                return exp % 1 === 0 ? `10^${exp}` : '';
              }
            },
            grid: {
              display: false
            }
          }
        },
        plugins: {
          legend: {
            display: true
          },
          title: {
            display: true,
            text: 'Relative Fluorescence (Scatter)',
            font: {
              size: 12
            },
            padding: {
              top: 10,
              bottom: 20
            }
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
            label: 'Predicted',
            data: this.predictedData,
            backgroundColor: this.predicted_TX_color
          },
          {
            label: 'Observed',
            data: this.observedData,
            backgroundColor: this.observed_TX_color
          }
        ]
      }
    }
  },
  watch: {
    predicted_TX(newVal) {
      this.predictedData.push({ x: this.predictedData.length + 1, y: newVal });
    },
    observed_TX(newVal) {
      this.observedData.push({ x: this.observedData.length + 1, y: newVal });
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
