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
      return {
        labels: ['Predicted', 'Observed'],
        datasets: [{
          data: [this.predicted_TX, this.observed_TX],
          backgroundColor: [this.predicted_TX_color, this.observed_TX_color],
          barThickness: 11,
        }]
      }
    },
    labelColors() {
      // If observed_TX is null or undefined, set the label color to red, otherwise use black
      return {
        predicted: '#000000',
        observed: (this.observed_TX === null || this.observed_TX === undefined) ? '#FF0000' : '#000000'
      };
    }
  },
  methods: {
    updateChartLabelColors(chart) {
      // Set the label colors based on the labelColors computed property
      const xAxis = chart.scales['x'];
      xAxis.ticks.forEach((tick, index) => {
        if (index === 1) { // The 'Observed' label is at index 1
          xAxis.ctx.fillStyle = this.labelColors.observed;
        } else {
          xAxis.ctx.fillStyle = this.labelColors.predicted;
        }
        xAxis.draw();
      });
    }
  },
  mounted() {
    this.$nextTick(() => {
      const chart = this.$refs.myChart.$data._chart;
      this.updateChartLabelColors(chart);
    });
  }
}
</script>

<template>
  <div class="chart-container">
    <Bar
      ref="myChart"
      id="my-chart-id"
      :options="chartOptions"
      :data="chartData"
    />
  </div>
</template>

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
