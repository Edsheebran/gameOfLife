<template>
  <div>
    <h2>The game of Life</h2>
    <div v-if="pattern[0].length === 0">
      <button @click="getRandomPattern">Get random Pattern</button>
      <button @click="generateBlanckGrid">Create my grid</button>
      <div class="game-zone" >No pattern loaded</div>
    </div>

    <div v-else  class="game-zone">
      <Grid :pattern="pattern" @change-cell="changeCell" />
      <button @click="pattern = nextState">Step</button>
      <button @click="run">Run</button>
    </div>
    <div>

    </div>
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
</style>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Grid from "./Grid.vue";
const availablePatternsUrl =
  "https://thunder-dev.flashbrand.me/recruitment/life/";

@Component({
  components: { Grid },
})
export default class Game extends Vue {
  changeCell({ row, col }: { row: number; col: number }) {
    const newState = this.pattern[row][col] === 1 ? 0 : 1;
    this.$set(this.pattern[row], col, newState);
  }
  pattern: number[][] = [[]];
  availablePatterns: string[] = [];

  // COMPUTED VALUES
  get patternSize() {
    return this.pattern.length;
  }

  // Computed value : matrix with the number of neighbour of each cell
  get neighboursGrid() {
    // eslint-disable-next-line @typescript-eslint/no-unused-vars
    const newNeighboursGrid = Array.from(Array(this.patternSize), (_) =>
      Array(this.patternSize).fill(0)
    );
    this.pattern.forEach((row, rowIndex) => {
      return row.forEach((cell, colIndex) => {
        // if the cell is alive we update its neighbours
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

  // Tell if a cell will be alive or dead
  cellNextState(row: number, col: number) {
    const neighbours = this.neighboursGrid[row][col];
    if (this.pattern[row][col] === 1 && neighbours === 2) return 1;
    return neighbours === 3 ? 1 : 0;
  }

  // Return the matrix with the future state of each cell
  get nextState() {
    return this.pattern.map((row, rowIndex) => {
      return row.map((col, colIndex) => {
        return this.cellNextState(rowIndex, colIndex);
      });
    });
  }

  // METHODS

  async getRandomPattern() {
    if (this.availablePatterns.length === 0) {
      const response = await fetch(availablePatternsUrl);
      this.availablePatterns = (await response.json()).patternList;
    }
    const randomPattern = this.availablePatterns[
      Math.floor(Math.random() * this.availablePatterns.length)
    ];
    const response = await fetch(availablePatternsUrl + randomPattern);
    this.pattern = (await response.json()).pattern;
  }
  generateBlanckGrid() {
    // eslint-disable-next-line @typescript-eslint/no-unused-vars
    this.pattern = Array.from(Array(100), (_) => Array(100).fill(0));
  }

  run() {
    setInterval(() => {
      this.pattern = this.nextState;
    }, 300);
  }
}
</script>
