<template>
  <div class="grid">
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
.grid{
  transition-property: visibility,height;
  transition-duration: 5s;
}
.alive {
  background-color: #ff6467 !important;
}
.cell {
  background-color: #35b5aa;
  height: 0.5vmin;
  width: 0.5vmin;
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
