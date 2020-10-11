<script>
import { Line } from 'vue-chartjs'

export default {
  extends: Line,
  props: {
    label: {
      type: String,
    },
    chartData: {
      type: Array,
    },
    options: {
      type: Object,
    },
    chartColorOptions: {
      type: Array,
    },
  },
  mounted() {
    const dates = this.chartData.map((d) => d.date).reverse()
    const totals = this.chartData.map((d) => d.total).reverse()

    this.renderChart(
      {
        labels: dates,
        datasets: [
          {
            label: this.label,
            data: totals,
            ...this.chartColorOptions,
          },
        ],
      },
      this.options
    )
  },
}
</script>
