<template>
  <div class="about">
    <div class="weather-wrapper clear-sky" v-if="typeof weather.city !== 'undefined'">
      <div class="info">
        <div class="location">{{ weather.city.name }} {{ weather.city.country }}</div>
      </div>
      <div class="main">
        <WeatherDay v-for="day in filteredData" :key="day.date._i" :day="day" />
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';
import WeatherDay from '@/components/WeatherDay.vue';

export default {
  name: 'WeekView',
  components: {
    WeatherDay
  },
  props: ['lat', 'lon', 'apiKey'],
  data () {
    return {
      url_base: 'https://api.openweathermap.org/data/2.5/forecast?',
      weather: {},
      filteredData: {}
    }
  },
  watch: {
    lat: function() {
      this.fetchWeather();
    }
  },
  created() {
    if (this.lat !== '' && this.lon !== '') this.fetchWeather();
  },
  methods: {
    fetchWeather: function() {
      fetch(`${this.url_base}lat=${this.lat}&lon=${this.lon}&units=metric&appid=${this.apiKey}`)
      .then(res => {
        return res.json();
      }).then(this.setWeather);
    },
    setWeather: function(results) {
      this.weather = results;
      this.filteredData = results.list.map(item => {
        return {
          date: moment(item.dt_txt.split(' ')[0]),
          main: item.main,
          weather: item.weather[0]
        };
      }).reduce((acc, item) => {
        if(!acc.some(day => day.date.isSame(item.date, 'day'))){
          acc.push(item);
        }
        return acc;
      }, []).slice(0,5);
      // console.log(this.filteredData);
    }
  },
}
</script>

<style scoped lang="scss">
.about {
  display: flex;
  justify-content: center;
  /* align-items: center; */
}

.weather-wrapper {
  padding: 20px;
}

.location {
  font-size: 30px;
  margin: 0;
}

.main {
  display: flex;
  justify-content: center;
  background: none;
  flex-wrap: wrap;
}
</style>

