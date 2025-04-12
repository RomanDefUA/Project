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
      background-color: #fff;
      border-radius: 10px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.2);
      text-align: center;
      width: 120px;
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

<button id="clickButton">Клік!</button>
<br>
<button id="autoFarmButton">Купити автозбір (80 DefCoin)</button>
<button id="multiplierButton">Купити множник (40 DefCoin)</button>

<div class="wheel">
  <h2>Звичайне колесо фортуни (100 DefCoin)</h2>
  <button id="spinWheel">Крутити</button>
  <div>Шанси: Хмарка — 65%, Пельмень — 25%, Артем — 9.9%, Дем’ян — 9.9%, Роман — 0.1%</div>
</div>

<div class="wheel">
  <h2>Преміум колесо фортуни (1000 DefCoin)</h2>
  <button id="spinPremium">Крутити</button>
  <div>Шанси: Артем — 75%, Дем’ян — 75%, Роман — 25%</div>
</div>

<h3>Отримані персонажі</h3>
<div id="inventory" class="inventory"></div>

<h3>Загальна сила: <span id="totalPower">0</span></h3>

<script>
  const characters = {
    "Хмарка_Папугай": { power: 10, image: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..." },
    "PhelmencikPon21K": { power: 100, image: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..." },
    "ArtemMNO": { power: 1000, image: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..." },
    "Дем’ян Романюк": { power: 1000, image: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..." },
    "RomanDefUA": { power: 100000, image: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..." }
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
    const autoBtn = document.getElementById("autoFarmButton");
    autoBtn.className = defcoin >= 80 ? "green buyable" : "red";
    if (autoFarm) {
      autoBtn.textContent = "Куплено";
      autoBtn.className = "gray";
    }

    const multBtn = document.getElementById("multiplierButton");
    multBtn.className = defcoin >= 40 ? "green buyable" : "red";

ZENYS|RomanDefUA_YT, [12.04.2025 10:09]
document.getElementById("spinWheel").className = defcoin >= 100 ? "green buyable" : "red";
    document.getElementById("spinPremium").className = defcoin >= 1000 ? "green buyable" : "red";
  }

  function addCharacter(name) {
    if (inventory[name]) return;
    inventory[name] = { level: 1 };

    const container = document.createElement("div");
    container.className = "inventory-item";

    const img = document.createElement("img");
    img.src = characters[name].image;
    img.alt = name;
    img.title = name;
    img.classList.add("spin");
    setTimeout(() => img.classList.remove("spin"), 1000);

    const levelText = document.createElement("div");
    levelText.textContent = Рівень: 1;

    const upgradeBtn = document.createElement("button");
    upgradeBtn.textContent = Прокачати (10);
    upgradeBtn.className = "green";

    upgradeBtn.addEventListener("click", () => {
      const currentLevel = inventory[name].level;
      if (currentLevel >= 100) return;

      const cost = 10 * currentLevel;
      if (defcoin >= cost) {
        defcoin -= cost;
        inventory[name].level++;
        totalPower += characters[name].power;
        levelText.textContent = Рівень: ${inventory[name].level};
        upgradeBtn.textContent = inventory[name].level < 100 ?
          Прокачати (${10 * inventory[name].level}) : "Макс";
        updateDisplay();
      }
    });

    container.appendChild(img);
    container.appendChild(levelText);
    container.appendChild(upgradeBtn);

    document.getElementById("inventory").appendChild(container);
    totalPower += characters[name].power;
    updateDisplay();
  }

  function gainPassiveCoins() {
    for (const [name, data] of Object.entries(inventory)) {
      const level = data.level;
      const power = characters[name].power;
      defcoin += power * level * multiplier;
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
    if (defcoin >= 80 && !autoFarm) {
      defcoin -= 80;
      autoFarm = true;
      updateDisplay();
    }
  });

  document.getElementById("multiplierButton").addEventListener("click", () => {
    if (defcoin >= 40) {
      defcoin -= 40;
      multiplier++;
      updateDisplay();
    }
  });

  document.getElementById("spinWheel").addEventListener("click", () => {
    if (defcoin < 100) return;
    defcoin -= 100;
    const roll = Math.random() * 100;
    if (roll < 65) addCharacter("Хмарка_Папугай");
    else if (roll < 90) addCharacter("PhelmencikPon21K");
    else if (roll < 99.9) addCharacter("ArtemMNO");
    else if (roll < 100) addCharacter("Дем’ян Романюк");
    else addCharacter("RomanDefUA");
    updateDisplay();
  });

  document.getElementById("spinPremium").addEventListener("click", () => {
    if (defcoin < 1000) return;
    defcoin -= 1000;
    const roll = Math.random() * 100;
    if (roll < 50) addCharacter("ArtemMNO");
    else if (roll < 75) addCharacter("Дем’ян Романюк");
    else addCharacter("RomanDefUA");
    updateDisplay();
  });

  updateDisplay();
</script>

</body>
</html>
