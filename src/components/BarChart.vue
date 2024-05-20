<template>
  <div>
    <canvas id="myChart"></canvas>
  </div>
</template>

<script>
import {
  Chart,
  BarController,
  BarElement,
  CategoryScale,
  LinearScale,
  LineController,
  LineElement,
  PointElement,
  Tooltip,
  Legend,
} from "chart.js";

// 必要なコンポーネントを登録
Chart.register(
  BarController,
  BarElement,
  CategoryScale,
  LinearScale,
  LineController,
  LineElement,
  PointElement,
  Tooltip,
  Legend
);

export default {
  name: "BarChart",
  mounted() {
    this.renderChart();
  },
  methods: {
    renderChart() {
      const rawData = {
        labels: ["January", "February", "March", "April", "May", "June"],
        datasets: [
          {
            label: "Category A",
            data: [150, 200, 300, 400, 250, 500],
            backgroundColor: "rgba(255, 99, 132, 0.2)",
            borderColor: "rgba(255, 99, 132, 1)",
            borderWidth: 1,
            yAxisID: "y1",
          },
          {
            label: "Category B",
            data: [300, 150, 100, 200, 300, 150],
            backgroundColor: "rgba(54, 162, 235, 0.2)",
            borderColor: "rgba(54, 162, 235, 1)",
            borderWidth: 1,
            yAxisID: "y1",
          },
          {
            label: "Category C",
            data: [250, 400, 50, 300, 200, 100],
            backgroundColor: "rgba(75, 192, 192, 0.2)",
            borderColor: "rgba(75, 192, 192, 1)",
            borderWidth: 1,
            yAxisID: "y1",
          },
        ],
      };

      // データの合計を計算し、割合に変換
      const totalData = rawData.labels.map((_, index) => {
        return rawData.datasets.reduce(
          (sum, dataset) => sum + dataset.data[index],
          0
        );
      });

      const percentageData = rawData.datasets.map((dataset) => {
        return {
          ...dataset,
          data: dataset.data.map((value, index) =>
            ((value / totalData[index]) * 100).toFixed(2)
          ),
        };
      });

      // 合計値の折れ線グラフ用データセット
      const totalDataset = {
        label: "Total",
        data: totalData,
        type: "line",
        borderColor: "rgba(255, 159, 64, 1)",
        backgroundColor: "rgba(255, 159, 64, 0.2)",
        yAxisID: "y2",
      };

      const ctx = document.getElementById("myChart").getContext("2d");
      new Chart(ctx, {
        type: "bar",
        data: {
          labels: rawData.labels,
          datasets: [...percentageData, totalDataset],
        },
        options: {
          responsive: true,
          scales: {
            x: {
              stacked: true,
            },
            y1: {
              type: "linear",
              position: "left",
              stacked: true,
              ticks: {
                callback: function (value) {
                  return value + "%";
                },
                beginAtZero: true,
                max: 100,
              },
              title: {
                display: true,
                text: "Percentage",
              },
            },
            y2: {
              type: "linear",
              position: "right",
              ticks: {
                beginAtZero: true,
              },
              title: {
                display: true,
                text: "Total Value",
              },
            },
          },
          plugins: {
            tooltip: {
              callbacks: {
                label: function (tooltipItem) {
                  if (tooltipItem.datasetIndex === rawData.datasets.length) {
                    return "Total: " + tooltipItem.raw;
                  }
                  return tooltipItem.raw + "%";
                },
              },
            },
          },
        },
      });
    },
  },
};
</script>

<style scoped>
canvas {
  max-width: 1000px;
  height: 800px;
  margin: 0 auto;
}
</style>
