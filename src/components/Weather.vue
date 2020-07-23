<template>
  <v-container class="fill-height" fluid>
    <v-col cols="12" align="center" justify="center">
      <v-row align="center" justify="center">
        <v-col cols="12" sm="8" md="4">
          <v-card class="elevation-12">
            <v-toolbar color="primary" dark flat>
              <v-toolbar-title>Weather App...with styles</v-toolbar-title>
              <v-spacer></v-spacer>
            </v-toolbar>
            <v-card-text>
              <v-form>
                <v-text-field
                  label="Enter a city and press enter (ex: Dallas)"
                  width="300"
                  v-model="searchedLocation"
                  @keydown.enter="searchDate"
                ></v-text-field>
              </v-form>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn large color="primary" @click="searchDate" class="mb-5">Search</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
      <v-row align="center" justify="center" v-if="!loadingCities">
        <v-expansion-panels>
          <v-expansion-panel
            @click="details(weatherCity.woeid)"
            @keydown.enter="details(weatherCity.woeid)"
            v-for="weatherCity in weatherCityList"
            :key="weatherCity.woeid"
          >
            <v-expansion-panel-header>{{ weatherCity.title }}</v-expansion-panel-header>
            <v-expansion-panel-content v-if="loading">
              <div class="loading_box">
                <p>Loading...</p>
                <div class="line"></div>
                <div class="line"></div>
                <div class="line"></div>
              </div>
            </v-expansion-panel-content>
            <v-expansion-panel-content v-else>
              <v-row align="center" justify="center">
                <v-col
                  cols="2"
                  v-for="weatherData in weatherCityInfo.consolidated_weather.slice(
                    0,
                    5
                  )"
                  :key="weatherData.id"
                >
                  <v-card class="ma-3 pa-6">
                    <h4>
                      {{ momentWeekday(weatherData.applicable_date) }}
                      {{ momentDate(weatherData.applicable_date) }}
                    </h4>
                    <img
                      :src="
                        `https://www.metaweather.com/static/img/weather/${weatherData.weather_state_abbr}.svg`
                      "
                    />
                    <p>
                      <span>Max:</span>
                      {{ Math.round(weatherData.min_temp) }}°C
                    </p>
                    <p>
                      <span>Min:</span>
                      {{ Math.round(weatherData.max_temp) }}°C
                    </p>
                    <p>
                      <span>Humidity: {{ weatherData.humidity }}%</span>
                    </p>
                    <p>
                      <span>Wind Speed:</span>
                      {{ Math.round(weatherData.wind_speed) }} mph
                    </p>
                    <p>
                      <span>Wind Direction:</span>
                      {{ Math.round(weatherData.wind_direction) }}°
                      {{ weatherData.wind_direction_compass }}
                    </p>
                  </v-card>
                </v-col>
              </v-row>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-row>
    </v-col>
  </v-container>
</template>
<script>
import axios from "axios";
import moment from "moment";

const cors = "https://cors-anywhere.herokuapp.com/";
const url = "https://www.metaweather.com/";

export default {
  name: "Weather",
  data() {
    return {
      searchedLocation: this.value,
      weatherCityList: [],
      weatherCityInfo: {},
      loading: true,
      loadingCities: false,
      alignment: "center",
      dense: false,
      justify: "center",
    };
  },
  methods: {
    searchDate() {
      this.loadingCities = true;
      axios
        .get(`${cors}${url}api/location/search/?query=` + this.searchedLocation)
        .then((res) => {
          this.weatherCityList = res.data;
          this.loadingCities = false;
        });
    },
    details(woeId) {
      this.loading = true;
      axios
        .get(`${cors}${url}/api/location/` + woeId)
        .then((res) => {
          this.weatherCityInfo = res.data;
          this.loading = false;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    momentWeekday(date) {
      const day = moment(date).weekday();
      if (day === 0) {
        return "Sunday";
      } else if (day === 1) {
        return "Monday";
      } else if (day === 2) {
        return "Tuesday";
      } else if (day === 3) {
        return "Wednesday";
      } else if (day === 4) {
        return "Thursday";
      } else if (day === 5) {
        return "Friday";
      } else if (day === 6) {
        return "Saturday";
      }
    },
    momentDate(date) {
      return moment(date).format("MM/DD");
    },
  },
};
</script>
