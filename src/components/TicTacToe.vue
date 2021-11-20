<template>
  <div id="tic-tac-toe" :style="ticTacToeWidth">
    <div class="span3" :style="ticTacToeWidth">
      <div class="row" :style="ticTacToeWidth">
        <h1 class="span3 text-center">Tic Tac Toe</h1>
        <div class="span3">
          <div class="input-prepend input-append">
            <input type="number" v-model="columnCount" :disabled="gameInProgress">
            <span class="add-on">Column</span>
          </div>
          <div class="input-prepend input-append">
            <span class="add-on win_text">O won</span
            ><strong id="o_win" class="win_times add-on">{{ oWinCount }}</strong
            ><span class="add-on">time(s)</span>
          </div>
          <div class="input-prepend input-append">
            <span class="add-on win_text">X won</span
            ><strong id="x_win" class="win_times add-on">{{ xWinCount }}</strong
            ><span class="add-on">time(s)</span>
          </div>
        </div>
      </div>
      <ul class="row" id="game">
        <li
          v-for="cell in cells"
          :key="cell.pos"
          @click="selectCell(cell)"
          :class="cell.class"
          class="btn span1"
        >
          {{ cell.text }}
        </li>
      </ul>
      <div class="clr">&nbsp;</div>
      <div class="row">
        <a @click="restart" class="btn-success btn span3">Restart</a>
      </div>
    </div>
  </div>
</template>

<script>
const XCellClass = "btn-primary disable";
const OCellClass = "btn-info disable";
export default {
  name: "TicTacToe",
  data() {
    return {
      cells: [],
      xWinCount: 0,
      oWinCount: 0,
      columnCount: 3,
      selectedCount: 0,
      gameInProgress: false,
      get cellCount() {
        return this.columnCount ** 2;
      },
      get ticTacToeWidth () {
        return `width: ${this.columnCount * 84.5}px;`;
      } 
    };
  },
  mounted() {
    this.setColumns();
  },
    watch: {
      columnCount: function () {
        this.setColumns()
      }
    },
  methods: {
    setColumns() {
      this.cells = [];
      for (let pos = 1; pos <= this.cellCount; pos++) {
        this.cells.push({
          pos,
          class: null,
          text: "+",
        });
      }
    },
    selectCell(cell) {
      if (cell.class) {
        alert("cell already selected");
        return;
      }
      this.gameInProgress = true;

      if (this.selectedCount % 2 == 0) this.setOCell(cell);
      else this.setXCell(cell);
      this.selectedCount++;

      if (this.selectedCount <= this.columnCount * 2 - 2) {
        return;
      }

      this.checkRowWin(cell);
      this.checkColumnWin(cell);
      this.checkCrossWin(cell)
      this.checkCrossWin(cell, false)

      if (this.selectedCount == this.cellCount) {
        return setTimeout(() => {
          alert("tie, none win");
          this.restart();
        }, 200);
      }
    },
    getCellIndex(rowNumber, columnNumber, columnPerRowCount) {
      return (rowNumber - 1) * columnPerRowCount + columnNumber - 1;
    },
    checkRowWin(cell) {
      for (let row = 1; row <= this.columnCount; row++) {
        // eslint-disable-next-line no-unused-vars
        let selectedCellInRowCount = 0;
        for (let column = 1; column <= this.columnCount; column++) {
          const cellIndex = this.getCellIndex(row, column, this.columnCount);
          const currentCell = this.cells[cellIndex];
          if (currentCell.text == cell.text) selectedCellInRowCount++;

          if (selectedCellInRowCount == 0) break;
        }

        if (selectedCellInRowCount == this.columnCount) {
          return setTimeout(() => {
            this.determineWin(cell);
          }, 100);
        }
      }
    },
    checkColumnWin(cell) {
      for (let column = 1; column <= this.columnCount; column++) {
        // eslint-disable-next-line no-unused-vars
        let selectedCellInColumnCount = 0;
        for (let row = 1; row <= this.columnCount; row++) {
          const cellIndex = this.getCellIndex(row, column, this.columnCount);
          const currentCell = this.cells[cellIndex];
          if (currentCell.text == cell.text) selectedCellInColumnCount++;

          if (selectedCellInColumnCount == 0) break;
        }

        if (selectedCellInColumnCount == this.columnCount) {
          return setTimeout(() => {
            this.determineWin(cell);
          }, 100);
        }
      }
    },
    checkCrossWin(cell, toRight = true) {
      const startPos = toRight == true ? 1 : this.columnCount;
      const additionalLoop = toRight == true ? this.columnCount + 1 : this.columnCount - 1
      let selectedCellInCrossCount = 0;
      for (
        let cellPos = startPos;
        cellPos <= this.cellCount;
        cellPos += additionalLoop
      ) {
        const currentCell = this.cells[cellPos - 1];
        console.log("🚀 ~ file: TicTacToe.vue ~ line 143 ~ checkCrossWin ~ currentCell", currentCell)
        
        if (currentCell.text == cell.text) selectedCellInCrossCount++;

        if (selectedCellInCrossCount == 0) break;
      }
        if (selectedCellInCrossCount == this.columnCount) {
          return setTimeout(() => {
            this.determineWin(cell);
          }, 100);
        }
    },
    determineWin(cell) {
      alert(`${cell.text} win, start new game`);
      this[`${cell.text.toLowerCase()}WinCount`]++;
      this.restart();
    },
    setXCell(cell) {
      cell.class = XCellClass;
      cell.text = "X";
    },
    setOCell(cell) {
      cell.class = OCellClass;
      cell.text = "O";
    },
    restart() {
      this.selectedCount = 0;
      this.gameInProgress = false;
      this.setColumns();
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#tic-tac-toe .disable {
  text-transform: uppercase;
  font-size: 30px;
  font-family: Georgia, "Times New Roman", Times, serif;
}
#tic-tac-toe #game li {
  float: left;
  padding: 0;
  list-style: none;
  text-align: center;
  margin-bottom: 20px;
  color: #fff;
  height: 60px;
  line-height: 60px;
  font-size: 40px;
  color: #ccc;
}
#tic-tac-toe #game li.disable {
  color: #fff;
}
#tic-tac-toe #game {
  float: left;
  padding: 0;
  clear: both;
}
#tic-tac-toe #reset {
  padding: 5px 10px;
  color: #fff;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  clear: both;
  cursor: pointer;
  float: left;
  text-align: center;
  text-transform: uppercase;
  outline: none;
  width: 204px;
}
.input-prepend span.pre_text {
  width: 55px;
}
.input-prepend .span1 {
  width: 93px;
}
.input-prepend {
  margin-bottom: 10px;
}
.clr {
  clear: both;
  height: 0;
}
#tic-tac-toe h1 {
  text-align: center;
  font-size: 28px;
}
#tic-tac-toe li::-moz-selection {
  background: none;
  color: #000;
}
#tic-tac-toe li::-webkit-selection {
  background: none;
  color: #000;
}
#tic-tac-toe {
  margin: 0 auto;
}
.input-append .win_times {
  background: #fff;
  width: 101px;
}
.input-append .win_text {
  width: 52px;
}
</style>
