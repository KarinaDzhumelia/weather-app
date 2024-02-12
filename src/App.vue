<template>
  <div id="app">
    <header>
      <input type="text" placeholder="Search" v-model.lazy="city" @keyup.enter="fetchGeo">
    </header>
    <div class="error" v-if="errorCheck === true">Invalid city name. Please enter a valid city.</div>
    <div v-else>
      <nav>
        <router-link to="/" >Today</router-link> |
        <router-link to="/week">For a week</router-link>
      </nav>
      <router-view :lat="lat" :lon="lon" :apiKey="apiKey"/>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
        apiKey: 'a8d599f6287f9b5534c52fbe85ec9645',
        url_geo: 'http://api.openweathermap.org/geo/1.0/direct?',
        city: '',
        lat: '',
        lon: '',
        errorCheck: false
      }
    },
    mounted() {
      this.getUserLocation();
    },
    methods: {
      fetchGeo: function () {
        fetch(`${this.url_geo}q=${this.city}&appid=${this.apiKey}`)
          .then(res => {
            return res.json();
          })
          .then(this.setGeo)
          .catch(error => {
            console.error('Error fetching geo data:', error);
            this.errorCheck = true;
          });
      },
      setGeo: function(results) {
        if (results.length === 0) {
          throw new Error('No results found for the city');
        } else {
          this.errorCheck = false;
        }
        this.lat = results[0].lat;
        this.lon = results[0].lon;
        this.city = '';
      },
      getUserLocation() {
        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(
            (position) => {
              this.lat = position.coords.latitude;
              this.lon = position.coords.longitude;
            },
            (error) => {
              console.error('Помилка отримання геолокації:', error);
            }
          );
        } else {
          console.error('Геолокація не підтримується в цьому браузері.');
        }
      },
    }
  }
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}

input[type="text"] {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #2c3e50;
  border-radius: 20px;
  margin-top: 20px;
  outline: none;

  &:focus {
    border-color: #42b983;
    box-shadow: 0 0 5px rgba(66, 185, 131, 0.7);
  }
}

input[type="text"]::placeholder {
  color: #2c3e50;
}

.error {
  color: red;
  font-size: 18px;
  margin-top: 20px;
}
</style>
