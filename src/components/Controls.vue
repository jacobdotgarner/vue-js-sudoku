<template>
  <div id="controls">
    <input type="button" value="New Game" />
    <div id="difficultyBlock">
      <label for="difficultyRange">Difficulty: </label><span >{{this.difficulty}}</span>
      <input
        type="range"
        min="1"
        max="9"
        v-bind:value="difficulty"
        class="slider"
        name="difficultyRange"
        id="difficultyRange"
      />
      <label>Easy ------ Tough</label>
      
    </div>
  </div>
</template>

<script>
export default {
    data() {
        return {
            difficulty: 5,
        }
    }
};
</script>

<style scoped>
#controls {
  margin-top: 60px;
  display: flex;
  justify-content: center;
}

#difficultyBlock {
    display: flex;
    flex-direction: column;
}
</style>