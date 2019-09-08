<template>
<!--  <div id="app">-->
<!--    <img alt="Vue logo" src="./assets/logo.png">-->
<!--    <HelloWorld msg="Welcome to Your Vue.js App"/>-->
<!--  </div>-->

  <div id="app">
    <div class="columns">
      <div class="column">
        <ShowDate/>
      </div>

      <div class="column">
        <ShowSchedule/>
      </div>

    </div>



  </div>



</template>

<script>
import ShowDate from './components/ShowDate.vue'
import ShowSchedule from "./components/ShowSchedule.vue";

export default {
  name: 'app',
  components: {
    ShowDate,
    ShowSchedule
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;*/
  color: #2c3e50;
  margin-top: 60px;
}
</style>
