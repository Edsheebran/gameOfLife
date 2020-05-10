<template>
<div>
  <div id="app">
    <svg class="right-corner" width="20vh" height="15vh">
      <rect width="20vh" height="1em" style="fill:#ff6467" />
      <rect width="1em" height="15vh" style="fill:#ff6467" />
    </svg>
    <Game />
    <svg class="left-corner">
      <rect width="20vh" height="1em" style="fill:#35b5aa" />
      <rect width="1em" height="15vh" style="fill:#35b5aa" />
    </svg>
  </div>
</div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Game from "./components/Game.vue";

@Component({
  components: {
    Game
  }
})
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 5vh;
}
.right-corner {
  z-index: -1;
  position: absolute;
  top:2vh;
  left:2vw;
}
.left-corner {
  z-index: -1;
  position: absolute;
  bottom:2vh;
  right:2vw;
  transform: scale(-1,-1);
}

</style>
