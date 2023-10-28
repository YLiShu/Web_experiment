<template>
  <div class="container">
    <div class="header">
      <h1 class="title">{{ province }}·{{ displayedCity }}</h1>
      <div class="search">
        <input type="text" v-model="city" />
        <button @click="getWeather">获取</button>
      </div>
    </div>
    <div class="content">
      <div class="time">{{ currentTime }}</div>
      <div class="weather">
        <i :class="'qi-' + weather?.icon + '-fill'"></i>
        <div class="temperature">
          <span>{{ weather?.temp }}℃</span>
          <span class="weather_text">{{ weather?.text }}</span>
        </div>
      </div>
      <div class="info_box">
        <div class="info">
          <img class="left" src="../../assets/wind.png" alt="" />
          <div class="right">
            <span>{{ weather?.windScale }}级</span>
            <span>{{ weather?.windDir }}</span>
          </div>
        </div>
        <div class="info">
          <img class="left" src="../../assets/temperature.png" alt="" />
          <div class="right">
            <span>{{ weather?.humidity }}%</span>
            <span>湿度</span>
          </div>
        </div>
        <div class="info">
          <img class="left" src="../../assets/pressure.png" alt="" />
          <div class="right">
            <span>{{ weather?.pressure }}hPa</span>
            <span>气压</span>
          </div>
        </div>
        <div class="info">
          <img class="left" src="../../assets/rainfall.png" alt="" />
          <div class="right">
            <span>{{ weather?.precip }}mm</span>
            <span>降雨量</span>
          </div>
        </div>
        <div class="info">
          <img class="left" src="../../assets/visibility.png" alt="" />
          <div class="right">
            <span>{{ weather?.vis }}km</span>
            <span>能见度</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import axios from "axios";

const city = ref("成都");
const displayedCity = ref("成都");
const province = ref("四川");
const weather = ref();
const cityId = ref();
const currentTime = ref(getCurrentTime());

function getCurrentTime() {
  const now = new Date();
  const year = now.getFullYear();
  const month = (now.getMonth() + 1).toString().padStart(2, "0");
  const day = now.getDate().toString().padStart(2, "0");
  const hours = now.getHours().toString().padStart(2, "0");
  const minutes = now.getMinutes().toString().padStart(2, "0");
  const seconds = now.getSeconds().toString().padStart(2, "0");
  return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
}

async function getWeather() {
  const API_KEY = "2581c6053e924e9e98f5abdd958861b6";
  const CITY_Id_URL = `https://geoapi.qweather.com/v2/city/lookup?location=${city.value}&key=${API_KEY}`;

  try {
    const cityIdResponse = await axios.get(CITY_Id_URL);
    console.log(cityIdResponse.data);
    displayedCity.value = city.value;
    province.value = cityIdResponse.data.location[0].adm1;
    cityId.value = cityIdResponse.data.location[0].id;

    const WEATHER_URL = `https://devapi.qweather.com/v7/weather/now?location=${cityId.value}&key=${API_KEY}`;
    const weatherResponse = await axios.get(WEATHER_URL);
    console.log(weatherResponse.data);
    weather.value = weatherResponse.data.now;
    city.value = "";
  } catch (error) {
    console.error("An error occurred while fetching data: ", error);
  }
}

onMounted(() => {
  getWeather();
  setInterval(() => {
    currentTime.value = getCurrentTime();
  }, 1000);
});
</script>


<style lang="scss" scoped>
.container {
  box-sizing: border-box;
  padding: 1rem;
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 5px;
  background: rgba(0, 0, 0, 0.7);

  .header {
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    margin-top: 10px;
    display: flex;
    align-items: center;
    width: 100%;
    height: 120px;
    justify-content: center;

    .title {
      font-size: 45px;
      margin-right: 25px;
      color: rgba(255, 255, 255, 0.5);
    }

    input {
      outline: none;
      padding: 5px;
      color: #fff;
      font-size: 16px;
      width: 180px;
      border-radius: 5px;
      margin-right: 5px;
      background: rgba(255, 255, 255, 0.3);
      border: 1px solid rgba(255, 255, 255, 0.3);
      font-family: "隶书";
    }

    button {
      font-size: 16px;
      font-family: "隶书";
      width: 55px;
      color: #fff;
      padding: 5px;
      outline: none;
      background: rgba(255, 255, 255, 0.3);
      border-radius: 5px;
      border: 1px solid rgba(255, 255, 255, 0.3);
    }
  }

  .content {
    height: 100%;
    width: 100%;
    margin-top: 20px;
    display: flex;
    box-sizing: border-box;
    padding: 10px;
    align-items: center;
    flex-direction: column;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    border-radius: 10px;

    .time {
      font-family: "Consolas";
      font-size: 60px;
      color: rgba(255, 255, 255, 0.5);
    }

    .weather {
      margin-top: 10px;
      width: 100%;
      font-family: "Consolas";
      font-size: 100px;
      color: rgba(255, 255, 255, 0.5);
      display: flex;
      justify-content: center;
      align-items: center;

      .temperature {
        margin-left: 20px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: flex-start;
        font-family: "Consolas";

        .weather_text {
          font-family: "隶书";
          font-size: 40px;
        }
      }

      i {
        font-size: 250px;
      }
    }

    .info_box {
      margin-top: 30px;
      display: flex;
      height: 100px;
      width: 80%;
      border-radius: 10px;
      background: rgba(255, 255, 255, 0.1);
      justify-content: space-around;

      .info {
        display: flex;
        align-items: center;
        justify-content: center;

        .left {
          width: 36px;
          height: 36px;
        }

        .right {
          margin-left: 20px;
          width: 100%;
          height: 100%;
          display: flex;
          justify-content: center;
          flex-direction: column;
        }
      }
    }
  }
}
</style>