<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>(X-O)-->Game</title>
  <link rel="icon" href="X-O.icon">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    body, html {
      height: 100%;
      font-family: system-ui;
    }
    body {
      background: linear-gradient(135deg ,blue ,silver ,gold ,red);
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .game {
      width: 100%;
      max-width: 400px;
      text-align: center;
      color: white;
      font-weight: 500;
    }
    .title {
      color: black;
      background: white;
      border: double 10px;
      width: 200px;
      margin: auto;
      border-radius: 20px;
      padding: 10px;
      font-size: 24px;
      transition: background-color 0.3s, color 0.3s;
    }
    .grid {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      margin: 20px 0;
    }
    .square {
      width: 30%;
      height: 30vw;
      max-width: 120px;
      max-height: 120px;
      background: black;
      margin: 5px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 50px;
      font-weight: bold;
      color: white;
      border-radius: 10px;
      transition: background-color 0.3s;
    }
    .square.x {
      color: white; /* لون X */
    }
    .square.o {
      color: black; /* لون O */
    }
    .square.x-played {
      background-color: red; /* لون الخلفية لـ X */
    }
    .square.o-played {
      background-color: blue; /* لون الخلفية لـ O */
    }
    .winner.x {
      background: gold !important;
    }
    .winner.o {
      background: silver !important;
    }
    .scoreboard1 {
      background: black;
      padding: 10px;
      display: inline-block;
      border-radius: 10px;
      justify-content: space-between;
      align-items: center;
      margin-top: 20px;
      width: 140px;
      margin: auto;
    }
    .scoreboard2 {
      background: black;
      padding: 10px;
      display: inline-block;
      border-radius: 10px;
      justify-content: space-between;
      align-items: center;
      margin-top: 20px;
      width: 140px;
      margin: auto;
    }
    .score1 span {
      color: gold; /* لون النقاط لـ X */
    }
    .score2 span {
      color: silver; /* لون النقاط لـ O */
    }
    .reset {
      height: 60px;
      width: 60px;
      background-size: cover;
      background-position: center;
      position: fixed;
      top: 35px;
      left: 90%;
      transform: translateX(-50%);
      border: 7.5px double;
      border-radius: 30px;
      transition: transform 0.3s ease;
    }
  .reset:active{
    color: blue;
  }
  </style>
</head>
<body>
  <div class="game">
    <div class="title"><span>(X-O)</span> GAME</div>
    <div class="grid">
      <div class="square" id="item1"></div>
      <div class="square" id="item2"></div>
      <div class="square" id="item3"></div>
      <div class="square" id="item4"></div>
      <div class="square" id="item5"></div>
      <div class="square" id="item6"></div>
      <div class="square" id="item7"></div>
      <div class="square" id="item8"></div>
      <div class="square" id="item9"></div>
    </div>
    <div class="scoreboard1">
      <div class="score1">(X) Wins --> <span id="x-wins">0</span></div>
    </div>
    <div class="scoreboard2">
      <div class="score2">(O) Wins --> <span id="o-wins">0</span></div>
    </div>
    <button class="reset" onclick="resetGame()"><h1>🔁</h1></button>
  </div>
  <script>
    let turn = 'x';
    let xWins = sessionStorage.getItem('xWins') ? parseInt(sessionStorage.getItem('xWins')) : 0;
    let oWins = sessionStorage.getItem('oWins') ? parseInt(sessionStorage.getItem('oWins')) : 0;

    function updateScoreboard() {
      document.getElementById('x-wins').innerText = xWins;
      document.getElementById('o-wins').innerText = oWins;
    }

    function setWinner(cells) {
      const winnerClass = turn === 'x' ? 'o' : 'x';
      cells.forEach(cellId => {
        document.getElementById(cellId).classList.add('winner', winnerClass);
      });
      const title = document.querySelector('.title span');
      title.innerText = `Player ${winnerClass.toUpperCase()} Wins!`;
      document.querySelector('.title').style.backgroundColor = winnerClass === 'x' ? 'gold' : 'silver';
      document.querySelector('.title').style.color = winnerClass === 'x' ? 'black' : 'white';
      if (winnerClass === 'x') {
        xWins++;
        sessionStorage.setItem('xWins', xWins);
      } else {
        oWins++;
        sessionStorage.setItem('oWins', oWins);
      }
      updateScoreboard();
      setTimeout(() => location.reload(), 2000);
    }

    function checkWinner() {
      const winPatterns = [
        ['item1', 'item2', 'item3'], ['item4', 'item5', 'item6'], ['item7', 'item8', 'item9'],
        ['item1', 'item4', 'item7'], ['item2', 'item5', 'item8'], ['item3', 'item6', 'item9'],
        ['item1', 'item5', 'item9'], ['item3', 'item5', 'item7']
      ];
      for (let pattern of winPatterns) {
        const [a, b, c] = pattern;
        const squares = [
          document.getElementById(a).innerHTML,
          document.getElementById(b).innerHTML,
          document.getElementById(c).innerHTML
        ];
        if (squares[0] && squares[0] === squares[1] && squares[1] === squares[2]) {
          setWinner(pattern);
          return;
        }
      }
      if ([...document.querySelectorAll('.square')].every(square => square.innerHTML !== '')) {
        const title = document.querySelector('.title span');
        title.innerText = "It's a draw!";
        document.querySelector('.title').style.backgroundColor = 'purple'; // لون العنوان عند التعادل
        document.querySelector('.title').style.color = 'white';
        setTimeout(() => location.reload(), 2000);
      }
    }

    function game(id) {
      const element = document.getElementById(id);
      if (element.innerHTML === '' && !document.querySelector('.winner')) {
        element.innerHTML = turn;
        element.classList.add(turn, `${turn}-played`);
        turn = (turn === 'x') ? 'o' : 'x';
        document.querySelector('.title span').innerText = turn.toUpperCase();
      }
      checkWinner();
    }

    document.querySelectorAll('.square').forEach(square => {
      square.addEventListener('click', function () {
        game(this.id);
      });
    });

    function resetGame() {
      document.querySelectorAll('.square').forEach(square => {
        square.innerHTML = '';
        square.classList.remove('winner', 'x', 'o', 'x-played', 'o-played');
      });
      xWins = 0;
      oWins = 0;
      sessionStorage.setItem('xWins', 0);
      sessionStorage.setItem('oWins', 0);
      updateScoreboard();
      const title = document.querySelector('.title span');
      title.innerText = '(X-O)';
      document.querySelector('.title').style.backgroundColor = 'white';
      document.querySelector('.title').style.color = 'black';
      turn = 'x';
    }

    updateScoreboard();
  </script>
</body>
</html>
