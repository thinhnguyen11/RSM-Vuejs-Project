<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md"> 
      <q-input
        v-model="search"
        @keyup.enter="getWeatherBySearch"
        placeholder="Search location"
        dark
        borderless
        >
        <template v-slot:before>
          <q-icon
            @click = "getLocation"
            name="my_location" 
          />
        </template>

        <template v-slot:hint>
          Field hint
        </template>

        <template v-slot:append>
          <q-btn
            @click="getWeatherBySearch"
            round
            dense
            flat
            icon="search" 
          />
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
          <span>{{Math.round(weatherData.main.temp)}}</span>
          <span class="text-h4 relative-position
          degree">&deg;F</span>
        </div>
      </div>

      <div class="col text-center"> 
        <img :src="`http://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png`">
      </div>
    </template>
    
    <template v-else>
        <div class="col column text-center text-white">
          <div class="col text-h2 text-weight-thin">
            Quasar<br>Weather
          </div>
          <q-btn
            @click = "getLocation"
            color="col"
            flat
          >
            <q-icon left size="3em" name="my_location" />
            <div>Find my location</div>
          </q-btn>
        </div>
    </template>

    <div class="col city"></div>

  </q-page>
</template>

<script>
export default 
{
  name: 'PageIndex',
  data() 
  {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: 'b8026bccf4ea32c1932be57bf744843b'
    }
  },

  computed: 
  {
    bgClass() 
    {
      if (this.weatherData) 
      {
        if (this.weatherData.weather[0].icon.endsWith('n')) 
        {
          return 'bg-night'
        }
        else 
        {
          return 'bg-day'
        }
      }
    }
  },

  methods: 
  {
    getLocation() 
    {
      navigator.geolocation.getCurrentPosition
      (position => 
      {
          console.log('position: ', position)
          this.lat = position.coords.latitude
          this.lon = position.coords.longitude
          this.getWeatherByCoords()
      })
    },
    getWeatherByCoords()
    {
      this.$axios(`${ this.apiUrl }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }&units=Imperial`).
      then(response => { 
        this.weatherData = response.data
      })
    },
    getWeatherBySearch()
    {
      this.$axios(`${ this.apiUrl }?q=${ this.search }&appid=${ this.apiKey }&units=Imperial`).
      then(response => { 
        this.weatherData = response.data
      })
    }
  }
}
</script>

<style lang = "sass">
  .q-page
    background: linear-gradient(to bottom, #659999, #f4791f)
    &.bg-night 
      background: linear-gradient(to bottom, #0f2027, #203a43, #2c5364)
    &.bg-day
      background: linear-gradient(to top, #c6ffdd, #fbd786, #f7797d)
  .degree
    top: -44px
  .city 
    flex: 0 0 100px
    background: url(city.png)
    background-size: contain
    background-position: center bottom
</style>