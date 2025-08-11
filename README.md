<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>🎡 游戏转盘</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      font-family: "Comic Sans MS", cursive;
      text-align: center;
      background-color: #fffbe6;
      padding: 20px;
    }
    h1 {
      color: #ff69b4;
    }
    canvas {
      margin-top: 20px;
      border-radius: 50%;
      background-color: #ffe4e1;
    }
    .inputs {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 10px;
      margin-top: 20px;
    }
    input {
      padding: 8px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 16px;
      width: 100%;
    }
    button {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 18px;
      background-color: #ffb6c1;
      border: none;
      border-radius: 10px;
      color: white;
      cursor: pointer;
    }
    #result {
      margin-top: 20px;
      font-size: 20px;
      color: #d63384;
    }
    #history {
      margin-top: 30px;
      text-align: left;
    }
    .history-item {
      margin-bottom: 10px;
      padding: 10px;
      background-color: #fce4ec;
      border-radius: 8px;
    }
    .history-item button {
      margin-top: 5px;
      background-color: #ff69b4;
      color: white;
      border: none;
      padding: 5px 10px;
      border-radius: 6px;
      font-size: 14px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>🎡 游戏转盘</h1>
  <canvas id="wheel" width="300" height="300"></canvas>

  <div class="inputs">
    <input type="text" placeholder="模块 1" id="item0">
    <input type="text" placeholder="模块 2" id="item1">
    <input type="text" placeholder="模块 3" id="item2">
    <input type="text" placeholder="模块 4" id="item3">
    <input type="text" placeholder="模块 5" id="item4">
    <input type="text" placeholder="模块 6" id="item5">
  </div>

  <button onclick="spin()">🎲 转一下！</button>
  <div id="result"></div>

  <div id="history"></div>

  <script>
    const canvas = document.getElementById("wheel");
    const ctx = canvas.getContext("2d");
    const radius = canvas.width / 2;
    let items = ["模块 1", "模块 2", "模块 3", "模块 4", "模块 5", "模块 6"];
    const colors = ["#ff9999", "#ffcc99", "#ffff99", "#ccff99", "#99ffcc", "#99ccff"];

    function drawWheel() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      for (let i = 0; i < 6; i++) {
        const angle = (i * Math.PI * 2) / 6;
        ctx.beginPath();
        ctx.moveTo(radius, radius);
        ctx.arc(radius, radius, radius, angle, angle + Math.PI * 2 / 6);
        ctx.fillStyle = colors[i];
        ctx.fill();
        ctx.stroke();
        ctx.save();
        ctx.translate(radius, radius);
        ctx.rotate(angle + Math.PI / 6);
        ctx.textAlign = "center";
        ctx.fillStyle = "#333";
        ctx.font = "16px Comic Sans MS";
        ctx.fillText(items[i], radius / 2, 0);
        ctx.restore();
      }
    }

    drawWheel();

    function spin() {
      items = [];
      for (let i = 0; i < 6; i++) {
        const val = document.getElementById("item" + i).value || `模块 ${i + 1}`;
        items.push(val);
      }
      drawWheel();

      const selected = Math.floor(Math.random() * 6);
      document.getElementById("result").textContent = `🎉 结果：${items[selected]}`;

      saveToHistory(items);
      displayHistory();
    }

    function saveToHistory(data) {
      const history = JSON.parse(localStorage.getItem("wheelHistory") || "[]");
      const timestamp = new Date().toLocaleString();
      history.push({ timestamp, data });
      localStorage.setItem("wheelHistory", JSON.stringify(history));
    }

    function displayHistory() {
      const history = JSON.parse(localStorage.getItem("wheelHistory") || "[]");
      const container = document.getElementById("history");
      container.innerHTML = "<h2>📜 历史设置</h2>";
      history.reverse().forEach((entry, index) => {
        const div = document.createElement("div");
        div.className = "history-item";
        div.innerHTML = `
          <strong>${entry.timestamp}</strong><br>
          ${entry.data.map((d, i) => `模块 ${i + 1}: ${d}`).join("<br>")}
          <br><button onclick="restoreHistory(${history.length - 1 - index})">恢复设置</button>
        `;
        container.appendChild(div);
      });
    }

    function restoreHistory(index) {
      const history = JSON.parse(localStorage.getItem("wheelHistory") || "[]");
      const entry = history[index];
      entry.data.forEach((val, i) => {
        document.getElementById("item" + i).value = val;
      });
      items = entry.data;
      drawWheel();
      document.getElementById("result").textContent = `✅ 已恢复设置：${entry.timestamp}`;
    }

    // 初始化历史记录显示
    displayHistory();
  </script>
</body>
</html>
# luckywheel
