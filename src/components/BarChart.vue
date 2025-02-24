<template>
  <div>
    <apexchart
      class="histogram"
      :height="height"
      type="bar"
      :series="series"
      :options="chartOptions"
      v-if="showChart"
    ></apexchart>
  </div>
</template>

<script>
import { formatFn } from "@/utils/formatFns.js";
import VueApexCharts from "vue-apexcharts";
import { sum } from "d3-array";

export default {
  props: ["data", "height", "categories", "colors", "show_percents", "xmax"],
  components: { apexchart: VueApexCharts },
  data() {
    return {
      labelWidth: 125,
    };
  },
  methods: {
    formatTooltip(d) {
      let out = formatFn(d);
      if (this.show_percents)
        out = `${out} (${((100 * d) / this.total).toFixed(0)}%)`;
      return out;
    },
  },
  computed: {
    showChart() {
      if (!this.data) return false;
      else return this.total > 0;
    },
    total() {
      if (this.series) return sum(this.series[0]["data"]);
    },
    series() {
      if (this.data) {
        let data = [];
        for (let i = 0; i < this.categories.length; i++)
          data.push(this.data.get(this.categories[i]) || 0);
        return [{ data: data, name: "Total" }];
      }
    },
    chartOptions() {
      return {
        colors: this.colors,
        chart: {
          type: "bar",
          height: this.height,
          toolbar: {
            show: false,
          },
          zoom: {
            enabled: false,
          },
        },
        plotOptions: {
          bar: {
            horizontal: true,
            distributed: true,
            dataLabels: { position: "bottom" },
          },
        },
        legend: { show: false },
        xaxis: {
          type: "category",
          categories: this.categories,
          labels: {
            style: { fontSize: "1rem" },
            formatter: (d) => formatFn(d, 2),
            show: false,
          },
          axisTicks: { show: false },
          axisBorder: { show: false },
          max: this.xmax,
        },
        yaxis: {
          labels: {
            style: { fontSize: "16px" },
            trim: false,
            minWidth: this.labelWidth,
            maxWidth: this.labelWidth,
          },
          axisBorder: { show: false },
        },
        tooltip: {
          enabled: false,
        },
        dataLabels: {
          enabled: true,
          formatter: this.formatTooltip,
          textAnchor: "start",
          offsetY: 7,
          offsetX: 10,
          style: {
            fontSize: "1rem",
            fontWeight: "bold",
            colors: ["#212529"],
          },
        },
        grid: { show: false },
        states: {
          hover: {
            filter: {
              type: "none",
            },
          },
          active: {
            filter: {
              type: "none",
            },
          },
        },
      };
    },
  },
};
</script>

<style>
.apexcharts-xaxis-label {
  dominant-baseline: middle !important;
}
.apexcharts-yaxis-label {
  dominant-baseline: middle !important;
}
</style>