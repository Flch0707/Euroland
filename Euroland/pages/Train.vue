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
                  <v-subheader class="primary" style="justify-content: space-between">
                    {{ item.name }}
                    <small> {{ item.country }} </small>
                    <v-btn icon @click="GoToCity(item.name)">
                      <v-icon small>
                        mdi-magnify
                      </v-icon>
                    </v-btn>
                  </v-subheader>

                  <v-divider />

                  <v-list v-if="item && item.distanceTrain" dense>
                    <v-list-item>
                      <v-list-item-content class="font-italic grey darken-3">
                        Train
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Distance:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.distanceTrain }}
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Price:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.priceTrain }}
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Time-tokens:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.tokenTrain }}
                      </v-list-item-content>
                    </v-list-item>
                  </v-list>
                  <v-divider />
                  <v-list v-if="item && item.distancePlane" dense>
                    <v-list-item>
                      <v-list-item-content class="font-italic grey darken-3">
                        Plane
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Distance:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.distancePlane }}
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Price:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.pricePlane }}
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-content>Time-tokens:</v-list-item-content>
                      <v-list-item-content class="align-end">
                        {{ item.tokenPlane }}
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
      const listCapital = []
      cities.forEach((city) => {
        if (city.capital) { listCapital.push(city) }
      })
      cities.forEach((city) => {
        const listDestPlane = []
        const listDestTrain = []
        const listDest = []
        let dest = {}
        let distPlane = 0
        if (city.capital) {
          listCapital.forEach((planeRoute) => {
            if (city.name !== planeRoute.name) {
              distPlane = this.GetDistancePlane(city, planeRoute)
              dest = {
                name: planeRoute.name,
                country: this.getCountry(planeRoute.name),
                distancePlane: Math.round(distPlane) + 'Km',
                pricePlane: this.GetPricePlane(distPlane) + '€',
                tokenPlane: this.getTokensPlane(distPlane)
              }
              if (Object.keys(dest).length > 0) { listDestPlane.push(dest) }
            }
          })
        }
        data.train.forEach((trainRoute) => {
          dest = {}
          if (trainRoute.cityA === city.name) {
            dest = {
              name: trainRoute.cityB,
              country: this.getCountry(trainRoute.cityB),
              distanceTrain: trainRoute.distance + 'Km',
              priceTrain: this.GetPriceTrain(trainRoute.distance) + '€',
              tokenTrain: this.getTokensTrain(trainRoute.distance)
            }
          }
          if (trainRoute.cityB === city.name) {
            dest = {
              name: trainRoute.cityA,
              country: this.getCountry(trainRoute.cityA),
              distanceTrain: trainRoute.distance + 'Km',
              priceTrain: this.GetPriceTrain(trainRoute.distance) + '€',
              tokenTrain: this.getTokensTrain(trainRoute.distance)
            }
          }
          if (Object.keys(dest).length > 0) {
            // city.capital && listCapital.includes(dest.name) ? Object.assign(listDest.find(dest.name).destination, dest) : listDest.push(dest)
            listDestTrain.push(dest)
          }
        })
        const concatArr = [...new Set(listDestPlane.map(e => e.name).concat(listDestTrain.map(e => e.name)))]
        concatArr.map((name) => {
          const dest = {}
          dest.name = name
          dest.country = this.getCountry(name)
          if (listDestPlane.map(e => e.name).includes(name)) {
            const tempObj = listDestPlane.filter(el => el.name === name)[0]
            dest.distancePlane = tempObj.distancePlane
            dest.pricePlane = tempObj.pricePlane
            dest.tokenPlane = tempObj.tokenPlane
          }
          if (listDestTrain.map(e => e.name).includes(name)) {
            const tempObj = listDestTrain.filter(el => el.name === name)[0]
            dest.distanceTrain = tempObj.distanceTrain
            dest.priceTrain = tempObj.priceTrain
            dest.tokenTrain = tempObj.tokenTrain
          }
          listDest.push(dest)
        })
        listDest.sort((a, b) => a.name.localeCompare(b.name))
        city.destination = listDest
      })
      return cities
    }
  },
  methods: {
    GoToCity (city) {
      this.tab = this.GetCities().map(city => city.name).findIndex(el => el === city)
    },
    GetCities () {
      const cities = []
      data.countries.forEach((country) => {
        country.cities.forEach((city) => {
          cities.push(city)
        })
      })
      return cities.sort((a, b) => a.name.localeCompare(b.name))
    },
    getCountry (name) {
      return data.countries.map((country) => {
        return country.cities.map(city => city.name).includes(name) ? country.name : null
      }).filter(country => country !== null)[0]
    },
    GetPriceTrain (distance) {
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
    getTokensTrain (distance) {
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
    },
    GetPricePlane (distance) {
      return distance < 500 ? Math.round(distance * 0.3) : distance < 1000 ? Math.round(distance * 0.12) : distance < 2000 ? Math.round(distance * 0.09) : Math.round(distance * 0.065)
    },
    getTokensPlane (distance) {
      let tokens = 1
      const nbDigit = distance.toString().length
      if (distance <= 1000) {
        tokens += 1
      } else {
        if (nbDigit <= 3) {
          tokens += 1
        }
        if (nbDigit === 4) {
          tokens += Math.round(distance / 1000)
        }
      }
      return tokens
    },
    GetDistancePlane (cityDepart, citDestination) {
      let lat1 = cityDepart.lat
      let lat2 = citDestination.lat
      const lon1 = cityDepart.long
      const lon2 = citDestination.long
      const R = 6371 // km
      const dLat = this.toRad(lat2 - lat1)
      const dLon = this.toRad(lon2 - lon1)
      lat1 = this.toRad(lat1)
      lat2 = this.toRad(lat2)

      const a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
           Math.sin(dLon / 2) * Math.sin(dLon / 2) * Math.cos(lat1) * Math.cos(lat2)
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a))
      const d = R * c
      return d
    },
    // Converts numeric degrees to radians
    toRad (Value) {
      return Value * Math.PI / 180
    }
  }
}
</script>
