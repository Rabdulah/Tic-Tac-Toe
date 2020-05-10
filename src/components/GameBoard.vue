<template>
  <div class="container">
    <div class="title">
      <h1>TIC-TAC-TOE</h1>
    </div>
    <div class="table">
      <table class="center" id="tic-tac-toe">
        <tr>
          <td class="cell" id="0"></td>
          <td class="cell vert" id="1"></td>
          <td class="cell" id="2"></td>
        </tr>
        <tr>
          <td class="cell hori" id="3"></td>
          <td class="cell vert hori" id="4"></td>
          <td class="cell hori" id="5"></td>
        </tr>
        <tr>
          <td class="cell" id="6"></td>
          <td class="cell vert" id="7"></td>
          <td class="cell" id="8"></td>
        </tr>
      </table>
    </div>
    <div class="controls">
      <div class="zoom modeBtn">
        <button v-on:click="twoPlayer" type="button" class="button2 float-left"></button>
      </div>
      <div>
      <div class="text-center">
        <div class="dropdown">
          <button
            class="btn btn-lg btn-warning dropdown-toggle"
            type="button"
            id="dropdownMenuButton"
            data-toggle="dropdown"
            aria-haspopup="true"
            aria-expanded="false"
          >AI Difficulty</button>
          <div class="dropdown-menu">
            <a class="dropdown-item" v-on:click="easy">Easy</a>
            <a class="dropdown-item" v-on:click="medium">Tough</a>
            <a class="dropdown-item" v-on:click="impossible">Impossible</a>
          </div>
        </div>
      </div>
      <div class="text-center">
        <button
          id="restart"
          type="button"
          class="btn btn-secondary btn-lg"
          v-on:click="restart"
        >New Game</button>
      </div>
      </div>
      <div class="zoom modeBtn">
        <button v-on:click="onePlayer" class="button1 float-right"></button>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: "GameBoard",
  data() {
    return {
      gameBoard: [0, 1, 2, 3, 4, 5, 6, 7, 8],
      isWin: "null",
      player1: "X",
      player2: "O",
      ai: "O",
      cells: null,
      gameMode: null,
      semaphore: 0,
      difficulty: 500,
      winConditions: [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6]
      ]
    };
  },
  mounted: function() {
    this.restart();
  },
  methods: {
    easy() {
      this.difficulty = 500;
    },
    medium() {
      this.difficulty = 800;
    },
    impossible() {
      this.difficulty = 1000;
    },
    onePlayer() {
      this.restart();
      this.gameMode = "one";
    },
    twoPlayer() {
      this.restart();
      this.gameMode = "two";
    },
    vsPC(cell) {
      this.turn(cell.target.id, this.player1);
      if (!this.isTie() && this.isWin != true) {
        if (this.handiCap() < this.difficulty) {
          this.turn(this.bestSpot(), this.ai);
        } else {
          this.turn(this.randomPick(), this.ai);
        }
      }
    },
    vsHuman(cell) {
      if (this.semaphore == 0) {
        this.turn(cell.target.id, this.player1);
        this.semaphore = 1;
      } else if (this.semaphore == 1) {
        this.turn(cell.target.id, this.player2);
        this.semaphore = 0;
      }
    },
    randomPick() {
      var available = this.freeCells();
      var rand = available[Math.floor(Math.random() * this.freeCells().length)];
      return rand;
    },
    handiCap() {
      return Math.floor(Math.random() * 999);
    },
    restart: function() {
      this.isWin = null;
      this.gameBoard = [0, 1, 2, 3, 4, 5, 6, 7, 8];
      this.cells = document.querySelectorAll(".cell");
      for (var i = 0; i < this.cells.length; i++) {
        this.cells[i].innerText = "";
        this.cells[i].addEventListener("click", this.click, false);
        this.cells[i].style.backgroundColor = "transparent";
      }
    },
    click: function(cell) {
      if (typeof this.gameBoard[cell.target.id] == "number") {
        if (this.gameMode != "two") {
          this.vsPC(cell);
        } else {
          this.vsHuman(cell);
        }
      }
    },
    turn: function(cellId, player) {
      this.gameBoard[cellId] = player;
      document.getElementById(cellId).innerText = player;
      if (this.checkWin(this.gameBoard, player)) {
        this.gameOver(this.isWin);
      }
    },
    checkWin(gameBoard, player) {
      var filled = gameBoard.reduce(
        (a, e, i) => (e === player ? a.concat(i) : a),
        []
      );
      this.isWin = null;
      for (let [i, winCon] of this.winConditions.entries()) {
        if (winCon.every(con => filled.indexOf(con) > -1)) {
          this.isWin = { index: i, player: player };
          break;
        }
      }
      return this.isWin;
    },
    gameOver(isWin) {
      for (let i of this.winConditions[isWin.index]) {
        document.getElementById(i).style.backgroundColor =
          isWin.player == this.player1 ? "#00ef55" : "#ffa57b";
      }
      for (var i = 0; i < this.cells.length; i++) {
        this.cells[i].removeEventListener("click", this.click, false);
      }
      this.isWin = true;
    },
    bestSpot() {
      return this.miniMax(this.gameBoard, this.ai).index;
    },
    miniMax(board, player) {
      let free = this.freeCells();
      if (this.checkWin(board, this.player1)) {
        return { score: -10 };
      } else if (this.checkWin(board, this.ai)) {
        return { score: 10 };
      } else if (free.length == 0) {
        return { score: 0 };
      }
      var moves = []; // possible moves available to this player instance
      for (var i = 0; i < free.length; i++) {
        var move = {};
        move.index = board[free[i]];
        board[free[i]] = player;
        if (player == this.ai) {
          var result = this.miniMax(board, this.player1);
          move.score = result.score;
        } else {
          var result = this.miniMax(board, this.ai);
          move.score = result.score;
        }
        board[free[i]] = move.index;
        moves.push(move);
      }
      var bestMove;
      if (player == this.ai) {
        var bestScore = -1000;
        for (var i = 0; i < moves.length; i++) {
          if (moves[i].score > bestScore) {
            bestScore = moves[i].score;
            bestMove = i;
          }
        }
      } else {
        var bestScore = 1000;
        for (var i = 0; i < moves.length; i++) {
          if (moves[i].score < bestScore) {
            bestScore = moves[i].score;
            bestMove = i;
          }
        }
      }

      return moves[bestMove];
    },
    freeCells() {
      let free = [];
      for (var i = 0; i < this.gameBoard.length; i++) {
        if (typeof this.gameBoard[i] == "number") {
          free.push(this.gameBoard[i]);
        }
      }
      return free;
    },
    isTie() {
      if (this.freeCells().length == 0) {
        if (this.isWin != true) {
          for (var i = 0; i < this.cells.length; i++) {
            this.cells[i].style.backgroundColor = "#6edeff";
            this.cells[i].removeEventListener("click", this.click, false);
          }
          return true;
        }
      }
      return false;
    }
  }
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
.title {
  margin: auto;
}

td {
  border: 2px solid;
  height: 120px;
  width: 150px;
  cursor: pointer;
  text-align: center;
  vertical-align: middle;
  font-family: "Comic Sans MS", cursive, sans-serif;
  font-size: 70px;
}
.table {
  margin: auto;
}
.controls {
  margin: auto 0;
  display: flex;
  justify-content: center;
}
table {
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #0da192 !important;
}
table tr:first-child td {
  border-top: 0;
}
table tr:last-child td {
  border-bottom: 0;
}
table tr td:first-child {
  border-left: 0;
}
table tr td:last-child {
  border-right: 0;
}
.modeBtn {
  margin: auto 50px;
}
.button1 {
  background: url(../assets/onePlayerAI.jpg) no-repeat;
  border-radius: 50%;
  width: 150px;
  height: 150px;
  background-size: 100%;
}
.button2 {
  background: url(../assets/twoPlayer.jpg);
  border-radius: 50%;
  width: 150px;
  height: 150px;
  background-size: 100%;
}
#dropdownMenuButton {
  background: #79ffae;
  border-color: #79ffae;
}
#restart {
  background: #2d787f;
  border-color: #2d787f;
}
@media screen and (max-width: 767px) {
  h1 {
    font-size: 40px;
  }
  .button1 {
    width: 100px;
    height: 100px;
  }
  .button2 {
    width: 100px;
    height: 100px;
  }
  .modeBtn {
    margin: auto 3px;
  }
}
@media screen and (min-width: 768px) {
  h1 {
    font-size: 4.5rem;
  }
}

.container-fluid {
  background-color: transparent;
  position: relative;
}
#table {
  padding-top: 0px;
}
.cell {
}
/* .vert {
  border-left: 10px solid;
  border-right: 10px solid;
}
.hori {
  border-top: 10px solid !important;
  border-bottom: 10px solid;
} */
h1 {
  color: #1f1e1e;
}
#restart {
  margin-top: 10px;
}
html,
body {
  background: #14bdac;
  background-size: cover;
  margin-bottom: 0px;
  padding-bottom: 0px;
  min-height: 100%;
}
.zoom {
  transition: transform 0.2s;
}

.zoom:hover {
  transform: scale(1.05);
}
</style>
