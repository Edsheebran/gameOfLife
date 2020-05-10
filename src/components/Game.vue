<template>
  <div>
    <h2>The game of Life</h2>
    <div v-if="pattern[0].length === 0">
      <button class="btn" @click="getRandomPattern">Get random Pattern</button>
      <button class="btn" @click="generateBlanckGrid">Create my grid</button>
      <div class="game-zone">{{ interfaceMessage }}</div>
    </div>

    <div v-else class="game-zone">
      <Grid :pattern="pattern" @change-cell="changeCell" />
      <button class="btn" :disabled="isRunning" @click="pattern = nextState">
        Step
      </button>
      <button class="btn" :disabled="isRunning" @click="run">
        {{ isRunning ? "Running..." : "Run" }}
      </button>
    </div>
    <div></div>
  </div>
</template>

<style scoped>
.game-zone {
  margin: 0;
  position: absolute;
  top: 50%;
  left: 50%;
  max-width: 80vmin;
  max-height: 80vmin;
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}

.btn {
  background-color: #35b5aa; /* light blue */
  border: none;
  color: white;
  padding: 9px 16px;
  margin: 1%;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  border-radius: 5px;
  transition-duration: 0.4s;
}

.btn:hover {
  background-color: #ff6467; /* light red */
}
.btn:disabled {
  background-color: grey;
}
</style>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Grid from "./Grid.vue";
const availablePatternsUrl =
  "https://thunder-dev.flashbrand.me/recruitment/life/";

/**
 * Controller with the logical part of the game
 */
@Component({
  components: { Grid },
})
export default class Game extends Vue {
  /**
   * Grid of the state of the game
   */
  pattern: number[][] = [[]];
  isRunning = false;
  interfaceMessage = "No pattern loaded";

  //#region COMPUTED_VALUES

  /**
   * Computed value with the size of the grid
   */
  get patternSize() {
    return this.pattern.length;
  }

  /**
   * Computed array with the number of alive cell
   * in the neigbourhoud of each cell
   */
  get neighboursGrid() {
    const newNeighboursGrid = Array.from(Array(this.patternSize), () =>
      Array(this.patternSize).fill(0)
    );
    this.pattern.forEach((row, rowIndex) => {
      return row.forEach((cell, colIndex) => {
        if (cell === 1) {
          const rowMin = Math.max(rowIndex - 1, 0);
          const rowMax = Math.min(rowIndex + 1, this.patternSize - 1);

          const colMin = Math.max(colIndex - 1, 0);
          const colMax = Math.min(colIndex + 1, this.patternSize - 1);
          for (let x = rowMin; x <= rowMax; ++x) {
            for (let y = colMin; y <= colMax; ++y) {
              if (x !== rowIndex || y !== colIndex) {
                newNeighboursGrid[x][y] += 1;
              }
            }
          }
        }
      });
    });
    return newNeighboursGrid;
  }

  /**
   * Return the matrix with the future state of each cell
   */
  get nextState() {
    return this.pattern.map((row, rowIndex) => {
      return row.map((col, colIndex) => {
        return this.cellNextState(rowIndex, colIndex);
      });
    });
  }
  //#endregion COMPUTED_VALUES

  //#region Methods

  /**
   * Fetch the list of available pattern and load a random one into this.pattern
   */
  async getRandomPattern() {
    try {
      let response = await fetch(availablePatternsUrl);
      let availablePatterns: string[] = [];
      if (response.status === 200) {
        availablePatterns = (await response.json()).patternList;
      } else {
        this.interfaceMessage = "Oops, we could not load the pattern !";
        return;
      }
      const randomPattern =
        availablePatterns[Math.floor(Math.random() * availablePatterns.length)];
      response = await fetch(availablePatternsUrl + encodeURI(randomPattern));
      if (response.status === 200) {
        this.pattern = (await response.json()).pattern;
      } else {
        this.interfaceMessage = "Oops, we could not load the pattern !";
        return;
      }
    } catch (error) {
      this.interfaceMessage = "Oops, we could not load the pattern !";
    }
  }

  /**
   * this.pattern became a 100x100 grid of dead cells
   */
  generateBlanckGrid() {
    this.pattern = Array.from(Array(100), () => Array(100).fill(0));
  }

  /**
   * Toggle the state of a cell with its coordinates
   * @param {Object} coordinates - the coordinates of the cell
   * @param {number} coordinates.row - index of the row of the cell
   * @param {number} coordinates.col - index of the column of the cell
   */
  changeCell({ row, col }: { row: number; col: number }) {
    const newState = this.pattern[row][col] === 1 ? 0 : 1;
    this.$set(this.pattern[row], col, newState);
  }

  /**
   * Tell if a cell will be alive or dead for the next state
   * @returns {number}
   */
  cellNextState(row: number, col: number) {
    const neighbours = this.neighboursGrid[row][col];
    if (this.pattern[row][col] === 1 && neighbours === 2) return 1;
    return neighbours === 3 ? 1 : 0;
  }

  /**
   * Start the simulation
   */
  run() {
    this.isRunning = true;
    setInterval(() => {
      this.pattern = this.nextState;
    }, 300);
  }
  //#endregion Methods
}
</script>
