<template>
  <div class="chart-container">
    <Bar
      id="my-chart-id"
      :options="chartOptions"
      :data="chartData"
    />
  </div>
</template>

<script>
import { Bar } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

export default {
  name: 'BarChart',
  components: { Bar },
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
            min: -5,
            max: 0,
            reverse: true,
            ticks: {
              stepSize: 1,
              callback: function(value) {
                return value.toFixed(1);
              }
            },
            grid: {
              display: false
            }
          }
        },
        plugins: {
          legend: {
            display: false
          }
        }
      }
    }
  },
  computed: {
    chartData() {
      return {
        labels: ['Predicted', 'Observed'],
        datasets: [{
          data: [this.predicted_TX, this.observed_TX],
          backgroundColor: [this.predicted_TX_color, this.observed_TX_color],
          barThickness: 11,
        }]
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
