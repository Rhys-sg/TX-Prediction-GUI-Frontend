<template>
  <!-- BarChart Component -->
  <!-- 
    This component renders a bar chart using vue-chartjs and Chart.js. 
    It displays two bars representing 'Predicted' and 'Observed' values with customizable colors. 
    The chart options and data are dynamically updated based on the props provided.
  -->
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
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LogarithmicScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LogarithmicScale)

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
            type: 'logarithmic',
            min: 1,
            max: 16000000000,
            // Format the tick labels as powers of 10
            ticks: {
              callback: function(value) {
                // Check if the value is a power of 10
                const exp = Math.log10(value);
                if (exp % 1 === 0) {
                  return `10^${exp}`;
                }
                return '';
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
