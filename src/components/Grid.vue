<template>
    <table>
      <tr v-for="(row, rowIndex) in pattern" :key="`row-${rowIndex}`">
        <td
          v-for="(cell, colIndex) in row"
          :key="`cell-${rowIndex}-${colIndex}`"
          :class="{ alive: cell === 1, cell: true }"
          @click="changeCell(rowIndex, colIndex)"
        />
      </tr>
    </table>
</template>

<style scoped>
table,
tr,
td {
  border: 0.1px solid black;
  border-collapse: collapse;
}

table {
  table-layout: fixed;
  width: 80vmin !important;
  height: 80vmin !important;
}

.alive {
  background-color: #ff6467 !important;
}
.cell {
  background-color: #35b5aa;
  max-height: 0.8vmin;
  max-width: 0.8vmin;
}
</style>

<script lang="ts">
import { Component, Prop, Emit, Vue } from "vue-property-decorator";

@Component
export default class Grid extends Vue {
  @Prop({ default: () => [[]] })
  pattern!: number[][];

  @Emit()
  changeCell(row: number, col: number) {
    return { row, col };
  }
}
</script>
