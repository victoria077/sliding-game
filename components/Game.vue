<template>
  <div class="container">
    <div>
      <p>Время: {{time}}</p>
      <p v-model="steps" class="steps">Количество ходов: {{steps}}</p>
    </div>
    <div class="game-board" v-model="gameBoard" @click="step()">
      <div class="tile" v-for="tile of tiles" :key="tile" @click="clicked=tile">{{tile}}</div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Game",
    data() {
      return {
        gameBoard: null,
        tiles: [], // ячейки
        clicked: null, // элемент, на который нажал пользователь
        steps: 0, // количество шагов
        begin: 0,
        timeStart: 0,
        timeEnd: 0,
        between: null
      }
    },
    computed: {
      // таймер
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
    mounted() {
      // устанавливаем ячейки в разном порядке
      this.tiles = ['1', '2', '3', '4', '5', '6', '7', '8'].sort(() => Math.random() - 0.5).concat([''])
    },
    methods: {
      //функция для запуска таймера
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
      step() {
        const gameState = [
          [this.tiles[0], this.tiles[1], this.tiles[2]],
          [this.tiles[3], this.tiles[4], this.tiles[5]],
          [this.tiles[6], this.tiles[7], this.tiles[8]],
        ]


        //находим позиции элементов
        let x, y;

        gameState.forEach((row, rowIndex) => {
          row.forEach((column, columnIndex) => {
            if (column == this.clicked) {
              x = rowIndex;
              y = columnIndex;
            }
          })
        })

        //находим позицию пустой ячейки
        let emptyX, emptyY;

        gameState.forEach((row, rowIndex) => {
          row.forEach((column, columnIndex) => {
            if (column == '') {
              emptyX = rowIndex;
              emptyY = columnIndex;
            }
          })
        })


        // проверяем можно ли сделать ход
        if ((y === emptyY && (x + 1 === emptyX || x - 1 === emptyX))
          ||
          (x === emptyX && (y + 1 === emptyY || y - 1 === emptyY))) {
          this.interval();
          this.begin += 1
          this.steps += 1
          const temp = gameState[x][y];
          gameState[x][y] = gameState[emptyX][emptyY];
          gameState[emptyX][emptyY] = temp;


          let tiles = this.tiles
          let el = this.clicked
          let newPositions = []

          // находим индексы пустой ячейки и ячейки, на которую нажали.
          // формируем массив с новым порядком элементов.
          function render() {
            let indEmpty = tiles.indexOf('')
            let indClicked = tiles.indexOf(el)
            tiles[indEmpty] = el
            tiles[indClicked] = ''
            newPositions = tiles
            tiles.forEach((el, ind) => {
              while (el[ind] == 9) {
                newPositions.push(el)
              }
            })
          }

          // вызываем функцию, которая возвращает новый порядок ячеек
          render()
          newPositions = JSON.parse(JSON.stringify(newPositions))
          this.tiles = newPositions

          // проверяем в правильном ли порядке стоят ячейки
          if (JSON.stringify(this.tiles) === JSON.stringify(['1', '2', '3', '4', '5', '6', '7', '8', ''])) {
            alert("Победа!")
          }
        }
      }
    }
  }
</script>

<style scoped lang="scss">
  .container {
    display: flex;
    justify-content: center;
    align-items: center;

    p {
      color: white;
      font-size: 30px;
      margin: 20px 40px;
    }

    .game-board {
      width: 450px;
      height: 450px;
      display: flex;
      flex-wrap: wrap;

      .tile {
        border: 2px solid white;
        height: 150px;
        width: 150px;
        color: white;
        font-size: 40px;
        display: flex;
        justify-content: center;
        align-items: center;
      }
    }
  }
</style>
