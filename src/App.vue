<template>
  <div : class="['app', weatherBackground]">
    <h1>Weather App</h1>
    <p>{{message}}</p>

    <div class="input-container">
      <input
        v-model="city"
        type="text"
        placeholder="Enter city name"/>
        <button @click="fetchWeatherCity">Search Weather</button>
        <button @click="fetchWeatherLocation">Use My Location</button>
    </div>

    <div v-if="forecast.length">
      <canvas id="weatherChart"></canvas>
    </div>

  <div v-if="weather">
    <h2>Weather in {{ weather.name }}</h2>
    <p>Temperature: {{ weather.main.temp }}°C</p>
    <p>Weather: {{ weather.weather[0].description }}</p>
  </div>

  <p v-if="error" style="color: red;">{{error}}</p>
</div>
</template>

<script>
import axios from "axios";
import { Chart } from "chart.js";
import L from "leaflet";

export default {
  data() {
    return {
      message: "Check the weather in your city!",
      city:"",//사용자 입력값 저장하는 타입
      weather:null, //API호출 결과 저장하는 타입
      error:null, //에러 메세지 저장하는 타입
      chart: null,
      map: null,
      mapVisible: false,
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
      async fetchWeather() {
        const apiKey = "";
        const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric`;

        try {
          this.error = null;
          const response = await axios.get(url);
          this.weather = response.data.list.slice(0, 8); //API 응답 데이터를 weather 변수에 저장
          this.createChart();
          this.showMap(this.weather.coord.lat, this.weather.coord.lon);
        } catch (error) {
          console.error ("Error fetching weather data:", error);
          this.weather = null;
          this.error = "City not found. Please try again";
        }
      },
      async fetchWeatherLocation() {
        const apiKey = "";
        if (!navigator.geolocation) {
          this.error = "Geolocation is not supported by your browser.";
          return;
        }
        navigator.geolocation.getCurrentPosition(
          async (position) => {
            const { latitude, longitude } = position.coords;
            const url = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${apiKey}&units=metric`;

            try {
              this.error = null;
              const response = await axios.get(url);
              this.weather = response.data;
            } catch (err) {
              console.error("Error fetching weather data:", err);
              this.weather = null;
              this.error = "Unable to fetch weather data for your location.";
            }
          },
          (error) => {
            console.error("Geolocation error: ", error);
            this.error = "Unable to retrieve your location.";
          }
        );
      },
      createChart() {
        const ctx = document.getElementById("weatherChart").getContext("2d");

        //이전 차트가 있으면 삭제
        if (this.chart) {
          this.chart.destroy();
        }
        
        //차트 데이터 준비
        const labels = this.forecast.map((item) => item.dt_txt.split(" ")[1]);
        const data = this.forecast.map((item) => item.main.temp);

        this.chart = new Chart(ctx, {
          type: "line",
          data : {
            labels: labels,
            datasets: [
              {
                label: "Temperature (°C)",
                data: data,
                borderColor: "blue",
                borderWidth: 2,
                fill: false,
              },
            ],
          },
          options: {
            responsive: true,
            plugins: {
              legend: {
                display: true,
              },
            },
            scales: {
              x: {
                title: {
                  display: true,
                  text: "Time",
                },
              },
              y: {
                title: {
                  display: true,
                  text: "Temperature (°C)",
                },
              },
            },
          },
        });
      },
      showMap(lat, lon) {
        this.mapVisible = true;
        if (this.map) {
          this.map.setView([lat, lon], 13);
        } else {
          this.map = L.map("map").setView([lat,lon], 13);
          //OpenStreetMap 타일 추가
          L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
            maxZoom: 18,
            attribution: '© OpenStreetMap contributors',
          }).addTo(this.map);

          //마커추가
          L.marker([lat, lon])
            .addTo(this.map)
            .bindPopup(`Weather: ${this.weather.weather[0].description}<br>Temp: ${this.weather.main.temp}°C`)
            .openPopup();
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

input {
  padding: 10px;
  font-size: 16px;
  width: 200px;
}

button {
  margin-top: 10px 20px;
  font-size: 16px;
  margin-left: 10px;
}

canvas {
  margin-top: 20px;
  max-width: 90%;
}

.cloudy {
  background: url("vue-practice\public\icons\clouds.png") no-repeat center center/cover;
}

.rainy {
  background: url("vue-practice\public\icons\rainy.png") no-repeat center center/cover;
}

.clear {
  background: url("vue-practice\public\icons\summer.png") no-repeat center center/cover;
}

.default {
  background: #f0f8ff;
}

#map {
  height: 400px;
  width: 100%;
  margin-top: 20px;
  border: 1px solid #ddd;
}
</style>