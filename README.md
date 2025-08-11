<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>🎡 马卡龙转盘</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      font-family: "Comic Sans MS", cursive;
      text-align: center;
      background-color: #fffafc;
      padding: 20px;
    }
    h1 {
      color: #ff80ab;
    }
    #wheel-container {
      position: relative;
      width: 300px;
      height: 300px;
      margin: 20px auto;
    }
    canvas {
      border-radius: 50%;
      background-color: #fff0f6;
    }
    .pointer {
      position: absolute;
      top: -10px;
      left: 50%;
      transform: translateX(-50%);
      width: 0;
      height: 0;
      border-left: 15px solid transparent;
      border-right: 15px solid transparent;
      border-bottom: 30px solid #ff80ab;
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
  </style>
</head>
<body>
  <h1>🎡 马卡龙转盘</h1>
  <div id="wheel-container">
    <div class="pointer"></div>
    <canvas id="wheel" width="300" height="300"></canvas>
  </div>

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

  <!-- 音效资源 -->
  <audio id="spinSound" src="spin.mp3" preload="auto"></audio>
  <audio id="dingSound" src="ding.mp3" preload="auto"></audio>

  <script>
    const canvas = document.getElementById("wheel");
    const ctx = canvas.getContext("2d");
    const radius = canvas.width / 2;
    let items = ["模块 1", "模块 2", "模块 3", "模块 4", "模块 5", "模块 6"];

    // 马卡龙色系
    const colors = ["#B2F2BB", "#FFC9DE", "#FFF3BF", "#A5D8FF", "#D0BFFF", "#FFD8A8"];
    let angleOffset = 0;
    let spinning = false;

    const spinSound = document.getElementById("spinSound");
    const dingSound = document.getElementById("dingSound");

    function drawWheel() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      for (let i = 0; i < 6; i++) {
        const angle = (i * Math.PI * 2) / 6 + angleOffset;
        ctx.beginPath();
        ctx.moveTo(radius, radius);
        ctx.arc(radius, radius, radius, angle, angle + Math.PI * 2 / 6);
        ctx.fillStyle = colors[i];
        ctx.fill();
        ctx.strokeStyle = "rgba(150,150,150,0.3)";
        ctx.lineWidth = 2;
        ctx.stroke();

        ctx.save();
        ctx.translate(radius, radius);
        ctx.rotate(angle + Math.PI / 6);
        ctx.textAlign = "center";
        ctx.fillStyle = "#444";
        ctx.font = "bold 18px 'Comic Sans MS', sans-serif";
        ctx.fillText(items[i], radius / 2, 0);
        ctx.restore();
      }
    }

    drawWheel();

    function spin() {
      if (spinning) return;
      items = [];
      for (let i = 0; i < 6; i++) {
        const val = document.getElementById("item" + i).value || `模块 ${i + 1}`;
        items.push(val);
      }

      let angle = 0;
      let speed = Math.random() * 0.2 + 0.3;
      let deceleration = 0.005;
      spinning = true;

      spinSound.currentTime = 0;
      spinSound.play();

      const interval = setInterval(() => {
        angle += speed;
        speed -= deceleration;
        angleOffset = angle;
        drawWheel();

        if (speed <= 0) {
          clearInterval(interval);
          spinning = false;
          spinSound.pause();
          dingSound.currentTime = 0;
          dingSound.play();
          const selectedIndex = getSelectedIndex(angleOffset);
          document.getElementById("result").textContent = `🎉 结果：${items[selectedIndex]}`;
        }
      }, 20);
    }

    function getSelectedIndex(offset) {
      const normalized = offset % (Math.PI * 2);
      const sectorAngle = (Math.PI * 2) / 6;
      const index = Math.floor((Math.PI * 1.5 - normalized + sectorAngle / 2) / sectorAngle) % 6;
      return (index + 6) % 6;
    }
  </script>
</body>
</html>
