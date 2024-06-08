<template>
    
  <h1>Трехмерное случайное блуждание с графиком</h1>
  <div style="display: flex; padding: 10px">
    <div style="padding: 10px; width: 10%;">
      <label>Начальная точка X: <input type="number" id="startX" value="0"></label>
      <label>Начальная точка Y: <input type="number" id="startY" value="0"></label>
      <label>Начальная точка Z: <input type="number" id="startZ" value="0"></label>
      <label>Целевая точка X: <input type="number" id="targetX" value="5"></label>
      <label>Целевая точка Y: <input type="number" id="targetY" value="5"></label>
      <label>Целевая точка Z: <input type="number" id="targetZ" value="5"></label>
      <label>Шаги (n): <input type="number" id="steps" value="10"></label>
      <label>Барьер по X (оставьте пустым, если нет): <input type="number" id="barrierX"></label>
      <label>Барьер по Y (оставьте пустым, если нет): <input type="number" id="barrierY"></label>
      <label>Барьер по Z (оставьте пустым, если нет): <input type="number" id="barrierZ"></label>
    </div>
    <div style="padding: 10px; width: 10%;">
      <label>Вероятность движения влево: <input type="number" step="0.01" id="probLeft" value="0.17"></label>
      <label>Вероятность движения вправо: <input type="number" step="0.01" id="probRight" value="0.17"></label>
      <label>Вероятность движения вверх: <input type="number" step="0.01" id="probUp" value="0.17"></label>
      <label>Вероятность движения вниз: <input type="number" step="0.01" id="probDown" value="0.17"></label>
      <label>Вероятность движения вверх по Z: <input type="number" step="0.01" id="probUpZ" value="0.16"></label>
      <label>Вероятность движения вниз по Z: <input type="number" step="0.01" id="probDownZ" value="0.16"></label>
      <label>Количество итераций симуляции: <input type="number" id="iterations" value="100"></label>
      <input type="checkbox" id="reflectingBarrierCheckbox"> <label for="reflectingBarrierCheckbox">Отражающий барьер</label>
      <label>Показать тепловую карту: <input type="checkbox" id="showHeatmap"></label>
      <label>Показать только расчет: <input type="checkbox" id="onlyCalculation"></label>
    </div>
    <div style="padding: 10px; width: 10%;">

      <button onclick="simulate()">Simulate</button>
    </div>
    <div id="randomWalkPlot" style="width: 60%; margin: auto"></div>
    <div style="padding: 10px;">
    <h3>Добавить область</h3>
    <label>Тип области:
      <select id="areaType">
        <option value="target">Целевая</option>
        <option value="barrier">Барьер</option>
      </select>
    </label>
    <label>Точка 1 (X1, Y1, Z1): <input type="number" id="areaX1" placeholder="X1">
      <input type="number" id="areaY1" placeholder="Y1">
      <input type="number" id="areaZ1" placeholder="Z1"></label>
    <label>Точка 2 (X2, Y2, Z2): <input type="number" id="areaX2" placeholder="X2">
      <input type="number" id="areaY2" placeholder="Y2">
      <input type="number" id="areaZ2" placeholder="Z2"></label>
    <button type="button" onclick="addArea()">Добавить область</button>
    <button type="button" onclick="clearAreas()">Очистить области</button>
    <ul id="areaList"></ul>
  </div>
  <div style="padding: 10px;">
    <button type="button" onclick="simulateRandomWalk()">Рассчитать</button>
    <p id="output"></p>
  </div>
  </div>
  <p id="result"></p>

</template>
<script setup lang="ts">
 
    let areaIdCounter = 0;
    let areas = [];

    function addArea() {
      const areaType = document.getElementById('areaType').value;
      const x1 = parseInt(document.getElementById('areaX1').value);
      const y1 = parseInt(document.getElementById('areaY1').value);
      const z1 = parseInt(document.getElementById('areaZ1').value);
      const x2 = parseInt(document.getElementById('areaX2').value);
      const y2 = parseInt(document.getElementById('areaY2').value);
      const z2 = parseInt(document.getElementById('areaZ2').value);

      const area = { id: areaIdCounter++, type: areaType, x1, y1, z1, x2, y2, z2 };
      areas.push(area);
      displayAreas();
    }

    function clearAreas() {
      areas = [];
      displayAreas();
    }

    function removeArea(id) {
      areas = areas.filter(area => area.id !== id);
      displayAreas();
    }

    function displayAreas() {
      const areaList = document.getElementById('areaList');
      areaList.innerHTML = '';
      areas.forEach(area => {
        const areaItem = document.createElement('li');
        areaItem.className = 'area-item';
        areaItem.innerHTML = `ID: ${area.id}, Тип: ${area.type}, Координаты: (${area.x1}, ${area.y1}, ${area.z1}) - (${area.x2}, ${area.y2}, ${area.z2})`;
        const removeButton = document.createElement('button');
        removeButton.textContent = 'Удалить';
        removeButton.onclick = () => removeArea(area.id);
        areaItem.appendChild(removeButton);
        areaList.appendChild(areaItem);
      });
    }

    function isInsideArea(x, y, z, area) {
      const minX = Math.min(area.x1, area.x2);
      const maxX = Math.max(area.x1, area.x2);
      const minY = Math.min(area.y1, area.y2);
      const maxY = Math.max(area.y1, area.y2);
      const minZ = Math.min(area.z1, area.z2);
      const maxZ = Math.max(area.z1, area.z2);

      return x >= minX && x <= maxX && y >= minY && y <= maxY && z >= minZ && z <= maxZ;
    }

    function simulate() {
      const startX = parseInt(document.getElementById('startX').value);
      const startY = parseInt(document.getElementById('startY').value);
      const startZ = parseInt(document.getElementById('startZ').value);
      const targetX = parseInt(document.getElementById('targetX').value);
      const targetY = parseInt(document.getElementById('targetY').value);
      const targetZ = parseInt(document.getElementById('targetZ').value);
      const steps = parseInt(document.getElementById('steps').value);
      const probLeft = parseFloat(document.getElementById('probLeft').value);
      const probRight = parseFloat(document.getElementById('probRight').value);
      const probUp = parseFloat(document.getElementById('probUp').value);
      const probDown = parseFloat(document.getElementById('probDown').value);
      const probUpZ = parseFloat(document.getElementById('probUpZ').value);
      const probDownZ = parseFloat(document.getElementById('probDownZ').value);
      const barrierX = document.getElementById('barrierX').value ? parseInt(document.getElementById('barrierX').value) : null;
      const barrierY = document.getElementById('barrierY').value ? parseInt(document.getElementById('barrierY').value) : null;
      const barrierZ = document.getElementById('barrierZ').value ? parseInt(document.getElementById('barrierZ').value) : null;
      const iterations = parseInt(document.getElementById('iterations').value);
      const reflectingBarrier = document.getElementById('reflectingBarrierCheckbox').checked;
      const showHeatmap = document.getElementById('showHeatmap').checked;
      const onlyCalculation = document.getElementById('onlyCalculation').checked;

      let countSuccess = 0;
      let allPaths = [];
      let heatmapData = {};

      for (let i = 0; i < iterations; i++) {
        let x = startX, y = startY, z = startZ;
        let path = [[x, y, z]];
        let reachedTarget = false;
        let terminated = false;

        for (let step = 0; step < steps; step++) {
          const rand = Math.random();
          if (rand < probLeft) x--;
          else if (rand < probLeft + probRight) x++;
          else if (rand < probLeft + probRight + probUp) y++;
          else if (rand < probLeft + probRight + probUp + probDown) y--;
          else if (rand < probLeft + probRight + probUp + probDown + probUpZ) z++;
          else z--;

          // Check areas
          for (const area of areas) {
            if (isInsideArea(x, y, z, area)) {
              if (area.type === 'target') {
                reachedTarget = true;
                countSuccess++;
                terminated = true;
              }
              if (area.type === 'barrier') {
                terminated = true;
              }
              break;
            }
          }
          if (terminated) break;

          // Reflecting barriers
          if (reflectingBarrier) {
            if ((barrierX !== null && (x === barrierX || x === 0)) || (barrierY !== null && (y === barrierY || y === 0)) || (barrierZ !== null && (z === barrierZ || z === 0))) {
              x = startX; y = startY; z = startZ;
            }
          } else {
            // Absorbing barriers
            if ((barrierX !== null && x === barrierX) || (barrierY !== null && y === barrierY) || (barrierZ !== null && z === barrierZ)) {
              break;
            }
          }

          path.push([x, y, z]);

          // Обновление данных для тепловой карты
          const key = `${x},${y},${z}`;
          if (heatmapData[key]) {
            heatmapData[key]++;
          } else {
            heatmapData[key] = 1;
          }
        }

        if (reachedTarget) {
          if (!showHeatmap && !onlyCalculation) {
            allPaths.push({
              type: 'scatter3d',
              mode: 'lines',
              x: path.map(p => p[0]),
              y: path.map(p => p[1]),
              z: path.map(p => p[2]),
              line: {
                width: 6,
                color: 'green'
              }
            });
          }
        } else {
          if (!showHeatmap && !onlyCalculation) { // Добавляем проверку на включение тепловой карты
            allPaths.push({
              type: 'scatter3d',
              mode: 'lines',
              x: path.map(p => p[0]),
              y: path.map(p => p[1]),
              z: path.map(p => p[2]),
              line: {
                width: 6,
                color: 'gray'
              }
            });
          }
        }
      }

      areas.forEach(area => {
        let vertices = generateVertices(area);
        allPaths.push({
          type: 'mesh3d',
          x: vertices.x,
          y: vertices.y,
          z: vertices.z,
          i: vertices.i,
          j: vertices.j,
          k: vertices.k,
          opacity: 0.5,
          color: area.type === 'target' ? 'lightgreen' : 'orange'
        });
      });

      // Создание тепловой карты, если она включена
      if (showHeatmap) {
        const heatmapX = [];
        const heatmapY = [];
        const heatmapZ = [];
        const heatmapValues = [];

        for (const key in heatmapData) {
          const [x, y, z] = key.split(',').map(Number);
          heatmapX.push(x);
          heatmapY.push(y);
          heatmapZ.push(z);
          heatmapValues.push(heatmapData[key]);
        }

        const heatmapTrace = {
          x: heatmapX,
          y: heatmapY,
          z: heatmapZ,
          type: 'scatter3d',
          mode: 'markers',
          marker: {
            size: heatmapValues,
            color: heatmapValues,
            colorscale: 'Viridis',
            opacity: 0.8
          }
        };

        allPaths.push(heatmapTrace);
      }

      if (!onlyCalculation) {
        Plotly.newPlot('randomWalkPlot', allPaths, {
          title: 'Random Walk',
          scene: {
            xaxis: { title: 'X' },
            yaxis: { title: 'Y' },
            zaxis: { title: 'Z' }
          }
        });
      }
    }

    function generateVertices(area) {
      const x1 = area.x1, y1 = area.y1, z1 = area.z1;
      const x2 = area.x2, y2 = area.y2, z2 = area.z2;

      return {
        x: [x1, x2, x2, x1, x1, x2, x2, x1],
        y: [y1, y1, y2, y2, y1, y1, y2, y2],
        z: [z1, z1, z1, z1, z2, z2, z2, z2],
        i: [0, 0, 0, 1, 1, 2, 2, 3, 4, 5, 6, 7],
        j: [1, 2, 3, 3, 4, 4, 5, 5, 6, 6, 7, 7],
        k: [3, 3, 1, 2, 5, 5, 6, 6, 7, 7, 0, 0]
      };
    }

    function simulateRandomWalk() {
      const startX = parseInt(document.getElementById('startX').value);
      const startY = parseInt(document.getElementById('startY').value);
      const startZ = parseInt(document.getElementById('startZ').value);
      const targetX = parseInt(document.getElementById('targetX').value);
      const targetY = parseInt(document.getElementById('targetY').value);
      const targetZ = parseInt(document.getElementById('targetZ').value);
      const steps = parseInt(document.getElementById('steps').value);
      const probLeft = parseFloat(document.getElementById('probLeft').value);
      const probRight = parseFloat(document.getElementById('probRight').value);
      const probUp = parseFloat(document.getElementById('probUp').value);
      const probDown = parseFloat(document.getElementById('probDown').value);
      const probUpZ = parseFloat(document.getElementById('probUpZ').value);
      const probDownZ = parseFloat(document.getElementById('probDownZ').value);
      const barrierX = document.getElementById('barrierX').value ? parseInt(document.getElementById('barrierX').value) : null;
      const barrierY = document.getElementById('barrierY').value ? parseInt(document.getElementById('barrierY').value) : null;
      const barrierZ = document.getElementById('barrierZ').value ? parseInt(document.getElementById('barrierZ').value) : null;
      const iterations = parseInt(document.getElementById('iterations').value);
      const reflectingBarrier = document.getElementById('reflectingBarrierCheckbox').checked;

      let countSuccess = 0;
      let crossSuccess = 0;
      let returnToStart = 0;
      let timesToTarget = [];
      let timesToFirstReturn = [];
      let successRates = [];

      for (let i = 0; i < iterations; i++) {
        let x = startX;
        let y = startY;
        let z = startZ;
        let pathValid = true;
        let crossed = false;
        let stepsToTarget = -1;
        let firstReturn = false;

        for (let step = 0; step < steps; step++) {
          const rand = Math.random();
          if (rand < probLeft) x--;
          else if (rand < probLeft + probRight) x++;
          else if (rand < probLeft + probRight + probUp) y++;
          else if (rand < probLeft + probRight + probUp + probDown) y--;
          else if (rand < probLeft + probRight + probUp + probDown + probUpZ) z++;
          else z--;

          if (!firstReturn && x === startX && y === startY && z === startZ) {
            timesToFirstReturn.push(step + 1);
            firstReturn = true;
          }

          // Check areas
          for (const area of areas) {
            if (isInsideArea(x, y, z, area)) {
              if (area.type === 'target') {
                stepsToTarget = step + 1;
                crossed = true;
                pathValid = true;
                break;
              }
              if (area.type === 'barrier') {
                pathValid = false;
                break;
              }
            }
          }
          if (!pathValid) break;

          if (x === targetX && y === targetY && z === targetZ && stepsToTarget === -1) {
            stepsToTarget = step + 1;
            crossed = true;
          }

          if (reflectingBarrier) {
            if (x === 0) x = startX;
            if (y === 0) y = startY;
            if (z === 0) z = startZ;
          }
        }

        if (stepsToTarget !== -1) timesToTarget.push(stepsToTarget);
        if (crossed && pathValid) crossSuccess++;
        if (x === startX && y === startY && z === startZ) returnToStart++;

        if ((i + 1) % 1000 === 0 || i === iterations - 1) {
          successRates.push((crossSuccess / (i + 1)) * 100);
        }
      }

      const meanTimeToTarget = timesToTarget.reduce((a, b) => a + b, 0) / timesToTarget.length;
      const varianceTimeToTarget = timesToTarget.map(time => Math.pow(time - meanTimeToTarget, 2)).reduce((a, b) => a + b, 0) / timesToTarget.length;
      const stdDeviationTimeToTarget = Math.sqrt(varianceTimeToTarget);
      const coefficientOfVariation = stdDeviationTimeToTarget / meanTimeToTarget;
      const probability = countSuccess / iterations;
      const crossProbability = crossSuccess / iterations;
      const returnProbability = returnToStart / iterations;

      document.getElementById('output').innerHTML =
        'Среднее время достижения цели: ' + meanTimeToTarget.toFixed(4) + ' шагов.<br>' +
        'Дисперсия: ' + varianceTimeToTarget.toFixed(4) + '.<br>' +
        'Стандартное отклонение: ' + stdDeviationTimeToTarget.toFixed(4) + '.<br>' +
        'Коэффициент вариации: ' + coefficientOfVariation.toFixed(4) + '.<br>' +
        'Вероятность достичь цели: ' + (probability * 100).toFixed(4) + '%.<br>' +
        'Вероятность пересечения целевой точки: ' + (crossProbability * 100).toFixed(4) + '%.<br>' +
        'Вероятность возврата в начальную точку: ' + (returnProbability * 100).toFixed(4) + '%.';
    } 
</script>