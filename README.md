<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Fun Mini Games & QR Code (Image)</title>
<style>
  body {
    font-family: Arial, sans-serif;
    max-width: 600px;
    margin: auto;
    padding: 10px;
    text-align: center;
  }
  h1 {
    margin-bottom: 5px;
  }
  #qr img {
    margin: 20px auto;
    width: 150px;
    height: 150px;
  }
  .game-section {
    margin: 30px 0;
    border-top: 1px solid #ccc;
    padding-top: 20px;
  }
  button {
    padding: 10px 15px;
    font-size: 16px;
    cursor: pointer;
  }
  #circle-game {
    position: relative;
    width: 300px;
    height: 200px;
    margin: 15px auto;
    border: 1px solid #ddd;
  }
  #circle {
    width: 40px;
    height: 40px;
    background: #4CAF50;
    border-radius: 50%;
    position: absolute;
    cursor: pointer;
  }
  #memory-board {
    max-width: 320px;
    margin: 10px auto;
    display: grid;
    grid-template-columns: repeat(4, 70px);
    grid-gap: 10px;
  }
  .card {
    width: 70px;
    height: 70px;
    background-color: #2196F3;
    color: white;
    font-size: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    user-select: none;
    border-radius: 8px;
  }
  .flipped {
    background-color: #f44336;
  }
  .matched {
    background-color: #4CAF50;
    cursor: default;
  }
  #guess-result {
    margin-top: 10px;
    font-weight: bold;
  }
</style>
</head>
<body>

<h1>Welcome to Fun Mini Games!</h1>

<div>
  <p>Scan this QR code to open this website on your phone:</p>
  <div id="qr"></div>
</div>

<div class="game-section" id="circle-game-section">
  <h2>1. Click the Circle</h2>
  <p>Click the green circle as many times as you can in 15 seconds!</p>
  <div id="circle-game">
    <div id="circle"></div>
  </div>
  <p>Score: <span id="circle-score">0</span></p>
  <button id="circle-start">Start Game</button>
  <p id="circle-timer"></p>
</div>

<div class="game-section" id="guess-game-section">
  <h2>2. Guess the Number</h2>
  <p>Guess a number between 1 and 100</p>
  <input type="number" id="guess-input" min="1" max="100" />
  <button id="guess-btn">Guess</button>
  <p id="guess-result"></p>
  <button id="guess-reset" style="display:none;">Play Again</button>
</div>

<div class="game-section" id="memory-game-section">
  <h2>3. Memory Cards</h2>
  <p>Match all pairs!</p>
  <div id="memory-board"></div>
  <button id="memory-reset">Restart Game</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/qrcode/build/qrcode.min.js"></script>
<script>
  // Generate QR code as an image inside #qr div
  const qrContainer = document.getElementById('qr');
  QRCode.toDataURL(window.location.href, { width: 150 })
    .then(url => {
      const img = document.createElement('img');
      img.src = url;
      img.alt = 'QR code';
      qrContainer.appendChild(img);
    })
    .catch(err => console.error(err));

  // --- Click the Circle Game ---
  const circle = document.getElementById('circle');
  const circleGame = document.getElementById('circle-game');
  const circleScoreEl = document.getElementById('circle-score');
  const circleTimerEl = document.getElementById('circle-timer');
  const circleStartBtn = document.getElementById('circle-start');
  let circleScore = 0;
  let circleTimeLeft = 15;
  let circleTimerId = null;
  let circleGameActive = false;

  function moveCircle() {
    const maxX = circleGame.clientWidth - circle.clientWidth;
    const maxY = circleGame.clientHeight - circle.clientHeight;
    const x = Math.random() * maxX;
    const y = Math.random() * maxY;
    circle.style.left = x + 'px';
    circle.style.top = y + 'px';
  }

  function circleGameTick() {
    circleTimeLeft--;
    circleTimerEl.textContent = `Time left: ${circleTimeLeft}s`;
    if (circleTimeLeft <= 0) {
      clearInterval(circleTimerId);
      circleTimerEl.textContent = 'Game Over!';
      circleGameActive = false;
      circle.style.display = 'none';
      circleStartBtn.disabled = false;
    }
  }

  circle.addEventListener('click', () => {
    if (!circleGameActive) return;
    circleScore++;
    circleScoreEl.textContent = circleScore;
    moveCircle();
  });

  circleStartBtn.addEventListener('click', () => {
    circleScore = 0;
    circleScoreEl.textContent = circleScore;
    circleTimeLeft = 15;
    circleTimerEl.textContent = `Time left: ${circleTimeLeft}s`;
    circleGameActive = true;
    circle.style.display = 'block';
    moveCircle();
    circleStartBtn.disabled = true;
    circleTimerId = setInterval(circleGameTick, 1000);
  });

  // --- Guess the Number Game ---
  const guessInput = document.getElementById('guess-input');
  const guessBtn = document.getElementById('guess-btn');
  const guessResult = document.getElementById('guess-result');
  const guessReset = document.getElementById('guess-reset');
  let secretNumber;
  let guessesLeft;

  function startGuessGame() {
    secretNumber = Math.floor(Math.random() * 100) + 1;
    guessesLeft = 7;
    guessResult.textContent = '';
    guessInput.value = '';
    guessInput.disabled = false;
    guessBtn.style.display = 'inline-block';
    guessReset.style.display = 'none';
  }

  guessBtn.addEventListener('click', () => {
    const guess = Number(guessInput.value);
    if (!guess || guess < 1 || guess > 100) {
      guessResult.textContent = 'Please enter a valid number between 1 and 100.';
      return;
    }
    guessesLeft--;
    if (guess === secretNumber) {
      guessResult.textContent = `🎉 Correct! The number was ${secretNumber}. You won!`;
      guessInput.disabled = true;
      guessBtn.style.display = 'none';
      guessReset.style.display = 'inline-block';
    } else if (guessesLeft <= 0) {
      guessResult.textContent = `😞 Game over! The number was ${secretNumber}.`;
      guessInput.disabled = true;
      guessBtn.style.display = 'none';
      guessReset.style.display = 'inline-block';
    } else if (guess < secretNumber) {
      guessResult.textContent = `Try higher! You have ${guessesLeft} guesses left.`;
    } else {
      guessResult.textContent = `Try lower! You have ${guessesLeft} guesses left.`;
    }
  });

  guessReset.addEventListener('click', startGuessGame);

  startGuessGame();

  // --- Memory Cards Game ---
  const memoryBoard = document.getElementById('memory-board');
  const memoryReset = document.getElementById('memory-reset');
  const icons = ['🍎','🍌','🍇','🍉','🍓','🥝','🍒','🍍'];
  let cards = [];
  let flippedCards = [];
  let matchedCount = 0;

  function shuffle(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
  }

  function createMemoryCards() {
    memoryBoard.innerHTML = '';
    cards = [];
    matchedCount = 0;
    flippedCards = [];
    const pairs = icons.concat(icons); // duplicate icons for pairs
    shuffle(pairs);

    pairs.forEach((icon, i) => {
      const card = document.createElement('div');
      card.classList.add('card');
      card.dataset.icon = icon;
      card.textContent = '';
      card.addEventListener('click', onCardClick);
      memoryBoard.appendChild(card);
      cards.push(card);
    });
  }

  function onCardClick(e) {
    const card = e.target;
    if (flippedCards.length === 2 || card.classList.contains('flipped') || card.classList.contains('matched')) return;

    card.classList.add('flipped');
    card.textContent = card.dataset.icon;
    flippedCards.push(card);

    if (flippedCards.length === 2) {
      if (flippedCards[0].dataset.icon === flippedCards[1].dataset.icon) {
        flippedCards.forEach(c => {
          c.classList.add('matched');
        });
        matchedCount += 2;
        flippedCards = [];
        if (matchedCount === cards.length) {
          setTimeout(() => alert('🎉 You matched all pairs!'), 100);
        }
      } else {
        setTimeout(() => {
          flippedCards.forEach(c => {
            c.classList.remove('flipped');
            c.textContent = '';
          });
          flippedCards = [];
        }, 1000);
      }
    }
  }

  memoryReset.addEventListener('click', createMemoryCards);

  createMemoryCards();
</script>

</body>
</html>

