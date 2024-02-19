<template>
  <div>
    <input v-model="city" type="text" placeholder="Enter city">
    <button @click="getWeather">Get Weather</button>

    <div v-if="weatherData">
      <h2>{{ weatherData.name }} Weather</h2>
      <p>Current temperature: {{ weatherData.main.temp }}°C</p>
      <img :src="(`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}.png`)" alt="">

      <h3>Forecast for the next 3 days:</h3>
      <ul>
        <li v-for="forecast in weatherForecast" :key="forecast.dt">
          {{ new Date(forecast.dt * 1000).toDateString() }} - {{ forecast.weather[0].description }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

const city = ref('');
const weatherData = ref(null);
const weatherForecast = ref([]);


const getWeather = async () => {
  try {
    const weatherResponse = await axios.get('http://api.openweathermap.org/data/2.5/weather', {
      params: {
        q: city.value,
        appid: 'af3904bcfb9954b533100c6413793863',
        units: 'metric'
      }
    });
    weatherData.value = weatherResponse.data;

    const forecastResponse = await axios.get('http://api.openweathermap.org/data/2.5/forecast', {
      params: {
        q: city.value,
        appid: 'af3904bcfb9954b533100c6413793863',
        units: 'metric'
      }
    });
    weatherForecast.value = forecastResponse.data.list.filter((item, index) => index % 8 === 0);
  } catch (error) {
    console.error('Error fetching weather data', error);
  }
};
</script>
