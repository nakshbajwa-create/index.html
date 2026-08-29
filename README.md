# index.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Tic Tac Toe VS Computer</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  min-height: 100vh;
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #141e30, #243b55);
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.game {
  width: 94%;
  max-width: 430px;
}

h1 {
  font-size: 38px;
  margin: 10px 0;
}

.subtitle {
  opacity: 0.8;
  margin-bottom: 20px;
}

.mode {
  margin-bottom: 15px;
}

select {
  padding: 10px 15px;
  border-radius: 10px;
  border: none;
  font-size: 16px;
}

#status {
  font-size: 21px;
  font-weight: bold;
  margin: 15px;
}

.score {
  display: flex;
  justify-content: space-around;
  margin-bottom: 18px;
  font-size: 18px;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}

.cell {
  aspect-ratio: 1;
  border: none;
  border-radius: 18px;
  background: white;
  color: #222;
  font-size: 55px;
  font-weight: bold;
  cursor: pointer;
  transition: transform 0.15s, background 0.2s;
}

.cell:active {
  transform: scale(0.92);
}

.cell.x {
  color: #1677ff;
}

.cell.o {
  color: #ff3b6b;
}

.cell.win {
  animation: win 0.6s infinite alternate;
}

@keyframes win {
  from {
    transform: scale(1);
  }
  to {
    transform: scale(1.08);
  }
}

button#restart {
  margin-top: 25px;
  padding: 14px 28px;
  border: none;
  border-radius: 30px;
  background: white;
  color: #243b55;
  font-size: 17px;
  font-weight: bold;
  cursor: pointer;
}

button#restart:active {
  transform: scale(0.95);
}

.info {
  margin-top: 18px;
  font-size: 14px;
  opacity: 0.7;
}
</style>
</head>

<body>

<div class="game">

  <h1>❌ Tic Tac Toe ⭕</h1>

  <div class="subtitle">You vs Computer 🤖</div>

  <div class="mode">
    <label>Difficulty: </label>

    <select id="difficulty">
      <option value="easy">Easy 😊</option>
      <option value="medium">Medium 😎</option>
      <option value="hard">Hard 🔥</option>
    </select>
  </div>

  <div id="status">Your Turn ❌</div>

  <div class="score">
    <div>👤 You: <span id="playerScore">0</span></div>
    <div>🤖 Computer: <span id="computerScore">0</span></div>
  </div>

  <div class="board">

    <button class="cell"></button>
    <button class="cell"></button>
    <button class="cell"></button>

    <button class="cell"></button>
    <button class="cell"></button>
    <button class="cell"></button>

    <button class="cell"></button>
    <button class="cell"></button>
    <button class="cell"></button>

  </div>

  <button id="restart">🔄 New Game</button>

  <div class="info">
    You are ❌ • Computer is ⭕
  </div>

</div>

<script>

const cells = document.querySelectorAll(".cell");
const statusText = document.getElementById("status");
const restartButton = document.getElementById("restart");
const difficulty = document.getElementById("difficulty");

let board = ["","","","","","","","",""];

let gameActive = true;

let playerScore = 0;
let computerScore = 0;

const winningPatterns = [
  [0,1,2],
  [3,4,5],
  [6,7,8],
  [0,3,6],
  [1,4,7],
  [2,5,8],
  [0,4,8],
  [2,4,6]
];

cells.forEach((cell, index) => {

  cell.addEventListener("click", () => {

    if (!gameActive || board[index] !== "") {
      return;
    }

    playerMove(index);

  });

});


function playerMove(index) {

  board[index] = "X";

  cells[index].textContent = "X";
  cells[index].classList.add("x");

  let result = checkGame();

  if (result) {
    finishGame(result);
    return;
  }

  statusText.textContent = "Computer is thinking 🤖...";

  gameActive = false;

  setTimeout(computerMove, 500);

}


function computerMove() {

  if (!gameActive && board.includes("")) {

    let move;

    const level = difficulty.value;

    if (level === "easy") {

      move = randomMove();

    }

    else if (level === "medium") {

      if (Math.random() < 0.5) {
        move = bestMove();
      } else {
        move = randomMove();
      }

    }

    else {

      move = bestMove();

    }

    board[move] = "O";

    cells[move].textContent = "O";
    cells[move].classList.add("o");

    let result = checkGame();

    if (result) {

      finishGame(result);

    } else {

      gameActive = true;
      statusText.textContent = "Your Turn ❌";

    }

  }

}


function randomMove() {

  const empty = [];

  board.forEach((value, index) => {

    if (value === "") {
      empty.push(index);
    }

  });

  return empty[Math.floor(Math.random() * empty.length)];

}


function bestMove() {

  let bestScore = -Infinity;
  let move;

  for (let i = 0; i < 9; i++) {

    if (board[i] === "") {

      board[i] = "O";

      let score = minimax(board, 0, false);

      board[i] = "";

      if (score > bestScore) {

        bestScore = score;
        move = i;

      }

    }

  }

  return move;

}


function minimax(newBoard, depth, isMaximizing) {

  let result = checkGame();

  if (result === "O") {
    return 10 - depth;
  }

  if (result === "X") {
    return depth - 10;
  }

  if (result === "draw") {
    return 0;
  }


  if (isMaximizing) {

    let bestScore = -Infinity;

    for (let i = 0; i < 9; i++) {

      if (newBoard[i] === "") {

        newBoard[i] = "O";

        let score = minimax(newBoard, depth + 1, false);

        newBoard[i] = "";

        bestScore = Math.max(bestScore, score);

      }

    }

    return bestScore;

  }

  else {

    let bestScore = Infinity;

    for (let i = 0; i < 9; i++) {

      if (newBoard[i] === "") {

        newBoard[i] = "X";

        let score = minimax(newBoard, depth + 1, true);

        newBoard[i] = "";

        bestScore = Math.min(bestScore, score);

      }

    }

    return bestScore;

  }

}


function checkGame() {

  for (let pattern of winningPatterns) {

    const [a,b,c] = pattern;

    if (
      board[a] &&
      board[a] === board[b] &&
      board[a] === board[c]
    ) {

      return board[a];

    }

  }

  if (!board.includes("")) {
    return "draw";
  }

  return null;

}


function finishGame(result) {

  gameActive = false;

  if (result === "X") {

    playerScore++;

    document.getElementById("playerScore").textContent = playerScore;

    statusText.textContent = "🎉 YOU WIN!";

  }

  else if (result === "O") {

    computerScore++;

    document.getElementById("computerScore").textContent = computerScore;

    statusText.textContent = "🤖 COMPUTER WINS!";

  }

  else {

    statusText.textContent = "🤝 DRAW!";

  }

  highlightWinner();

}


function highlightWinner() {

  for (let pattern of winningPatterns) {

    const [a,b,c] = pattern;

    if (
      board[a] &&
      board[a] === board[b] &&
      board[a] === board[c]
    ) {

      cells[a].classList.add("win");
      cells[b].classList.add("win");
      cells[c].classList.add("win");

    }

  }

}


restartButton.addEventListener("click", newGame);


function newGame() {

  board = ["","","","","","","","",""];

  gameActive = true;

  statusText.textContent = "Your Turn ❌";

  cells.forEach(cell => {

    cell.textContent = "";

    cell.classList.remove("x");
    cell.classList.remove("o");
    cell.classList.remove("win");

  });

}

</script>

</body>
</html>
