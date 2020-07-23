<template>
  <q-page class="flex column">
    <div class="col q-pt-lg q-px-md">
      <q-input
        bottom-slots
        v-model="search"
        placeholder="location"
        dark
        borderless=""
        >
        <template v-slot:before>
          <q-icon
            @click="getLocation"
            name="my_location" />
        </template>


        <template v-slot:append>
          <q-btn round dense flat icon="search" />
        </template>
      </q-input>
    </div>

    

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{weatherData.name}}
        </div>
        <div class="text-h6 text-weight-light">
          {{weatherData.weather[0].main}}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp)}}</span>
          <span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>

      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar <br> Weahter
        </div>
          <q-btn
            @click="getLocation"
            class="col"
            flat>
            <q-icon left size="3em" name="my_location" />
            <div>Find my location</div>
          </q-btn>
      </div>
    </template>

    <div class="col skyline"></div>

  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data(){
    return{
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiURL:'https://api.openweathermap.org/data/2.5/weather',
      apiKey: '56bc2a1374eb7daa0e9aa172391c3899'
    }
  },
  methods: {
    getLocation(){
      navigator.geolocation.getCurrentPosition(position => {
        console.log('position ', position)
        this.lat= position.coords.latitude
        this.lon= position.coords.longitude
        this.getWeatherByCoords()
      })
    },
    getWeatherByCoords(){
      this.$axios(`${this.apiURL}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
      })
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to top, #00b4db, #0083b0)
  
  .degree
    top: -44px;

  .skyline
    flex: 0 0 100px
    background: url(../assets/skyline.png)
    background-size: contain
    background-position: center bottom
</style>
