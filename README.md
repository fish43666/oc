<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>OC生成器 - 五维雷达图</title>
  <style>
    body {
      font-family: sans-serif;
      background: #f4f4f4;
      padding: 2em;
      transition: background-color 0.5s ease;
    }
    .container {
      background: white;
      padding: 2em;
      border-radius: 1em;
      max-width: 900px;
      margin: auto;
    }
    input, select, textarea {
      width: 100%;
      padding: 0.5em;
      margin-bottom: 1em;
      border-radius: 0.5em;
      border: 1px solid #ccc;
    }
    button {
      padding: 0.7em 1.5em;
      border: none;
      border-radius: 0.5em;
      background: #007bff;
      color: white;
      cursor: pointer;
    }
    #output {
      white-space: pre-wrap;
      word-wrap: break-word;
      background: #222;
      color: #eee;
      padding: 1em;
      border-radius: 0.5em;
      margin-top: 1em;
      font-family: monospace;
    }
    canvas {
      display: block;
      margin: 1em auto;
      background: #fff;
      border-radius: 1em;
      max-width: 100%;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>OC生成器（带五维雷达图）</h2>
    <label>角色名字：<input type="text" id="name" placeholder="xzgh" /></label>
    <label>性别：
      <select id="gender">
        <option>男凉</option>
        <option>男</option>
        <option>女</option>
        <option>不明</option>
        <option>自定义</option>
      </select>
    </label>
    <label>年龄（岁）：<input type="number" id="age" min="1" max="999" /></label>
    <label>身高（cm）：<input type="number" id="height" /></label>
    <label>发色：<input type="text" id="hair" /></label>
    <label>瞳色（支持异色）：<input type="text" id="eyes" /></label>
    <label>外貌特征：<input type="text" id="features" /></label>
    <label>穿着风格与配饰：<input type="text" id="clothes" /></label>
    <label>阵营/所属团体：<input type="text" id="camp" /></label>
    <label>主要武器：<input type="text" id="weapon" /></label>
    <label>脉原属性类型：<input type="text" id="source" /></label>
    <label>脉原等级（0~6 或特殊状态）：<input type="text" id="sourceLevel" /></label>
    <label>脉原技能简述：<textarea id="sourceSkill" rows="3"></textarea></label>
    <label>特殊设定与背景故事：<textarea id="special" rows="3"></textarea></label>

    <h3>🧭 五维属性评分（0~100）</h3>
    <label>力量 Strength：<input type="number" id="str" min="0" max="100" /></label>
    <label>敏捷 Agility：<input type="number" id="agi" min="0" max="100" /></label>
    <label>智力 Intelligence：<input type="number" id="int" min="0" max="100" /></label>
    <label>感知 Perception：<input type="number" id="per" min="0" max="100" /></label>
    <label>魅力 Charisma：<input type="number" id="cha" min="0" max="100" /></label>

    <h3>⚔️ 科系选择</h3>
    <select id="kinds">
      <option>器术科</option>
      <option>幻术科</option>
      <option>卷轴科</option>
      <option>体术科</option>
    </select>

    <h3>💉 当前状态</h3>
    <select id="status">
      <option>健康</option>
      <option>轻伤</option>
      <option>受伤</option>
      <option>濒危</option>
      <option>死亡</option>
      <option>赛博飞升</option>
    </select>

    <button onclick="generateOC()">生成角色描述 + 五维雷达图</button>

    <h3>📝 自动生成的角色描述：</h3>
    <pre id="output"></pre>

    <canvas id="radar" width="400" height="400"></canvas>
  </div>

  <script>
    function generateOC() {
      const name = document.getElementById('name').value.trim();
      const gender = document.getElementById('gender').value;
      const age = document.getElementById('age').value;
      const height = parseInt(document.getElementById('height').value) || 0;
      const hair = document.getElementById('hair').value.trim();
      const eyes = document.getElementById('eyes').value.trim();
      const features = document.getElementById('features').value.trim();
      const clothes = document.getElementById('clothes').value.trim();
      const camp = document.getElementById('camp').value.trim();
      const weapon = document.getElementById('weapon').value.trim();
      const source = document.getElementById('source').value.trim();
      const sourceLevel = document.getElementById('sourceLevel').value.trim();
      const sourceSkill = document.getElementById('sourceSkill').value.trim();
      const special = document.getElementById('special').value.trim();
      const kind = document.getElementById('kinds').value;
      const status = document.getElementById('status').value;

      // 瞳色彩蛋：红色（深红）
      if (eyes.includes('红')) {
        document.body.style.backgroundColor = '#8B0000'; // 深红色
      } else {
        document.body.style.backgroundColor = '#f4f4f4';
      }

      // 限制名字不能输入“棍木”“棍亩”
      if (name === '棍木' || name === '棍亩') {
        alert('该名字禁止输入！');
        return;
      }

      // 五维属性读取
      const str = Math.min(Math.max(parseInt(document.getElementById('str').value), 0), 100) || 0;
      const agi = Math.min(Math.max(parseInt(document.getElementById('agi').value), 0), 100) || 0;
      const intt = Math.min(Math.max(parseInt(document.getElementById('int').value), 0), 100) || 0;
      const per = Math.min(Math.max(parseInt(document.getElementById('per').value), 0), 100) || 0;
      const cha = Math.min(Math.max(parseInt(document.getElementById('cha').value), 0), 100) || 0;

      // 五维满分彩蛋
      let extramsg = '';
      if (str === 100 && agi === 100 && intt === 100 && per === 100 && cha === 100) {
        extramsg = '我不当人啦，jojo！！！';
      }

      // 名字彩蛋
      if (name.toLowerCase() === 'mikasa') {
        alert('你在打什么主意？');
      } else if (name === '艾伦') {
        extramsg = '机油哒';
      } else if (name === 'xzgh') {
        extramsg = '你药干嘛？？？';
      }

      // 生成文字描述
      let text = `角色名字：${name}
性别：${gender}
年龄：${age} 岁
身高：${height} cm
发色：${hair}
瞳色：${eyes}
外貌特征：${features}
穿着风格与配饰：${clothes}
阵营/所属团体：${camp}
主要武器：${weapon}
脉原属性类型：${source}
脉原等级：${sourceLevel}
脉原技能简述：${sourceSkill}
特殊设定与背景故事：${special}
科系选择：${kind}
当前状态：${status}

${extramsg}
`;

      if (height > 500) {
        text += "\n那一天人类又想起了被巨人支配的恐惧。";
      }

      // 输出文字
      document.getElementById('output').textContent = text;

      // 画雷达图
      drawRadarChart([str, agi, intt, per, cha]);
    }

    function drawRadarChart(data) {
      const canvas = document.getElementById('radar');
      const ctx = canvas.getContext('2d');
      const width = canvas.width;
      const height = canvas.height;
      const centerX = width / 2;
      const centerY = height / 2;
      const radius = Math.min(centerX, centerY) * 0.8;

      ctx.clearRect(0, 0, width, height);

      const labels = ['力量', '敏捷', '智力', '感知', '魅力'];
      const angles = [];
      const points = [];

      // 计算每个点的角度
      for(let i=0; i<5; i++) {
        angles[i] = (Math.PI * 2 / 5) * i - Math.PI / 2; // 从上方开始
      }

      // 画蛛网网格（5层）
      ctx.strokeStyle = '#aaa';
      ctx.lineWidth = 1;
      for(let layer = 1; layer <= 5; layer++) {
        ctx.beginPath();
        for(let i=0; i<5; i++) {
          let r = radius * (layer/5);
          let x = centerX + r * Math.cos(angles[i]);
          let y = centerY + r * Math.sin(angles[i]);
          if (i === 0) ctx.moveTo(x,y);
          else ctx.lineTo(x,y);
        }
        ctx.closePath();
        ctx.stroke();
      }

      // 画径向线和标签
      ctx.fillStyle = '#444';
      ctx.font = '14px sans-serif';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      for(let i=0; i<5; i++) {
        // 径向线
        ctx.beginPath();
        ctx.moveTo(centerX, centerY);
        ctx.lineTo(centerX + radius * Math.cos(angles[i]), centerY + radius * Math.sin(angles[i]));
        ctx.stroke();

        // 标签
        let labelX = centerX + (radius + 20) * Math.cos(angles[i]);
        let labelY = centerY + (radius + 20) * Math.sin(angles[i]);
        ctx.fillText(labels[i], labelX, labelY);
      }

      // 计算属性点坐标，属性值映射到半径（最大100）
      for(let i=0; i<5; i++) {
        let r = radius * (data[i]/100);
        let x = centerX + r * Math.cos(angles[i]);
        let y = centerY + r * Math.sin(angles[i]);
        points[i] = {x, y};
      }

      // 画属性多边形
      ctx.beginPath();
      ctx.fillStyle = 'rgba(0, 123, 255, 0.5)';
      ctx.strokeStyle = '#007bff';
      ctx.lineWidth = 2;
      for(let i=0; i<5; i++) {
        if(i===0) ctx.moveTo(points[i].x, points[i].y);
        else ctx.lineTo(points[i].x, points[i].y);
      }
      ctx.closePath();
      ctx.fill();
      ctx.stroke();

      // 画每个点的圆圈
      ctx.fillStyle = '#007bff';
      for(let i=0; i<5; i++) {
        ctx.beginPath();
        ctx.arc(points[i].x, points[i].y, 5, 0, Math.PI * 2);
        ctx.fill();
      }
    }
  </script>
</body>
</html>
