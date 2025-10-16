<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <title>بازی کلیکر سکه پرهام</title>
  <style>
    body {
      font-family: 'Vazir', sans-serif;
      background-color: #1e1e1e;
      color: #fff;
      text-align: center;
      padding-top: 50px;
      transition: background-color 0.5s ease;
    }
    .light-mode {
      background-color: #f5f5f5;
      color: #000;
    }
    #coin, #rareCoin, #boss {
      width: 150px;
      height: 150px;
      background-size: cover;
      margin: 20px auto;
      cursor: pointer;
      transition: transform 0.3s ease;
    }
    #coin:active, #rareCoin:active, #boss:active {
      transform: scale(0.95) rotate(10deg);
    }
    .stats {
      margin-top: 20px;
      font-size: 1.2em;
    }
    button {
      margin: 10px;
      padding: 10px 20px;
      font-size: 1em;
      cursor: pointer;
      background-color: #ffd700;
      border: none;
      border-radius: 5px;
      color: #000;
    }
    button:hover {
      background-color: #ffec8b;
    }
    #message {
      margin-top: 15px;
      font-style: italic;
      font-size: 1em;
    }
  </style>
</head>
<body>

  <h1>💰 بازی کلیکر سکه پرهام</h1>
  <div id="usernameSection">
    <input type="text" id="usernameInput" placeholder="نام کاربری‌ات رو وارد کن">
    <button onclick="setUsername()">شروع بازی</button>
  </div>

  <div id="gameSection" style="display:none;">
    <div class="stats">سلام <span id="usernameDisplay"></span>!</div>
    <div id="coin" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/4/4e/Gold_coin_icon.png');"></div>
    <div id="rareCoin" style="display:none; background-image:url('https://upload.wikimedia.org/wikipedia/commons/2/2e/Silver_coin_icon.png');"></div>
    <div id="boss" style="display:none; background-image:url('https://upload.wikimedia.org/wikipedia/commons/5/5e/Monster_icon.png');"></div>
    <div class="stats">سکه‌ها: <span id="coins">0</span></div>
    <div class="stats">قدرت کلیک: <span id="clickPower">1</span></div>
    <button onclick="upgradeClick()">ارتقای کلیک (هزینه: <span id="clickCost">10</span>)</button>
    <button onclick="buyRobot()">خرید ربات خودکار (هزینه: <span id="robotCost">100</span>)</button>
    <div class="stats">ربات‌ها: <span id="robots">0</span></div>
    <button onclick="toggleTheme()">تغییر حالت شب/روز</button>
    <button onclick="resetGame()">ریست کامل بازی</button>
    <div id="message"></div>
  </div>

  <script>
    let coins = 0, clickPower = 1, clickCost = 10, robotCost = 100, robots = 0;
    let username = "", isLightMode = false;
    let bossHP = 20, bossActive = false;

    const coinDisplay = document.getElementById('coins');
    const clickPowerDisplay = document.getElementById('clickPower');
    const clickCostDisplay = document.getElementById('clickCost');
    const robotCostDisplay = document.getElementById('robotCost');
    const robotDisplay = document.getElementById('robots');
    const usernameDisplay = document.getElementById('usernameDisplay');
    const messageDisplay = document.getElementById('message');
    const coinEl = document.getElementById('coin');
    const rareCoinEl = document.getElementById('rareCoin');
    const bossEl = document.getElementById('boss');

    function setUsername() {
      const input = document.getElementById('usernameInput').value;
      if (input.trim() !== "") {
        username = input;
        localStorage.setItem('username', username);
        document.getElementById('usernameSection').style.display = 'none';
        document.getElementById('gameSection').style.display = 'block';
        usernameDisplay.textContent = username;
        loadGame();
      }
    }

    function loadGame() {
      coins = parseInt(localStorage.getItem('coins')) || 0;
      clickPower = parseInt(localStorage.getItem('clickPower')) || 1;
      clickCost = parseInt(localStorage.getItem('clickCost')) || 10;
      robotCost = parseInt(localStorage.getItem('robotCost')) || 100;
      robots = parseInt(localStorage.getItem('robots')) || 0;
      username = localStorage.getItem('username') || "";
      isLightMode = localStorage.getItem('lightMode') === "true";
      if (isLightMode) document.body.classList.add('light-mode');
      usernameDisplay.textContent = username;
      updateDisplay();
    }

    function saveGame() {
      localStorage.setItem('coins', coins);
      localStorage.setItem('clickPower', clickPower);
      localStorage.setItem('clickCost', clickCost);
      localStorage.setItem('robotCost', robotCost);
      localStorage.setItem('robots', robots);
      localStorage.setItem('lightMode', isLightMode);
    }

    function updateDisplay() {
      coinDisplay.textContent = coins;
      clickPowerDisplay.textContent = clickPower;
      clickCostDisplay.textContent = clickCost;
      robotCostDisplay.textContent = robotCost;
      robotDisplay.textContent = robots;
    }

    coinEl.addEventListener('click', () => {
      coins += clickPower;
      updateDisplay();
      saveGame();
    });

    function upgradeClick() {
      if (coins >= clickCost) {
        coins -= clickCost;
        clickPower += 1;
        clickCost = Math.floor(clickCost * 1.5);
        updateDisplay();
        saveGame();
      }
    }

    function buyRobot() {
      if (coins >= robotCost) {
        coins -= robotCost;
        robots += 1;
        robotCost = Math.floor(robotCost * 2);
        updateDisplay();
        saveGame();
      }
    }

    setInterval(() => {
      coins += robots * 2;
      updateDisplay();
      saveGame();
    }, 2000);

    function toggleTheme() {
      isLightMode = !isLightMode;
      document.body.classList.toggle('light-mode');
      saveGame();
    }

    function resetGame() {
      localStorage.clear();
      location.reload();
    }

    // سکه نادر
    setInterval(() => {
      if (Math.random() < 0.3) {
        rareCoinEl.style.display = 'block';
        setTimeout(() => rareCoinEl.style.display = 'none', 5000);
      }
    }, 25000);

    rareCoinEl.addEventListener('click', () => {
      coins += 50;
      rareCoinEl.style.display = 'none';
      messageDisplay.textContent = "✨ سکه‌ی نادر گرفتی! +50 سکه";
      updateDisplay();
      saveGame();
    });

    // باس‌فایت
    setInterval(() => {
      if (!bossActive) {
        bossActive = true;
        bossHP = 20;
        bossEl.style.display = 'block';
        messageDisplay.textContent = "🦹‍♂️ باس ظاهر شد! شکستش بده!";
      }
    }, 60000);

    bossEl.addEventListener('click', () => {
      bossHP -=
