<template>
  <div id="app">
    <h1>Weather App</h1>
    <p>{{message}}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: "hello! welcome to the Weather App!"
    }; //vue.js에서 이부분은 필수
  }
};

</script>

<style>
#app {
  text-align: center;
  margin-top: 50px;
}
</style>

