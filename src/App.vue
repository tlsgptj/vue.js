<template>
  <div : class="['app', weatherBackground]">
    <h1>Weather App</h1>
    <p>{{message}}</p>

    <div>
      <input
        v-model="city"
        type="text"
        placeholder="Enter city name"/>
        <button @click="fetchWeather">Search Weather</button>
    </div>

  <div v-if="weather">
    <h2>Weather in {{weather.name}}</h2>
    <p>Temperature: {{weather.main.temp}}°C</p>
    <p>Weather: {{weather.weather[0].description}}</p>
  </div>

  <p v-if="error" style="color: red;">{{error}}</p>
</div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      message: "Check the weather in your city!",
      city:"",//사용자 입력값 저장하는 타입
      weather:null, //API호출 결과 저장하는 타입
      error:null, //에러 메세지 저장하는 타입
    }; //vue.js에서 이부분은 필수
  },
  computed: {
    weatherBackground() {
      if (!this.weather) return "";
      const mainWeather = this.weather.weather[0].main.toLowerCase();
      if (mainWeather.includes("cloud")) return "cloudy";
      if (mainWeather.includes("rain")) return "rainy";
      if (mainWeather.includes("clear")) return "clear";
      return "default";
    },
  },
    methods: {
      async fetehWeather() {
        const apiKey = "";
        const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric`;

        try {
          this.error = null;
          const response = await axios.get(url);
          this.weather = response.data; //API 응답 데이터를 weather 변수에 저장
        } catch (error) {
          console.error ("Error fetching weather data:", error);
          this.weather = null;
          this.error = "City not found. Please try again";
        //에러 처리
       }
      },
    },
  };
</script>

<style>
.app {
  text-align: center;
  margin-top: 50px;
  min-height: 10vh;
  transition: background 0.5s ease;
}
input {
  padding: 10px;
  font-size: 16px;
  width: 200px;
}
button {
  margin-top: 10px 20px;
  padding: 10px 20px;
  font-size: 16px;
}

.cloudy {
  background: url("") no-repeat center center/cover;
}
.rainy {
  background: url("") no-repeat center center/cover;
}
.clear {
  background: url("") no-repeat center center/cover;
}
.default {
  background: #f0f8ff;
}
</style>

