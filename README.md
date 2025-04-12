<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <title>DefCoin Гра</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #ddd;
      color: #333;
      text-align: center;
      margin: 0;
      padding: 20px;
    }
    h1 {
      margin-bottom: 10px;
    }
    #clickButton {
      margin: 20px auto;
      width: 150px;
      height: 150px;
      background-color: gold;
      border: none;
      border-radius: 50%;
      font-size: 24px;
      color: #333;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0,0,0,0.2);
      transition: background-color 0.3s, transform 0.1s;
    }
    #clickButton:hover {
      background-color: orange;
    }
    #clickButton:active {
      transform: scale(0.95);
    }
    button {
      margin: 5px;
      padding: 10px 20px;
      font-size: 16px;
      border: none;
      border-radius: 5px;
      transition: background-color 0.3s;
    }
    button.buyable:hover {
      background-color: #007bff;
      color: white;
    }
    button.green {
      background-color: #4CAF50;
      color: white;
    }
    button.red {
      background-color: #f44336;
      color: white;
    }
    button.gray {
      background-color: gray;
      color: white;
      cursor: default;
    }
    .inventory {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
    }
    .inventory-item {
      margin: 10px;
      padding: 10px;
      border-radius: 10px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.2);
      text-align: center;
      width: 140px;
      color: white;
    }
    .inventory img {
      width: 80px;
      transition: transform 0.3s;
    }
    .inventory img.spin {
      animation: spin 1s linear;
    }
    @keyframes spin {
      0% {transform: rotate(0deg);}
      100% {transform: rotate(360deg);}
    }
    .wheel {
      margin-top: 30px;
      border-top: 2px solid #999;
      padding-top: 20px;
    }
  </style>
</head>
<body>

<h1>DefCoin: <span id="defcoin">0</span></h1>
<button id="clickButton">Клік!</button><br>
<button id="autoFarmButton">Купити автозбір (400 DefCoin)</button>
<button id="multiplierButton">Купити множник (200 DefCoin)</button>

<div class="wheel">
  <h2>Звичайне колесо фортуни (800 DefCoin)</h2>
  <button id="spinWheel">Крутити</button>
  <div>Шанси: Хмарка — 65%, Пельмень — 25%, Артем — 9.9%, Дем’ян — 9.9%, Роман — 0.1%</div>
</div>

<div class="wheel">
  <h2>Преміум колесо фортуни (4000 DefCoin)</h2>
  <button id="spinPremium">Крутити</button>
  <div>Шанси: Артем — 50%, Дем’ян — 50%</div>
</div>

<h3>Отримані персонажі</h3>
<div id="inventory" class="inventory"></div>
<h3>Загальна сила: <span id="totalPower">0</span></h3>

<script>
const characters = {
  "Хмарка_Папугай": { power: 10, image: "", color: "#555" },
  "PhelmencikPon21K": { power: 100, image: "", color: "#2196F3" },
  "ArtemMNO": { power: 1000, image: "", color: "#d32f2f" },
  "Дем’ян Романюк": { power: 1000, image: "", color: "#d32f2f" },
  "RomanDefUA": { power: 100000, image: "", color: "#FFD700" }
};

let defcoin = 0;
let multiplier = 1;
let autoFarm = false;
let inventory = {};
let totalPower = 0;

function updateDisplay() {
  document.getElementById("defcoin").textContent = Math.floor(defcoin);
  document.getElementById("totalPower").textContent = totalPower;
  updateButtons();
}

function updateButtons() {
  document.getElementById("autoFarmButton").className = defcoin >= 400 && !autoFarm ? "green buyable" : (autoFarm ? "gray" : "red");
  document.getElementById("multiplierButton").className = defcoin >= 200 ? "green buyable" : "red";
  document.getElementById("spinWheel").className = defcoin >= 800 ? "green buyable" : "red";
  document.getElementById("spinPremium").className = defcoin >= 4000 ? "green buyable" : "red";
}

function addCharacter(name) {
  if (inventory[name]) return;
  inventory[name] = { level: 1 };
  const container = document.createElement("div");
  container.className = "inventory-item";
  container.style.backgroundColor = characters[name].color;

  const img = document.createElement("img");
  img.src = characters[name].image;
  img.alt = name;
  img.title = name;
  img.classList.add("spin");

  const nameText = document.createElement("div");
  nameText.textContent = name;

  const levelText = document.createElement("div");
  levelText.textContent = `Рівень: 1`;

  const upgradeBtn = document.createElement("button");
  upgradeBtn.textContent = `Прокачати (80)`;
  upgradeBtn.className = "green";

  upgradeBtn.addEventListener("click", () => {
    const currentLevel = inventory[name].level;
    const cost = 80 * currentLevel;
    if (defcoin >= cost && currentLevel < 100) {
      defcoin -= cost;
      inventory[name].level++;
      totalPower += characters[name].power;
      levelText.textContent = `Рівень: ${inventory[name].level}`;
      upgradeBtn.textContent = inventory[name].level < 100 ? `Прокачати (${80 * inventory[name].level})` : "Макс";
      updateDisplay();
    }
  });

  container.appendChild(img);
  container.appendChild(nameText);
  container.appendChild(levelText);
  container.appendChild(upgradeBtn);
  document.getElementById("inventory").appendChild(container);

  totalPower += characters[name].power;
  updateDisplay();
}

function gainPassiveCoins() {
  for (const [name, data] of Object.entries(inventory)) {
    defcoin += characters[name].power * data.level * multiplier * 0.1;
  }
  updateDisplay();
}

setInterval(() => {
  if (autoFarm) gainPassiveCoins();
}, 1000);

document.getElementById("clickButton").addEventListener("click", () => {
  defcoin += 1 * multiplier;
  updateDisplay();
});

document.getElementById("autoFarmButton").addEventListener("click", () => {
  if (defcoin >= 400 && !autoFarm) {
    defcoin -= 400;
    autoFarm = true;
    updateDisplay();
  }
});

document.getElementById("multiplierButton").addEventListener("click", () => {
  if (defcoin >= 200) {
    defcoin -= 200;
    multiplier++;
    updateDisplay();
  }
});

document.getElementById("spinWheel").addEventListener("click", () => {
  if (defcoin < 800) return;
  defcoin -= 800;
  const roll = Math.random() * 100;
  if (roll < 65) addCharacter("Хмарка_Папугай");
  else if (roll < 90) addCharacter("PhelmencikPon21K");
  else if (roll < 99.9) addCharacter("ArtemMNO");
  else if (roll < 99.9 + 9.9) addCharacter("Дем’ян Романюк");
  else addCharacter("RomanDefUA");
  updateDisplay();
});

document.getElementById("spinPremium").addEventListener("click", () => {
  if (defcoin < 4000) return;
  defcoin -= 4000;
  const roll = Math.random() * 100;
  if (roll < 50) addCharacter("ArtemMNO");
  else addCharacter("Дем’ян Романюк");
  updateDisplay();
});

updateDisplay();
</script>
</body>
</html>
