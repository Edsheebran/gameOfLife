<template>
  <td :class="{ alive: state, cell: true }"></td>
</template>

<style scoped>
.alive {
  background-color: black;
}
.cell {
  height: 0.5vmin;
  width: 0.5vmin;
}
</style>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class Cell extends Vue {
  @Prop({ default: 0 })
  state!: number;
}
</script>
