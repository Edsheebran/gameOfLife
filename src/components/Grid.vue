<template>
  <div>
    <button @click="update">Update</button>
    <table>
      <tr v-for="(row, rowIndex) in pattern" :key="`row-${rowIndex}`">
        <Cell
          v-for="(cell, colIndex) in row"
          :key="`cell-${rowIndex}-${colIndex}`"
          :state="cell"
        ></Cell>
      </tr>
    </table>
  </div>
</template>

<style scoped>
table,
tr,
td {
  border: 0.1px solid black;
  border-collapse: collapse;
}

table {
  margin: auto;
}
</style>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Cell from "./Cell.vue";

@Component({
  components: { Cell }
})
export default class Grid extends Vue {
  pattern: number[][] = [[]];

  async created() {
    const response = await fetch(
      "https://thunder-dev.flashbrand.me/recruitment/life/pulsar"
    );
    this.pattern = (await response.json()).pattern;
  }

  get patternSize() {
    return this.pattern.length;
  }

  get neighboursGrid() {
    // eslint-disable-next-line @typescript-eslint/no-unused-vars
    const newNeighboursGrid = Array.from(Array(this.patternSize), _ =>
      Array(this.patternSize).fill(0)
    );
    this.pattern.forEach((row, rowIndex) => {
      return row.forEach((col, colIndex) => {
        if (col === 1) {
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

  get nextState() {
    return this.pattern.map((row, rowIndex) => {
      return row.map((col, colIndex) => {
        return this.cellNextState(rowIndex, colIndex);
      });
    });
  }

  cellNextState(row: number, col: number) {
    const neighbours = this.neighboursGrid[row][col];
    if (this.pattern[row][col] === 1 && neighbours === 2) return 1;
    return neighbours === 3 ? 1 : 0;
  }

  update() {
    setInterval(() => (this.pattern = this.nextState), 100);
  }
}
</script>
