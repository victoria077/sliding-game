<template>
  <div class="container">
    <div>
      <p>Время: {{time}}</p>
      <p v-model="steps" class="steps">Количество ходов: {{steps}}</p>
    </div>
    <div class="game-board">
        <div class="tile" v-for="(item,x) in cells" :class="{ hole:  item == '' }" :key="item" @click="clicked=item; step(item, x)">
          {{ item }}
        </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Game",
    data() {
      let size = 4;
      return {
        size: size,
        cells: this.generateCells(size),
        holePos: size * size - 1,
        steps: 0,
        begin: 0,
        timeStart: 0,
        timeEnd: 0,
        between: null,
        xPos: null,
        yPos: null,
        holeXPos: size - 1,
        holeYPos: size - 1,
        clicked: null
      };
    },
    created() {
      this.shuffleBoard(7);
    },
    computed: {
      minutes() {
        return Math.floor((this.between % 3600000) / 60000);
      },
      seconds() {
        return Math.floor((this.between % 60000) / 1000);
      },
      time() {
        return (this.minutes < 10 ? '0' + this.minutes : this.minutes) + ":"
          + (this.seconds < 10 ? '0' + this.seconds : this.seconds);
      },
    },
    methods: {
      generateCells(size) {
        let map = [...Array(size ** 2 + 1).keys()].slice(1);
        map[size ** 2-1] = "";
        return map
      },
      checkOption(opt) {
        if(opt >= 0 && opt < this.size * this.size){
          return opt
        }
      },
      getOptions() {
        return [this.holePos - 1, this.holePos + 1, this.holePos + this.size, this.holePos - this.size]
          .filter(opt => this.checkOption(opt))
      },
      getRndInteger(min, max) {
        return Math.floor(Math.random() * (max - min)) + min;
      },
      shuffleBoard(nIter) {
        for (let i = 0; i < nIter; i++) {
          let options = this.getOptions(this.holePos);
          let nOpt = this.getRndInteger(0, options.length);
          let selectedMove = options[nOpt]
          this.makeMove(selectedMove)
        }
      },
      makeMove(index) {
        const temp = this.cells[index];
        this.cells[index] = ""
        this.cells[this.holePos] = temp
        this.holePos = index;

        [this.holeXPos,this.holeYPos] = this.toXY(index)
        this.$forceUpdate();
      },
      toXY(index){
        return [Math.floor(index % this.size), Math.floor(index / this.size)]
      },
      checkCell(item, index) {
        [this.xPos,this.yPos] = this.toXY(index)

        let canMove = (this.yPos  === this.holeYPos && (this.xPos + 1 === this.holeXPos || this.xPos - 1 === this.holeXPos))
          ||
          (this.xPos === this.holeXPos && (this.yPos  + 1 === this.holeYPos || this.yPos  - 1 === this.holeYPos));

        if (canMove)
          return true

        return false
      },
      verifyBoard() {
        for (let y = 0; y < this.size ** 2 - 1; y++) {
          if (this.cells[y] != y + 1){
            return false
          }
        }
        return true
      },
      step(x, y) {
        if (this.checkCell(x, y)) {
          this.interval()
          this.begin += 1
          this.steps += 1
          this.makeMove(y)
          if (this.verifyBoard()){
            alert("Победа!");

          }
        }
      },
      interval() {
        if (!this.begin) {
          this.timeStart = new Date().getTime();
          this.timeEnd = new Date().getTime();
          setInterval(() => {
            this.timeEnd = new Date().getTime();
            this.between = this.timeEnd - this.timeStart;
          }, 1000);
        }
      },
    }
  }
</script>

<style scoped lang="scss">

  .container {
    display: flex;
    justify-content: center;

    p {
      color: white;
      font-size: 30px;
      margin: 20px 40px;
    }

    .game-board {
      width: 400px;
      height: 400px;
      display: flex;
      flex-direction: row;
      flex-wrap: wrap;

      .tile {
        border: 1px solid white;
        height: 100px;
        width: 100px;
        color: white;
        font-size: 40px;
        display: flex;
        justify-content: center;
        align-items: center;
      }

      .hole {
        background: white;
      }
    }
  }
</style>
