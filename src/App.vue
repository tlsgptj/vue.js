<template>
  <div id="app">
    <h1>Weather App</h1>
    <p>{{message}}</p>

  <div v-if="weather">
    <h2>Weather in {{weather.name}}</h2>
    <p>Temperature: {{weather.main.temp}}°C</p>
    <p>Weather: {{weather.weather[0].description}}</p>
  </div>

  <button @click="fetehWeather">Get Weather</button>
</div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      message: "Check the weather in your city!",
      weather:null,
    }; //vue.js에서 이부분은 필수
  },
  methods: {
    async fetehWeather() {
      const apiKey = "";
      const city = "Seoul";
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric`;

      try {
        const response = await axios.get(url);
        this.weather = response.data; //API 응답 데이터를 weather 변수에 저장
      } catch (error) {
        console.error ("Error fetching weather data:", error);
        //에러 처리
       }
      },
    },
  };
</script>

<style>
#app {
  text-align: center;
  margin-top: 50px;
}
button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 16px;
}
</style>

