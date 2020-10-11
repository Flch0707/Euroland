<template>
  <v-card>
    <v-tabs v-model="tab" background-color="primary" dark>
      <v-tab v-for="item in cities" :key="item.name">
        {{ item.name }}
      </v-tab>
    </v-tabs>

    <v-tabs-items v-model="tab">
      <v-tab-item v-for="item in cities" :key="item.destination">
        <v-card flat>
          <v-card-text>{{ item.name }}</v-card-text>
        </v-card>
      </v-tab-item>
    </v-tabs-items>
  </v-card>
</template>
<script>
import data from '~/static/data.json'
export default {
  data () {
    return {
      tab: null,
      items: [],
      data
    }
  },
  computed: {
    cities () {
      const listCities = []
      let ListDestination = []
      let ComputedPrice = 0
      data.countries.forEach((e) => {
        e.cities.forEach((city) => {
          ListDestination = []
          ListDestination = data.train.map((route) => {
            if (route.cityA === city.name) {
              const nbDigit = route.distance.toString().length
              if (nbDigit <= 2) {
                ComputedPrice = 15
              }
              if (nbDigit === 3) {
                ComputedPrice = Math.round(route.distance / 100) * 15
              }
              if (nbDigit === 4) {
                ComputedPrice = Math.round(route.distance / 1000) * 10 * 15
              }
              if (nbDigit === 5) {
                ComputedPrice = Math.round(route.distance / 10000) * 100 * 15
              }
              return {
                name: route.cityB,
                distance: route.distance,
                price: ComputedPrice
              }
            }
            if (route.cityB === city.name) {
              const nbDigit = route.distance.toString().length
              if (nbDigit <= 2) {
                ComputedPrice = 15
              }
              if (nbDigit === 3) {
                ComputedPrice = Math.round(route.distance / 100) * 15
              }
              if (nbDigit === 4) {
                ComputedPrice = Math.round(route.distance / 1000) * 10 * 15
              }
              if (nbDigit === 5) {
                ComputedPrice = Math.round(route.distance / 10000) * 100 * 15
              }
              return {
                name: route.cityA,
                distance: route.distance,
                price: ComputedPrice
              }
            }
          })
          if (ListDestination.length > 0) {
            Object.assign(city, city.destination = ListDestination)
            listCities.push(city)
          }
        })
      })
      return listCities
    }
  }
}
</script>
