<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>-->
    <h1>Sudoku!</h1>
    <h3
      id="instructionHeader"
      v-on:click="showHideInstructions"
    >Click here to show/hide instructions</h3>
    <ul id="instructionList" v-if="areInstructionsShown">
      <li>Click on an empty cell and use the number keys to enter a value</li>
      <li>Press backspace to remove an entered value</li>
      <li>Cells turn red when a rule violation is detected</li>
      <li>Slide the difficulty selector and click the 'New Game' button to start a new game</li>
    </ul>
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
      diffVal: 0.5,
      areInstructionsShown: false
    };
  },
  methods: {
    restartGame($event) {
      console.log("restart game function called");
      this.diffVal = $event;
      this.gridKey += 1;
    },
    showHideInstructions() {
      this.areInstructionsShown = !this.areInstructionsShown;
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

.hidden {
  display: none;
}

#instructionHeader {
  cursor: pointer;
}
</style>
