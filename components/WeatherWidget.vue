<template>
  <div class="weather-app">
    <div class="input-container">
      <input v-model="city" class="city-input" type="text" placeholder="Enter city">
      <button @click="getWeather" class="get-weather-btn">Поиск</button>
    </div>

    <div v-if="weatherData" class="weather-container">
      <div class="weather-info">
        <h2>{{ weatherData.name }}</h2>
        <p>Температура: {{ weatherData.main.temp }}°C</p>
        <p>скорость ветра: {{ weatherData.wind.speed }} грудус: {{ weatherData.wind.deg }} порыв: {{ weatherData.wind.gust }} </p>
        <img :src="'https://openweathermap.org/img/wn/'+weatherData.weather[0].icon+'.png'" alt="Weather icon">
      </div>
    </div>
      <div v-if="weatherForecast && weatherForecast.length > 0" class="weather-forecasts-grid">
        <div v-for="forecast in weatherForecast" :key="forecast.dt" class="weather-forecast">
          <h3>{{ new Date(forecast.dt * 1000).toDateString() }}</h3>
          <p>Погода: {{ forecast.weather[0].description }}</p>
          <p>Температура: {{ forecast.main.temp }}°C</p>
          <p>скорость ветра: {{ forecast.wind.speed }}<br> грудус: {{ forecast.wind.deg }} <br> порыв: {{ forecast.wind.gust }} <br> </p>
          <img v-if="forecast.weather[0].icon" :src="'https://openweathermap.org/img/wn/' + forecast.weather[0].icon + '.png'" alt="Weather icon">
        </div>
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
    const weatherResponse = await axios.get('https://api.openweathermap.org/data/2.5/weather', {
      params: {
        q: city.value,
        appid: 'af3904bcfb9954b533100c6413793863',
        units: 'metric'
      }
    });
    weatherData.value = weatherResponse.data;

    const forecastResponse = await axios.get('https://api.openweathermap.org/data/2.5/forecast', {
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

<style>
  @import '../assets/app.css';
</style>



