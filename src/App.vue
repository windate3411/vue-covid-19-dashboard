<template>
  <v-app>
    <v-main>
      <v-container class="mt-5 dashboard-wrapper">
        <v-card>
          <v-container>
            <h1 class="text-center dashboard-title">COVID-19 Tracking Board</h1>
            <v-tabs v-model="tab">
              <v-tab href="#tab-Positive">Positive</v-tab>
              <v-tab href="#tab-Hoptialized">Hoptialized</v-tab>
              <v-tab href="#tab-InIcu">InIcu</v-tab>
              <v-tab href="#tab-OnVentilators">OnVentilators</v-tab>
              <v-tab href="#tab-Recovered">Recovered</v-tab>
              <v-tab href="#tab-Deaths">Deaths</v-tab>
            </v-tabs>
            <v-tabs-items v-model="tab">
              <v-tab-item
                v-for="chartData in renderData"
                :key="chartData.id"
                :value="'tab-' + chartData.label"
              >
                <v-card flat>
                  <v-row v-if="arrPositive.length">
                    <v-col cols="12">
                      <h2>{{ chartData.label }}</h2>
                      <LineChart
                        :chartData="chartData.data"
                        :options="chartOptions"
                        :label="chartData.label"
                        :chartColorOptions="chartData.chartColorOptions"
                      />
                    </v-col>
                  </v-row>
                </v-card>
              </v-tab-item>
            </v-tabs-items>
          </v-container>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import axios from 'axios'
import dayjs from 'dayjs'
import LineChart from './components/LineChart'

export default {
  name: 'App',
  components: {
    LineChart,
  },
  data() {
    return {
      tab: 'tab-Positive',
      arrPositive: [],
      arrHoptialized: [],
      arrInIcu: [],
      arrOnVentilators: [],
      arrRecovered: [],
      arrDeaths: [],
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
      },
    }
  },
  async created() {
    let { data } = await axios.get(
      'https://api.covidtracking.com/v1/us/daily.json'
    )
    data = data.slice(0, 30)
    data.forEach((item) => {
      const date = dayjs(`${item.date}`).format('YYYY/MM/DD')
      const {
        positive,
        hospitalizedCurrently,
        inIcuCurrently,
        recovered,
        onVentilatorCurrently,
        death,
      } = item
      this.arrPositive.push({ date, total: positive })
      this.arrHoptialized.push({ date, total: hospitalizedCurrently })
      this.arrDeaths.push({ date, total: death })
      this.arrRecovered.push({ date, total: recovered })
      this.arrOnVentilators.push({ date, total: onVentilatorCurrently })
      this.arrInIcu.push({ date, total: inIcuCurrently })
    })
  },
  computed: {
    renderData() {
      const {
        arrPositive,
        arrHoptialized,
        arrInIcu,
        arrOnVentilators,
        arrRecovered,
        arrDeaths,
      } = this
      const labels = [
        'Positive',
        'Hoptialized',
        'InIcu',
        'OnVentilators',
        'Recovered',
        'Deaths',
      ]
      const displayedDataArr = [
        arrPositive,
        arrHoptialized,
        arrInIcu,
        arrOnVentilators,
        arrRecovered,
        arrDeaths,
      ]
      const chartColorOptions = [
        {
          borderColor: '#EF5350',
          backgroundColor: 'rgba(255, 56, 96, 0.1)',
        },
        {
          borderColor: '#FFF176',
          backgroundColor: 'rgba(191, 182, 63, 0.1)',
        },
        {
          borderColor: '#FFB74D',
          backgroundColor: 'rgba(239, 109, 9, 0.1)',
        },
        {
          borderColor: '#B3E5FC',
          backgroundColor: 'rgba(9, 140, 239, 0.1)',
        },
        {
          borderColor: '#00E676',
          backgroundColor: 'rgba(11, 227, 47, 0.1)',
        },
        {
          borderColor: '#E30B86',
          backgroundColor: 'rgba(239, 9, 140, 0.1)',
        },
      ]
      return displayedDataArr.map((data, index) => ({
        label: labels[index],
        data,
        chartColorOptions: chartColorOptions[index],
      }))
    },
  },
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Chilanka&display=swap');

.dashboard-wrapper {
  font-family: 'Chilanka', cursive;
  font-weight: 900;
}
</style>
