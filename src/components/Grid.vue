<template>
  <table>
    <tr v-for="(row, rowIndex) in pattern" :key="`row-${rowIndex}`">
      <td
        v-for="(col, colIndex) in row"
        :key="`cel-${rowIndex}-${colIndex}`"
        :class="{alive:col}"
      ></td>
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
  margin: auto;
}

td {
  height: 0.5vmin;
  width: 0.5vmin;
}

.alive {
  background-color: black;
}
</style>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class Grid extends Vue {
  @Prop({ default: "HAHa" })
  message!: string;

  pattern: number[][] = [[]];

  async created() {
    const response = await fetch(
      "https://thunder-dev.flashbrand.me/recruitment/life/gosperGliderGun"
    );
    this.pattern = (await response.json()).pattern;
  }
}
</script>
