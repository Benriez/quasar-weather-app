<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        @keyup.enter="getWeatherbySearch"
        bottom-slots
        v-model="search"
        placeholder="Search"
        dark
        borderless=""
        >
        <template v-slot:before>
          <q-icon
            @click="getLocation"
            name="my_location" />
        </template>


        <template v-slot:append>
          <q-btn
            @click="getWeatherbySearch"
            round
            dense
            flat
            icon="search" />
        </template>
      </q-input>
    </div>

    

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{weatherData.name}}
        </div>
        <div class="text-h6 text-weight-light">
          7 Tage Inzidenz
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(coronaData.data["01001"].weekIncidence)}}</span> 
        </div>
      </div>

      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar <br> Weather
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
      coronaData: null,
      weekIncidence: '',
      gemeindezahl: null,
      lat: null,
      lon: null,
      apiURL:'https://api.openweathermap.org/data/2.5/weather',
      apiCoronaURL: 'https://api.corona-zahlen.org/districts/',
      apiKey: '56bc2a1374eb7daa0e9aa172391c3899'
    }
  },
  computed: {
    bgClass(){
      if(this.weatherData){
        if(this.weatherData.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation(){
      this.$q.loading.show()
      navigator.geolocation.getCurrentPosition(position => {
        this.lat= position.coords.latitude
        this.lon= position.coords.longitude
        this.getWeatherByCoords()
      })
    },
    getWeatherByCoords(){
      this.$q.loading.show()
      this.$axios(`${this.apiURL}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        console.log('response.data', response.data)
        this.$q.loading.hide()
        // https://www.dcat-ap.de/def/politicalGeocoding/municipalityKey/20210131.html
      })
    },
    getWeatherbySearch(){
      this.$q.loading.show()
      this.$axios(`${this.apiURL}?q=${this.search}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        this.getAGS()

        
      })
      this.$q.loading.hide()

    }, 
    getAGS(){
      var ort = this.weatherData.name
      var ags = '';
      
      jQuery.get('ags.txt',function(data){
        for (let index = 0; index < 5; index++) {
          ags = ags + data.charAt(data.indexOf(ort)-27 + index)    
        }
      });

      setTimeout(function (){
        this.gemeindezahl = ags
        console.log(this.gemeindezahl)
        check()

      }, 250);
      
      var check = function(){
          console.log('late')
          this.getCoronaStats()
      }
      
      

    },
    getCoronaStats(){
      this.$axios(`https://api.corona-zahlen.org/districts/${this.gemeindezahl}`).then(response=>{
        this.coronaData = response.data
      });
      console.log('late')
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to top, #00b4db, #0083b0)
    &.bg-night
      background: linear-gradient(to bottom, #16222a, #3a6073)
    &.bg-day
      background: linear-gradient(to top, #00b4db, #0083b0)
  .degree
    top: -44px;

  .skyline
    flex: 0 0 150px
    background: url(../assets/skyline.png)
    background-size: contain
    background-position: center bottom
</style>
