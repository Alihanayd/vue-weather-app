<script setup></script>
<template>
  <div class="main">
    <div class="search-container">
      <input
        type="text"
        class="search-bar"
        placeholder="Search for a city.."
        v-model="city"
        @keyup.enter="getData()"
      />
      <div class="error-cnt">
        <span class="error-msg" v-show="!isOkay">Böyle bir şehir yok</span>
      </div>
      <div class="btn-container">
        <button @click="getData()" class="search-btn">Search</button>
      </div>
    </div>
    <div class="results-container" v-show="Object.keys(datas).length > 0">
      <div class="city">{{ capitalizeFirstLetter(selectedCity) }}</div>
      <div class="date">{{ datas.date }}</div>
      <div class="day">{{ datas.day }}</div>
      <div class="degree">{{ datas.degree + "°C" }}</div>
      <div class="desc">
        {{ datas.description }}
        <img :src="`${datas.icon}`" alt="" class="icon" />
      </div>
    </div>
  </div>
</template>
<script>
import { API_TOKEN, API_URL } from "../consts";
export default {
  data() {
    return {
      city: "",
      selectedCity: "",
      datas: {},
      isOkay: true,
    };
  },
  methods: {
    handleError(res) {
      if (!res.ok) throw Error(res.statusText);
      return res;
    },

    getData() {
      this.datas = {};
      this.selectedCity = this.city;

      fetch(
        `${API_URL}getWeather?data.lang=tr&data.city=${this.selectedCity}`,
        {
          headers: {
            authorization: API_TOKEN,
          },
        }
      )
        .then((response) => response.json())
        .catch((error) => alert(error))
        .then((data) => {
          if (data?.result) {
            this.setResults(data?.result[0]);
            this.isOkay = true;
            console.log(this.isOkay);
          } else {
            this.isOkay = false;
            console.log(this.isOkay);
          }
        });

      this.city = "";
    },
    setResults(results) {
      this.datas = results;
    },
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    },
  },
};
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.main {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.search-container {
  margin-bottom: 60px;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.search-bar {
  font-size: 24px;
  outline: none;
  border: none;
  border-radius: 10px;
  padding: 8px 16px;
  width: 50%;
}
.error-cnt {
  width: 50%;
  text-align: left;
}
.error-msg {
  color: rgb(250, 6, 6);
}
.btn-container {
  display: flex;
  justify-content: center;
}
.search-btn {
  background: none;
  color: #fff;
  border: 2px #98a886 solid;
  font: inherit;
  line-height: 1;
  margin: 0.5em;
  padding: 1em 2em;
  transition: 0.2s;
  cursor: pointer;
}
.search-btn:hover {
  background-color: rgb(152, 168, 134);
  box-shadow: inset 0 0 0 rgba(152, 168, 134, 0.5),
    0 0 1.5em rgba(152, 168, 134, 0.7);
}
.results-container {
  font-size: 30px;
  color: #c4a69d;
}
.desc img {
  width: 16px;
  height: 16px;
}
</style>
