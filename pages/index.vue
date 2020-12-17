<template>
  <div>
    <Game></Game>
  </div>
</template>

<script>
  import Game from "../components/Game"
export default {
  components: {
    Game
  }
}
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

  body{
    background-color: #1b1b1b;
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
</style>
