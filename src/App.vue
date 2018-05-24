<template>
  <div id="app">
    <home/>
  </div>
</template>

<script>
import Home from "./components/Home.vue";

export default {
  name: "app",
  components: {
    Home
  }
};
</script>

<style>
body {
  margin: 0;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #546578bd;

  width: 100vw;
  height: 100vh;
  position: relative;
}
</style>
