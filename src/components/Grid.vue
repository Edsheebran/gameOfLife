<template>
  <table class="grid container">
    <tr v-for="(row, rowIndex) in pattern" :key="`row-${rowIndex}`">
      <td
        v-for="(cell, colIndex) in row"
        :key="`cell-${rowIndex}-${colIndex}`"
        :class="{ alive: cell === 1, cell: true, grid: true }"
        @click="changeCell(rowIndex, colIndex)"
      />
    </tr>
  </table>
</template>

<style scoped>
.container {
  table-layout: fixed;
  width: 80vmin;
  height: 80vmin;
}

.grid {
  border: 0.1px solid black;
  border-collapse: collapse;
}

.cell.alive {
  background-color: #35b5aa; /* light blue */
}
.cell {
  background-color: white;
  max-height: 0.8vmin;
  max-width: 0.8vmin;
}
</style>

<script lang="ts">
import { Component, Prop, Emit, Vue } from "vue-property-decorator";
/**
 * Component to display the game of life
 */
@Component
export default class Grid extends Vue {
  /**
   * Grid with the state of the cells
   */
  @Prop({ default: () => [[]] })
  pattern!: number[][];

  /**
   * Event to emit when cell is clicked to change its state
   */
  @Emit()
  changeCell(row: number, col: number) {
    return { row, col };
  }
}
</script>
