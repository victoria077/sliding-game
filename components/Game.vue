<template>
  <div class="container">
    <div>
      <p>Время: {{time}}</p>
      <p v-model="steps" class="steps">Количество ходов: {{steps}}</p>
    </div>
    <div class="game-board">
      <div class="row" v-for="(row,y) in cells" :key="row.toString()">
        <div class="tile" v-for="(item,x) in row" :class="{ hole:  item == '' }" :key="item" @click="step(x,y)">
          {{ item }}
        </div>
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
        holePos: [size - 1, size - 1],
        steps: 0,
        begin: 0,
        timeStart: 0,
        timeEnd: 0,
        between: null
      };
    },
    created() {
      this.shuffleBoard(this.size * 40);
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
        let map = new Array(size).fill(0).map((x, v) => new Array(size).fill(0).map((y, u) => u + v * size + 1));
        map[size - 1][size - 1] = '';
        return map
      },
      checkOption(opt, cell) {
        let newXPos = opt[0] + cell[0]
        let newYPos = opt[1] + cell[1]
        return newXPos >= 0 && newYPos >= 0 && newXPos < this.size && newYPos < this.size
      },
      getOptions(cell) {
        return [[-1, 0], [0, -1], [1, 0], [0, 1]].filter(opt => this.checkOption(opt, cell))
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
      verifyBoard() {
        for (let y = 0; y <= this.size; y++) {
          for (let x = 0; x < this.size; x++) {
            let cellNum = y * this.size + x + 1

            if (cellNum == this.size * this.size)
              return true;
            else if (this.cells[y][x] != cellNum)
              return false;
          }
        }
      },
      makeMove(move) {
        let newXPos = this.holePos[0] + move[0]
        let newYPos = this.holePos[1] + move[1]
        this.cells[this.holePos[1]][this.holePos[0]] = this.cells[newYPos][newXPos]
        this.cells[newYPos][newXPos] = ''
        this.holePos[0] = newXPos
        this.holePos[1] = newYPos
        this.$forceUpdate();
      },
      checkCell(x, y) {
        let emptyX = this.holePos[0]
        let emptyY = this.holePos[1]
        return ((y === emptyY && (x + 1 === emptyX || x - 1 === emptyX))
          ||
          (x === emptyX && (y + 1 === emptyY || y - 1 === emptyY)))
      },
      step(x, y) {
        if (this.checkCell(x, y)) {
          this.interval();
          this.begin += 1
          this.steps += 1
          let moveX = x - this.holePos[0]
          let moveY = y - this.holePos[1]
          this.makeMove([moveX, moveY])
          if (this.verifyBoard())
            alert("Победа!")
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

    .row {
      display: flex;
      flex-direction: row;

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
