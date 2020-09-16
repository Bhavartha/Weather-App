<template>
  <q-page class="flex column">
    <div class="col q-pt-lg q-px-md">
      <q-input v-model="search" placeholder="City" dark borderless>
        <template v-slot:prepend>
          <q-icon @click="getLocation" name="my_location" />
        </template>
        <template v-slot:append>
          <q-icon name="search" @click="text = ''" class="cursor-pointer" />
        </template>
      </q-input>
    </div>
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{weatherData.name}}</div>
        <div class="text-weight-light">{{weatherData.weather[0].main}}</div>
        <div class="q-my-lg relative-position">
          <span class="text-weight-thin text-h1">{{weatherData.main.temp}}</span>
          <span class="text-weight-thin text-h3 degree">&deg;</span>
        </div>
      </div>

      <div class="col text-center">
        <img src="https://picsum.photos/100" alt="Pic" />
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quarar
          <br />Weather
        </div>
        <q-btn class="col" flat @click="getLocation">
          <q-icon left size="3" name="my_location" />
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>
    <div class="col city"></div>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      apiKey: "b0933f380b15f4b81937b10828267f50",
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
    };
  },
  methods: {
    getLocation() {
      navigator.geolocation.getCurrentPosition((position) => {
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        this.getWeatherByCoordinates();
      });
    },
    async getWeatherByCoordinates() {
      const res = await this.$axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
      );
      this.weatherData = res.data;
    },
  },
};
</script>

<style lang="scss" scoped>
.q-page {
  background: linear-gradient(to right, #136a8a, #267871);
}
.degree {
  position: absolute;
  top: 0px;
}
.city {
  flex: 0 0 100px;
  background: url("https://cdn.pixabay.com/photo/2017/01/31/00/33/architecture-2022614_960_720.png");
  background-size: contain;
  background-position: center bottom;
  color: black;
}
</style>