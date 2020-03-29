<template>
  <div id="theGrid">
    <input
      class="sBtn"
      type="button"
      v-for="c in cells"
      v-bind:value="c.val"
      v-bind:key="c.id"
      v-bind:id="c.id"
      v-bind:class="{ badVal: c.bad, preGen: c.preGen}"
      @keydown.stop="sBtnKeyDown(c.id, $event)"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      cells: this.initializeCells(),
      isVictory: false
    };
  },
  props: {
    difficulty: String //lower is easier, needs to be within 0-1
  },
  methods: {
    sBtnClick() {
      // console.log("click @ cell : " + id);
    },

    sBtnFocus() {
      // console.log("got focus @ cell : " + id);
    },

    sBtnBlur() {
      // console.log("lost focus @ cell : " + id);
    },

    sBtnKeyDown(id, $event) {
      //   console.log("key down @ cell : " + id);
      //   console.log($event);
      const arrInd = this.cells.findIndex(cell => cell.id === id);
      const cell = this.cells[arrInd];

      if (
        !cell.preGen &&
        ($event.key === "1" ||
          $event.key === "2" ||
          $event.key === "3" ||
          $event.key === "4" ||
          $event.key === "5" ||
          $event.key === "6" ||
          $event.key === "7" ||
          $event.key === "8" ||
          $event.key === "9")
      ) {
        //console.log($event.key + " was pressed");
        this.overwriteBtnVal(arrInd, parseInt($event.key));
      } else if (!cell.preGen && $event.key === "Backspace") {
        //console.log("Backspace was pressed");
        this.overwriteBtnVal(arrInd, "");
      } else if ($event.key === "ArrowRight") {
        this.navigateGrid(id, 1);
      } else if ($event.key === "ArrowLeft") {
        this.navigateGrid(id, -1);
      } else if ($event.key === "ArrowUp") {
        this.navigateGrid(id, -9);
      } else if ($event.key === "ArrowDown") {
        this.navigateGrid(id, 9);
      }

      this.validateGameState();
      this.checkForVictory();
    },

    overwriteBtnVal(arrInd, newVal) {
      this.cells[arrInd].val = newVal;
    },

    navigateGrid(id, move) {
      if (document.getElementById(id + move) !== null) {
        document.getElementById(id + move).focus();
      }
    },

    checkForVictory() {
      let isWin = true;
      this.cells.forEach(c => {
        if (c.val === "" || c.bad === true) {
          isWin = false;
        }
      });
      if (isWin && !this.isVictory) {
        //console.log("You Win!!!");
        alert("You win! Good job!");
        this.isVictory = true;
      }
    },

    initializeCells() {
      let arr = this.makeCells();
      let sudokuGrid = this.generateSudokuGrid();
      let i = 0;
      //const difficulty = 0.4;
      //console.log(sudokuGrid);
      arr.map(c => {
        const rand = Math.random();
        if (rand <= this.difficulty) {
          c.val = "";
        } else {
          c.val = sudokuGrid[i];
          c.preGen = true;
        }
        i++;
      });
      return arr;
    },

    validateGameState() {
      let grid = this.cellsToGrid();
      this.cells.forEach(c => {
        if (!c.preGen) {
          let hasDuplicates = false;
          let rowVals = this.getCurrentRowVals(grid, c.id - 1).filter(v => {
            if (typeof v === "number") {
              return v;
            }
          });
          let colVals = this.getCurrentColVals(grid, c.id - 1).filter(v => {
            if (typeof v === "number") {
              return v;
            }
          });
          let boxVals = this.getCurrentBoxVals(grid, c.id - 1).filter(v => {
            if (typeof v === "number") {
              return v;
            }
          });
          let vals = [];
          vals.push(rowVals);
          vals.push(colVals);
          vals.push(boxVals);
          vals.forEach(set => {
            //console.log(c.id + " " + set);
            set.forEach(x => {
              let count = 0;
              for (let i = 0; i < set.length; i++) {
                if (set[i] === x) {
                  count++;
                }
                if (count > 1) {
                  hasDuplicates = true;
                }
              }
            });
          });

          //console.log(vals);
          if (hasDuplicates && typeof c.val === "number") {
            //console.log("bad value found at " + c.id);
            c.bad = true;
            vals = [];
          } else {
            //console.log("no bad value at " + c.id);
            c.bad = false;
            vals = [];
          }
        }
      });
    },

    generateSudokuGrid() {
      let wasSuccessful = false;
      let wasErrorThrown = false;
      let grid = [];
      const oneToNine = [1, 2, 3, 4, 5, 6, 7, 8, 9];
      while (!wasSuccessful) {
        grid = this.makeZeroesGrid();

        for (let i = 0; i < grid.length; i++) {
          let legalNums = [];
          let forbiddenNums = [];
          oneToNine.forEach(n => {
            legalNums.push(n);
          });
          let currBoxVals = this.getCurrentBoxVals(grid, i);
          let currRowVals = this.getCurrentRowVals(grid, i);
          let currColVals = this.getCurrentColVals(grid, i);
          currBoxVals.forEach(val => {
            if (val !== 0 && !forbiddenNums.includes(val)) {
              forbiddenNums.push(val);
            }
          });
          currRowVals.forEach(val => {
            if (val !== 0 && !forbiddenNums.includes(val)) {
              forbiddenNums.push(val);
            }
          });
          currColVals.forEach(val => {
            if (val !== 0 && !forbiddenNums.includes(val)) {
              forbiddenNums.push(val);
            }
          });
          //console.log('forbidden: ' + forbiddenNums);
          legalNums = legalNums.filter(x => {
            if (!forbiddenNums.includes(x)) {
              return x;
            }
          });
          //console.log('legal: ' + legalNums);
          if (legalNums.length === 0) {
            wasErrorThrown = true;
            break;
          }
          let choice = Math.floor(Math.random() * Math.floor(legalNums.length));
          grid[i] = legalNums[choice];
        }
        if (wasErrorThrown) {
          wasErrorThrown = false;
          // console.log("grid gen failure");
        } else {
          wasSuccessful = true;
          // console.log("grid gen success");
        }
      }
      return grid;
    },

    makeCells() {
      let arr = [];
      for (let i = 1; i <= 81; i++) {
        arr.push({ id: i, val: "", bad: false, preGen: false });
      }
      return arr;
    },

    makeZeroesGrid() {
      let arr = [];
      for (let i = 1; i <= 81; i++) {
        arr.push(0);
      }
      return arr;
    },

    getCurrentBoxVals(grid, ind) {
      let boxNum = this.getBoxNum(ind);
      let box = [];

      let rowOffset = 0;
      if (boxNum >= 3 && boxNum <= 5) {
        rowOffset = 27;
      } else if (boxNum >= 6 && boxNum <= 8) {
        rowOffset = 54;
      }

      let colOffset = 0;
      if (boxNum === 1 || boxNum === 4 || boxNum === 7) {
        colOffset = 3;
      } else if (boxNum == 2 || boxNum == 5 || boxNum == 8) {
        colOffset = 6;
      }

      for (let i = 0; i < 9; i++) {
        let mult = 0;
        if (i >= 3 && i <= 5) {
          mult = 1;
        } else if (i >= 6 && i <= 8) {
          mult = 2;
        }
        box.push(grid[i - 3 * mult + mult * 9 + rowOffset + colOffset]);
      }
      return box;
    },

    getCurrentRowVals(grid, ind) {
      let rowNum = this.getRowNum(ind);
      let row = [];
      for (let i = 0; i < 9; i++) {
        row.push(grid[i + rowNum * 9]);
      }
      return row;
    },

    getCurrentColVals(grid, ind) {
      let colNum = this.getColNum(ind);
      let col = [];
      for (let i = 0; i < 9; i++) {
        col.push(grid[i * 9 + colNum]);
      }

      return col;
    },

    getRowNum(ind) {
      if (ind < 9) {
        return 0;
      } else if (ind < 18) {
        return 1;
      } else if (ind < 27) {
        return 2;
      } else if (ind < 36) {
        return 3;
      } else if (ind < 45) {
        return 4;
      } else if (ind < 54) {
        return 5;
      } else if (ind < 63) {
        return 6;
      } else if (ind < 72) {
        return 7;
      } else {
        return 8;
      }
    },

    getBoxNum(ind) {
      // get super-row
      let superRow = 0;
      if (ind < 27) {
        superRow = 0;
      } else if (ind < 54) {
        superRow = 1;
      } else {
        superRow = 2;
      }

      // get super-col
      let superCol = 0;
      if (ind % 9 < 3) {
        superCol = 0;
      } else if (ind % 9 < 6) {
        superCol = 1;
      } else {
        superCol = 2;
      }

      if (superRow === 0 && superCol === 0) {
        return 0;
      } else if (superRow === 0 && superCol === 1) {
        return 1;
      } else if (superRow === 0 && superCol === 2) {
        return 2;
      } else if (superRow === 1 && superCol === 0) {
        return 3;
      } else if (superRow === 1 && superCol === 1) {
        return 4;
      } else if (superRow === 1 && superCol === 2) {
        return 5;
      } else if (superRow === 2 && superCol === 0) {
        return 6;
      } else if (superRow === 2 && superCol === 1) {
        return 7;
      } else {
        return 8;
      }
    },

    getColNum(ind) {
      if (ind % 9 === 0) {
        return 0;
      } else if (ind % 9 === 1) {
        return 1;
      } else if (ind % 9 === 2) {
        return 2;
      } else if (ind % 9 === 3) {
        return 3;
      } else if (ind % 9 === 4) {
        return 4;
      } else if (ind % 9 === 5) {
        return 5;
      } else if (ind % 9 === 6) {
        return 6;
      } else if (ind % 9 === 7) {
        return 7;
      } else {
        return 8;
      }
    },

    cellsToGrid() {
      let grid = [];
      grid = this.cells.map(c => {
        return c.val;
      });
      return grid;
    }
  }
};
</script>

<style scoped>
#theGrid {
  display: grid;
  grid-template-columns: auto auto auto auto auto auto auto auto auto;
  /* justify-content: space-around; */
  row-gap: 1%;
  column-gap: 1%;
  max-width: 33%;

  margin-left: 33%;
  margin-right: 33%;
}

.sBtn {
  max-width: 100%;
  min-width: 50px;
  max-height: 100%;
  min-height: 50px;
  /* background-color: blue; */
}

.badVal {
  background-color: lightcoral;
}

.badVal:hover {
  background-color: lightpink;
}

.preGen:hover {
  background-color: lightblue;
  /* color: white; */
}

.preGen {
  background-color: lightsteelblue;
}
</style>