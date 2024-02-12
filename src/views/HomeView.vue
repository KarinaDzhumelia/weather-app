<template>
  <div class="home">
    <div class="weather-wrapper" :class="weatherClass" v-if="typeof weather.main !== 'undefined'">
      <div class="main">
        <div class="info">
          <div class="location">{{ weather.name }} {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-info">
          <div class="temp">Now: {{ Math.round(weather.main.temp) }}°C</div>
          <div class="temp">Min: {{ Math.round(weather.main.temp_min) }}°C</div>
          <div class="temp">Max: {{ Math.round(weather.main.temp_max) }}°C</div>
          <div class="weather">
            <div class="weather-title">{{ weather.weather[0].main }}</div>
            <div class="weather-icon">
              <img :src="getWeatherIconUrl()" alt="Weather Icon" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  props: ['lat', 'lon', 'apiKey'],
  data () {
    return {
      url_base: 'https://api.openweathermap.org/data/2.5/weather?',
      weather: {}
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
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
    getWeatherIconUrl() {
      const iconId = this.weather.weather[0].icon;
      const baseUrl = 'https://openweathermap.org/img/wn/';
      return `${baseUrl}${iconId}@2x.png`;
    },
  },
  computed: {
    weatherClass() {
      const mainWeather = this.weather.weather[0].main.toLowerCase();
      switch (mainWeather) {
        case 'clear':
          if(this.weather.weather[0].icon[2]==='n') return 'night-clear-sky';
          return 'clear-sky';
          case 'clouds':
          if(this.weather.weather[0].icon[2]==='n') return 'night-clouds';
          return 'clouds';
        case 'rain':
          return 'rain';
        case 'thunderstorm':
          return 'thunderstorm';
        case 'snow':
          return 'snow';
        case 'drizzle':
          return 'drizzle';
        case 'mist':
          return 'mist';
        default:
          return 'clear';
      }
    }
  }
}
</script>

<style lang="scss">
.home {
  max-width: 600px;
  margin: 0 auto;
}

.weather-wrapper {
  position: relative;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  background-size: cover;
}

.main {
  padding: 20px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.1), rgba(0, 0, 0, 0.5));
}

.clear-sky,
.night-clear-sky,
.night-clouds,
.thunderstorm {
  .weather-icon {
    background-image: linear-gradient(to bottom, rgba(255, 255, 255, 0.1), rgba(255, 255, 255, 0.5));
  }
}

.clear-sky {
  background-image: url('../assets/clear-sky.jpg');
}


.night-clear-sky {
  background-image: url('../assets/night-clear-sky.jpg');
}

.clouds {
  background-image: url('../assets/clouds.jpg');
}

.night-clouds {
  background-image: url('../assets/night-clouds.jpg');
}

.rain {
  background-image: url('../assets/rain.jpg');
}

.thunderstorm {
  background-image: url('../assets/thunderstorm.jpg');
}

.snow {
  background-image: url('../assets/snow.jpg');
}

.drizzle {
  background-image: url('../assets/drizzle.jpg');
}

.mist {
  background-image: url('../assets/mist.jpg');
}

.info {
  text-align: center;
  color: #fff;
}

.location {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 10px;
}

.date {
  font-size: 18px;
  color: #ddd;
}

.weather-info {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.temp {
  flex: 1;
  text-align: center;
  margin: 0 10px;
  font-size: 18px;
  color: #fff;
}


.weather {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.weather-title {
  text-align: center;
  font-size: 24px;
  font-weight: bold;
  /* margin-top: 20px; */
  color: #ddd;
}

.weather-icon {
  text-align: center;
  margin-left: 20px;
  border-radius: 20px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.1), rgba(0, 0, 0, 0.5));
  img {
    width: 70px;
    border-radius: 20px;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
    filter: invert(1);
  }
}

@media screen and (max-width: 600px) {
  .home {
    max-width: 100%;
  }
  
  .main {
    padding: 10px;
  }
  
  .weather-info {
    flex-direction: column;
  }
  
  .temp {
    margin: 5px 0;
  }

  .weather {
    justify-content: end;
  }
}
</style>

