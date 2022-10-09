<script>
import { RouterLink } from "vue-router";
export default {
  props: ["data"],
  computed: {
    view_more_url() {
      return `/places?&search=${this.data.city}`;
    },
  },
};
</script>
<template>
  <div class="card">
    <div class="weather" v-if="data.weather">
      <h2 class="city">Weather in {{data.city}}</h2>
      <h1 class="temp">
        {{data.weather.temperature}}
      </h1>
      <div class="flex">
        <img :src="data.weather.icon[0]" alt="" class="icon" />
        <div class="description">
          {{data.weather.description[0]}}
        </div>
      </div>
      <div class="humidity">Humidity: {{data.weather.humidity}}</div>
      <div class="wind">Wind speed: {{data.weather.wind_speed}}</div>
    </div>
    <div class="card--footer">
      <RouterLink :to="view_more_url" class="btn-more-details">
        More details
      </RouterLink>
    </div>
  </div>
</template>