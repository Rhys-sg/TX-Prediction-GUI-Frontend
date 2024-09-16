<template>
  <!-- BarChart Component -->
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
            ticks: {
              callback: function(value) {
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
          },
          title: {
            display: true,
            text: 'Relative Fluorescence',
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
      const predictedLabel = this.predicted_TX ? 'Predicted' : '<s>Predicted</s>';
      const observedLabel = this.observed_TX ? 'Observed' : '<s>Observed</s>';
      
      return {
        labels: [predictedLabel, observedLabel],
        datasets: [{
          data: [this.predicted_TX || 0, this.observed_TX || 0], // Use 0 if no value is provided
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
