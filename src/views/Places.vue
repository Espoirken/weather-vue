<script>
import axios from "axios";
import _ from "lodash";

export default {
  data() {
    return {
      apiUrl: import.meta.env.VITE_API_URL,
      apiToken: import.meta.env.VITE_API_TOKEN,
      headers: {},
      places: [],
      isReady: false,
    };
  },
  async mounted() {
    this.headers = {
      Accept: "application/json",
      Authorization: `Bearer ${this.apiToken}`,
      "Access-Control-Allow-Origin": "*",
    };

    this.getPlaces();
  },
  methods: {
    setWeatherData: async function () {
      this.places.data.forEach(async (location, key) => {
        await axios
          .get(`${this.apiUrl}/weather`, {
            headers: this.headers,
            params: {
              latitude: location.latitude,
              longitude: location.longitude,
            },
          })
          .then((response) => {
            this.places.data[key]["weather"] = _.first(response.data);
          });
      });
    },
    getPlaces: async function () {
      await axios
        .get(`${this.apiUrl}/places`, {
          headers: this.headers,
          params: {
            search: this.$route.query.search,
          },
        })
        .then((response) => {
          this.places = response.data;
          this.setWeatherData();
          this.isReady = true;
        })
        .catch((error) => {
          console.log(error)
          this.isReady = false;
        });
    },
  },
};
</script>

<template>
  <main>
    <div class="container locations__container">
      <div class="locations">
        <section class="wrap" v-if="isReady">
          <div class="new-container" v-for="(place, placeIndex) in places.data" :key="placeIndex">
            <div class="weather-side">
              <div class="weather-gradient"></div>
              <div class="date-container">
                <h2 class="place-name">{{place.name}}</h2>
                <i class="location-icon"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24"
                    viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"
                    stroke-linejoin="round" class="feather feather-map-pin location-icon">
                    <path d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z"></path>
                    <circle cx="12" cy="10" r="3"></circle>
                  </svg></i>
                <span class="location">{{place.city}}, {{place.country}}
                </span>
              </div>
              <div class="weather-container">
                <div class="flex" v-for="(category, key) in place.categories" :key="key">
                  <img :src="category.icon" alt="" class="category-icon" />
                  <h3 class="weather-desc">
                    {{category.name}}
                  </h3>
                </div>
              </div>
            </div>
            <div class="info-side">
              <div class="today-info-container" v-if="place.weather">
                <div class="today-info" v-for="(info, index) in place.weather.slice(0, 1)" :key="index">
                  <div class="precipitation">
                    <span class="title">MIN TEMP</span><span class="value">{{info.temp_min}}</span>
                    <div class="clear"></div>
                  </div>
                  <div class="precipitation">
                    <span class="title">MAX TEMP</span><span class="value">{{info.temp_max}}</span>
                    <div class="clear"></div>
                  </div>
                  <div class="humidity">
                    <span class="title">HUMIDITY</span><span class="value">{{info.humidity}}</span>
                    <div class="clear"></div>
                  </div>
                  <div class="wind">
                    <span class="title">WIND</span><span class="value">{{info.wind_speed}}</span>
                    <div class="clear"></div>
                  </div>
                </div>
              </div>
              <div class="week-container">
                <ul class="week-list">
                  <li v-for="(weather, weatherIndex) in place.weather" :key="weatherIndex">
                    <i class="weather-description" data-feather="sun"></i><span class="day-name">{{weather.day}}</span>
                    <p class="weather-description"><img :src="weather.icon" /></p>
                    <p class="weather-description">{{weather.description[0]}}</p>
                  </li>
                  <div class="clear"></div>
                </ul>
              </div>
            </div>
          </div>
        </section>
        <section class="weather loading" v-else>
        </section>
      </div>
    </div>
  </main>
</template>
