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
      default: null,
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
      observed_TX_internal: this.observed_TX !== null && this.observed_TX !== undefined ? this.observed_TX : this.predicted_TX,
      observed_TX_color_internal: this.observed_TX !== null && this.observed_TX !== undefined ? this.observed_TX_color : 'red',
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
  watch: {
    observed_TX(newVal) {
      if (newVal === null || newVal === undefined) {
        this.observed_TX_internal = this.predicted_TX;
        this.observed_TX_color_internal = 'red';
      } else {
        this.observed_TX_internal = newVal;
        this.observed_TX_color_internal = this.observed_TX_color;
      }
    }
  },
  computed: {
    chartData() {
      return {
        labels: ['Predicted', 'Observed'],
        datasets: [{
          data: [this.predicted_TX, this.observed_TX_internal],
          backgroundColor: [this.predicted_TX_color, this.observed_TX_color_internal],
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
