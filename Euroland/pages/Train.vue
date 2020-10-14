/* eslint-disable no-console */
/* eslint-disable no-console */
/* eslint-disable no-console */
<template>
  <v-card>
    <v-tabs v-model="tab" background-color="primary" dark center-active>
      <v-tab v-for="city in cities" :key="city.name">
        {{ city.name }}
      </v-tab>
    </v-tabs>
    <v-tabs-items v-model="tab">
      <v-tab-item v-for="city in cities" :key="city.name">
        <v-data-iterator
          :items="city && city.destination"
          hide-default-footer
        >
          <template v-slot:default="props">
            <v-row>
              <v-col
                v-for="item in props.items"
                :key="item && item.name"
                cols="12"
                sm="6"
                md="4"
                lg="3"
              >
                <v-card>
                  <v-subheader class="primary">
                    {{ item.name }}
                  </v-subheader>

                  <v-divider />

                  <v-list dense>
                    <v-list-item>
                      <v-list-item-content>Distance:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.distance }} Km
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Price:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.price }} €
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Time-tokens:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.token }}
                      </v-list-item-content>
                    </v-list-item>
                  </v-list>
                </v-card>
              </v-col>
            </v-row>
          </template>
        </v-data-iterator>
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
      const cities = this.GetCities()
      cities.forEach((city) => {
        const listDest = []
        data.train.forEach((route) => {
          let dest = {}
          if (route.cityA === city.name) {
            dest = {
              name: route.cityB,
              distance: route.distance,
              price: this.GetPrices(route.distance),
              token: this.getTokens(route.distance)
            }
          }
          if (route.cityB === city.name) {
            dest = {
              name: route.cityA,
              distance: route.distance,
              price: this.GetPrices(route.distance),
              token: this.getTokens(route.distance)
            }
          }
          if (Object.keys(dest).length > 0) { listDest.push(dest) }
        })
        listDest.sort((a, b) => a.name.localeCompare(b.name))
        city.destination = listDest
      })
      return cities
    }
  },
  methods: {
    GetCities () {
      const cities = []
      data.countries.forEach((country) => {
        country.cities.forEach((city) => {
          city.country = country.name
          cities.push(city)
        })
      })
      return cities.sort((a, b) => a.name.localeCompare(b.name))
    },
    GetPrices (distance) {
      let price = 0
      const nbDigit = distance.toString().length
      if (nbDigit <= 2) {
        price = 15
      }
      if (nbDigit === 3) {
        price = Math.round(distance / 100) * 15
      }
      if (nbDigit === 4) {
        price = Math.round(distance / 1000) * 10 * 15
      }
      if (nbDigit === 5) {
        price = Math.round(distance / 10000) * 100 * 15
      }
      return price
    },
    getTokens (distance) {
      let tokens = 0
      const nbDigit = distance.toString().length
      if (distance <= 250) {
        tokens = 1
      } else {
        if (nbDigit <= 2) {
          tokens = 1
        }
        if (nbDigit === 3) {
          tokens = Math.round(distance / 250)
        }
        if (nbDigit === 4) {
          tokens = Math.round(distance / 2500) * 10
        }
        if (nbDigit === 5) {
          tokens = Math.round(distance / 25000) * 100
        }
      }
      return tokens
    }
  }
}
</script>
