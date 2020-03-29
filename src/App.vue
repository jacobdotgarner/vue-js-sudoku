<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>-->
    <h1>Sudoku!</h1>
    <sudoku-grid v-bind:key="gridKey" v-bind:difficulty="diffVal"></sudoku-grid>
    <controls v-on:restart-game="restartGame"></controls>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import SudokuGrid from "./components/SudokuGrid";
import Controls from "./components/Controls";

export default {
  name: "App",
  components: {
    // HelloWorld
    SudokuGrid,
    Controls
  },
  data() {
    return {
      gridKey: 0,
      diffVal: 0.5
    };
  },
  methods: {
    restartGame($event) {
      console.log("restart game function called");
      this.diffVal = $event;
      this.gridKey += 1;
    }
  }
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
