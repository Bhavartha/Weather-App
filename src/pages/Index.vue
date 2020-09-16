<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input @keyup.enter="getWeatherByPlace" v-model="search" placeholder="City" dark borderless>
        <template v-slot:prepend>
          <q-icon @click="getWeatherByCoordinates" name="my_location" />
        </template>
        <template v-slot:append>
          <q-icon name="search" @click="getWeatherByPlace" class="cursor-pointer" />
        </template>
      </q-input>
    </div>
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{weatherData.name}}</div>
        <div class="text-weight-light">{{weatherData.weather[0].main}}</div>
        <div class="q-my-lg relative-position">
          <span class="text-weight-thin text-h1">{{Math.round(weatherData.main.temp)}}</span>
          <span class="text-weight-thin text-h3 degree">&deg;</span>
        </div>
      </div>

      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" />
      </div>
    </template>
    <template v-else>
      <div class="col text-center text-white">
        <div class="col text-h2 text-weight-thin q-mb-xl">Weather</div>
        <q-btn class="col" flat @click="getWeatherByCoordinates">
          <q-icon left size="3" name="my_location" />
          <div>Get weather</div>
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
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith("n")) {
          return "bg-night";
        } else {
          return "bg-day";
        }
      }
    },
  },
  methods: {
    getLocation() {
      this.$q.loading.show();
      navigator.geolocation.getCurrentPosition((position) => {
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
      });
      this.$q.loading.hide();
    },
    async getWeatherByPlace() {
      this.$q.loading.show();
      const res = await this.$axios.get(
        `https://api.openweathermap.org/data/2.5/weather?q=${this.search}&appid=${this.apiKey}&units=metric`
      );
      this.weatherData = res.data;
      this.$q.loading.hide();
    },
    async getWeatherByCoordinates() {
      this.$q.loading.show();
      this.getLocation();
      const res = await this.$axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
      );
      this.weatherData = res.data;
      this.$q.loading.hide();
    },
  },
};
</script>

<style lang="scss" scoped>
.q-page {
  background: linear-gradient(to bottom, #136a8a, #267871);
  &.bg-day {
    background: linear-gradient(to bottom, #fdc830, #f37335);
  }
  &.bg-night {
    background: linear-gradient(to bottom, #232526, #414345);
  }
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