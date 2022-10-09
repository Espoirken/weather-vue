<script>
import Card from "@/components/Card.vue";
import axios from "axios";

export default {
  data() {
    return {
      apiUrl: import.meta.env.VITE_API_URL,
      apiToken: import.meta.env.VITE_API_TOKEN,
      headers: {},
      locations: [],
      isReady: false,
    };
  },
  components: {
    Card
  },
  async mounted() {
    this.headers = {
      Accept: "application/json",
      Authorization: `Bearer ${this.apiToken}`,
      "Access-Control-Allow-Origin": "*",
    };

    await this.getLocations();
  },
  methods: {
    getLocations: async function () {
      await axios.get(`${this.apiUrl}/location`, {
        headers: this.headers
      }).then(response => {
        this.locations = response.data;
        this.setWeatherData();
        this.isReady = true;
      }).catch(error => {
        this.isReady = false;
      });
    },

    setWeatherData: async function () {
      this.locations.data.forEach(async (location, key) => {
        await axios.get(`${this.apiUrl}/weather`, {
          headers: this.headers,
          params: {
            latitude: location.latitude,
            longitude: location.longitude
          }
        }).then((response) => {
          this.locations.data[key]['weather'] = response.data[0][0];
        });
      });
    },
  }
};
</script>

<template>
  <main>
    <div class="container locations__container">
      <div class="locations">
        <section class="wrap weather" v-if="isReady">
          <Card v-for="(location, index) in locations.data" :key="index" :data="location" />
        </section>
        <section class="weather loading" v-else>
        </section>
      </div>
    </div>
  </main>
</template>