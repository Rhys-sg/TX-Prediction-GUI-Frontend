<template>
    <canvas ref="graphCanvas" style="width: 100%; height: 100%;"></canvas>
  </template>
  
  <script>
  import { Chart, registerables } from 'chart.js';
  Chart.register(...registerables);
  
  export default {
    props: {
      graphData: {
        type: Object,
        required: true
      }
    },
    data() {
      return {
        chartInstance: null
      };
    },
    mounted() {
      this.renderGraph();
    },
    methods: {
      renderGraph() {
        const ctx = this.$refs.graphCanvas.getContext('2d');
  
        if (this.chartInstance) {
          this.chartInstance.destroy();
        }
  
        this.chartInstance = new Chart(ctx, {
          type: 'scatter',
          data: {
            datasets: [
              {
                label: this.graphData.label || 'Observations',
                data: this.graphData.data, // Array of { x, y, label }
                backgroundColor: 'rgba(54, 162, 235, 0.6)'
              }
            ]
          },
          options: {
            responsive: true,
            plugins: {
              tooltip: {
                callbacks: {
                  label: function(context) {
                    const point = context.raw;
                    return point.label || `(${point.x}, ${point.y})`;
                  }
                }
              }
            },
            scales: {
              x: {
                type: 'linear',
                position: 'bottom',
                title: {
                  display: true,
                  text: this.graphData.xAxisTitle || 'X'
                }
              },
              y: {
                title: {
                  display: true,
                  text: this.graphData.yAxisTitle || 'Y'
                }
              }
            }
          }
        });
      }
    },
    beforeUnmount() {
      if (this.chartInstance) {
        this.chartInstance.destroy();
      }
    }
  };
  </script>
  