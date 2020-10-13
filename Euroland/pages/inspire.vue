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
          :items-per-page.sync="itemsPerPage"
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
                  <v-card-title class="subheading font-weight-bold">
                    {{ item.name }}
                  </v-card-title>

                  <v-divider />

                  <v-list dense>
                    <v-list-item>
                      <v-list-item-content>Distance:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.distance }} Km
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Price (Franc):</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.price }} Fr
                      </v-list-item-content>
                    </v-list-item>
                  </v-list>
                </v-card>
              </v-col>
            </v-row>
          </template>
        </v-data-iterator>

        <!--         <v-card v-for="dest in city.destination" :key="dest.name">
          <v-card-text>{{ dest && dest.name }}</v-card-text>
          <v-card-text>{{ dest && dest.distance }}</v-card-text>
          <v-card-text>{{ dest && dest.price }}</v-card-text>
        </v-card> -->
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
      data,
      itemsPerPage: 4
    }
  },
  computed: {
    cities () {
      const cities = this.GetCities()
      cities.forEach((city) => {
        // eslint-disable-next-line no-console
        let ComputedPrice = 0
        const listDest = []
        data.train.forEach((route) => {
          let dest = {}
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
            dest = {
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
            dest = {
              name: route.cityA,
              distance: route.distance,
              price: ComputedPrice
            }
          }
          if (Object.keys(dest).length > 0) {
            listDest.push(dest)
          }
        })
        city.destination = listDest
      })
      return cities
    }
  },
  methods: {
    GetCities () {
      let cities = []
      let a = {}
      cities = data.countries.map((e) => {
        e.cities.forEach((city) => {
          Object.assign(city, city.destination = [])
          a = city
        })
        return a
      })
      return cities.sort((a, b) => a.name - b.name)
    },
    GetDestinations (city) {

    }
  }
}
</script>
