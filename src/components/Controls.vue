<template>
  <div id="controls">
    <input type="button" value="New Game" v-on:click="$emit('restart-game', difficulty)" />
    <div id="difficultyBlock">
      <label for="difficultyRange">New Game Difficulty:</label>
      <span>{{this.difficulty}}</span>
      <input
        type="range"
        min="0.1"
        max="0.9"
        v-model.number="difficulty"
        step="0.1"
        class="slider"
        name="difficultyRange"
        id="difficultyRange"
      />
      <label>Easier ------ Tougher</label>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      difficulty: 0.5
    };
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
  margin-left: 30px;
}
</style>