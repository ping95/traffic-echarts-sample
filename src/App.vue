<template>
  <TrafficInout />
  <br /><br /><br />
  <TrafficInoutBound />
</template>

<script>
import TrafficInout from "./components/TrafficInout.vue";
import TrafficInoutBound from "./components/TrafficInoutBound.vue";
export default {
  name: "App",
  components: {
    TrafficInout,
    TrafficInoutBound,
    //MutipleChart,
    //LongChart,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
