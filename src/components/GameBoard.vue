<template>
<div>
  <table id="tic-tac-toe">
    <tr>
      <td>X</td>
      <td>O</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>X</td>
      <td></td>
      <td></td>
    </tr>
  </table>
</div>
</template>

<script>
  // http://www.madcoder.me/posts/view/1/simple-tic-tac-toe-javascript
  //   https://mostafa-samir.github.io/Tic-Tac-Toe-AI/
  //     https://medium.freecodecamp.org/how-to-make-your-tic-tac-toe-game-unbeatable-by-using-the-minimax-algorithm-9d690bad4b37
export default {
  name: 'GameBoard',
  data () {
    return {
      gameBoard: [1, 2, 3, 4, 5, 6, 7, 8],
      isWin: false
    }
  }
}
</script>
