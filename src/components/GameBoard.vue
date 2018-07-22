<template>
<div>
  <table class="center" id="tic-tac-toe">
    <tr>
      <td class="cell" id="0"></td>
      <td class="cell" id="1"></td>
      <td class="cell" id="2"></td>
    </tr>
    <tr>
      <td class="cell" id="3"></td>
      <td class="cell" id="4"></td>
      <td class="cell" id="5"></td>
    </tr>
    <tr>
      <td class="cell" id="6"></td>
      <td class="cell" id="7"></td>
      <td class="cell" id="8"></td>
    </tr>
  </table>
  <div>
    <button v-on:click="restart">New Game</button>
  </div>
</div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'GameBoard',
  data () {
    return {
      gameBoard: [0, 1, 2, 3, 4, 5, 6, 7, 8],
      isWin: false,
      player1: null,
      player2: null,
      ai: null,
      cells: null
    }
  },
  mounted: function () {
      this.restart()
  },
  methods: {
    restart: function () {
      this.gameBoard = [0, 1, 2, 3, 4, 5, 6, 7, 8]
      this.cells = document.querySelectorAll('.cell')
      for (var i = 0; i < this.cells.length; i++) {
         this.cells[i].innerText = ''
         this.cells[i].addEventListener('click', this.click, false)
      }
    },
    click: function (cell) {
      console.log(cell.target.id)
    }
  }
}
</script>

<style>
  td {
    border: 2px solid;
    height: 150px;
    width: 150px;
    cursor: pointer;
  }

  table {
    border-collapse: collapse;
    margin-left:auto;
    margin-right:auto;
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
</style>
