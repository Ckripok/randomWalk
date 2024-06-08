<template>
  <div class="control-panel" style="width: 100%">
    <h1>Двумерное случайное блуждание на плоскости</h1>
  </div>
  <div class="container">
    <div class="control-panel">
      <!-- <label>Начальная точка X: <input type="number" id="startX" value="0"></label> -->

      <div class="flex flex-col">
        <label>Начальная точка X</label>
        <el-input-number
          v-model="calculateTwoD.startX"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Начальная точка Y: <input type="number" id="startY" value="0"></label> -->
      <div class="flex flex-col">
        
        <label>Начальная точка Y: </label>
        <el-input-number
          v-model="calculateTwoD.startY"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Целевая точка X: <input type="number" id="targetX" value="5"></label> -->
      <div class="flex flex-col">
        <label>Целевая точка X: </label>
        <el-input-number
          v-model="calculateTwoD.targetX"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Целевая точка Y: <input type="number" id="targetY" value="5"></label> -->
      <div class="flex flex-col">
        <label>Целевая точка Y: </label>
        <el-input-number
          v-model="calculateTwoD.targetY"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>

      <!-- <label>Шаги (n): <input type="number" id="steps" value="10"></label> -->
      <div class="flex flex-col">
        <label>Шаги (n): </label>
        <el-input-number
          v-model="calculateTwoD.steps"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>

      <!-- <label>Вероятность движения влево: <input type="number" step="0.01" id="probLeft" value="0.25"></label> -->
      
      <div class="flex flex-col">
        <label>Вероятность движения влево: </label>
        <el-input-number
          v-model="calculateTwoD.probLeft"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>

      <!-- <label>Вероятность движения вправо: <input type="number" step="0.01" id="probRight" value="0.25"></label> -->

      <div class="flex flex-col">
        <label>Вероятность движения вправо: </label>
        <el-input-number
          v-model="calculateTwoD.probRight"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Вероятность движения вверх: <input type="number" step="0.01" id="probUp" value="0.25"></label> -->

      <div class="flex flex-col">
        <label>Вероятность движения вверх: </label>
        <el-input-number
          v-model="calculateTwoD.probUp"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Вероятность движения вниз: <input type="number" step="0.01" id="probDown" value="0.25"></label> -->

      <div class="flex flex-col">
        <label>Вероятность движения вниз: </label>
        <el-input-number
          v-model="calculateTwoD.probDown"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Вероятность остаться на месте: <input type="number" step="0.01" id="probStay" value="0"></label> -->

      <div class="flex flex-col">
        <label>Вероятность остаться на месте: </label>
        <el-input-number
          v-model="calculateTwoD.probStay"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Количество итераций симуляции: <input type="number" id="iterations" value="1000"></label> -->

      <div class="flex flex-col">
        <label>Количество итераций симуляции: </label>
        <el-input-number
          v-model="calculateTwoD.iterations"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Тип области:
        <select id="areaType">
          <option value="barrier">Барьер</option>
          <option value="reflecting-barrier">Отражающий барьер</option>
          <option value="target">Целевая область</option>
        </select>
      </label> -->
      <div id="chartSelection" class="flex flex-col space-y-2">
        <el-select v-model="calculateTwoD.areaType" placeholder="Select" size="large" style="width: 240px">
          <el-option
            v-for="item in [{label:'Барьер', value:'barrier'}, {label:'Отражающий барьер', value:'reflecting-barrier'}, {label:'Целевая область', value:'target'}]"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
        </div>
      <!-- <label>Координаты первой точки X: <input type="number" id="areaX1" value="0"></label> -->
      <div class="flex flex-col">
        <label>Координаты первой точки X: </label>
        <el-input-number
          v-model="calculateTwoD.areaX1"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>
      <!-- <label>Координаты первой точки Y: <input type="number" id="areaY1" value="0"></label> -->

      <div class="flex flex-col">
        <label>Координаты первой точки Y: </label>
        <el-input-number
          v-model="calculateTwoD.areaY1"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>

      <!-- <label>Координаты второй точки X: <input type="number" id="areaX2" value="2"></label> -->

      <div class="flex flex-col">
        <label>Координаты второй точки X: </label>
        <el-input-number
          v-model="calculateTwoD.areaX2"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div> 

      <!-- <label>Координаты второй точки Y: <input type="number" id="areaY2" value="2"></label> -->

      <div class="flex flex-col">
        <label>Координаты второй точки Y: </label>
        <el-input-number
          v-model="calculateTwoD.areaY2"
          style="width: 240px"
          placeholder="Введите число"
          increase-icon
        />
      </div>

      <!-- <button onclick="addArea()">Добавить область</button> -->
      <div class="flex flex-col" >
      <el-button class="w-[240px] mb-2" type="primary" @click="addArea()"
          >Добавить область</el-button
        >
      <!-- <button onclick="clearAreas()">Очистить области</button> -->
      <el-button class="w-[240px] mb-2 !ml-0" type="primary" @click="clearAreas()">Очистить области</el-button>
      </div>
      <!-- <label>Показать тепловую карту: <input type="checkbox" id="showHeatmap"></label> -->
      <div class="flex flex-col" id="showHeatmap">
        <label>Показать тепловую карту: </label>
        <el-checkbox v-model="calculateTwoD.showHeatmap" />
        </div>
      <!-- <label>Показать только расчет: <input type="checkbox" id="onlyCalculation"></label> -->
      <div class="flex flex-col">
        <label>Показать только расчет: </label>
        <el-checkbox v-model="calculateTwoD.onlyCalculation" />
        </div>
      <!-- <button onclick="simulate()">Simulate</button> -->
      <el-button class="w-[240px] mb-2" type="primary" @click="simulate()">Симуляция</el-button>
      <h3>Список областей</h3>
      <ul id="areaList"></ul>
    </div>
    <div id="randomWalkPlot" class="chart-panel"></div>
    <div class="result-panel">
      <p id="output"></p>
    </div>
  </div>
</template>
<script setup lang="ts"> 
import { reactive, ref } from 'vue'
// аавыавыа
class TwoD {
    public startX: number = 0
    public startY: number = 0
    public targetX: number = 5
    public targetY: number = 5
    public steps: number = 10
    public probLeft: number = 0.25
    public probRight: number = 0.25
    public probUp: number = 0.25
    public probDown: number = 0.25
    public probStay: number = 0
    public iterations: number = 1000
    public areaType: string = 'barrier'
    public areaX1: number = 0
    public areaY1: number = 0
    public areaX2: number = 2
    public areaY2: number = 2
    public showHeatmap: boolean = false
    public onlyCalculation: boolean = false
    public heatmap: any
    public heatmapData: any
    public heatmapOptions: any
}
const calculateTwoD = ref(new TwoD())

    let areas = [];
    let areaIdCounter = 0;

    function addArea() {
      const areaType = document.getElementById('areaType').value;
      const x1 = parseInt(document.getElementById('areaX1').value);
      const y1 = parseInt(document.getElementById('areaY1').value);
      const x2 = parseInt(document.getElementById('areaX2').value);
      const y2 = parseInt(document.getElementById('areaY2').value);

      const area = { id: areaIdCounter++, type: areaType, x1, y1, x2, y2 };
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
        areaItem.innerHTML = `ID: ${area.id}, Тип: ${area.type}, Координаты: (${area.x1}, ${area.y1}) - (${area.x2}, ${area.y2})`;
        const removeButton = document.createElement('button');
        removeButton.textContent = 'Удалить';
        removeButton.onclick = () => removeArea(area.id);
        areaItem.appendChild(removeButton);
        areaList.appendChild(areaItem);
      });
    }

    function simulate() {
      const startX = parseInt(document.getElementById('startX').value);
      const startY = parseInt(document.getElementById('startY').value);
      const targetX = parseInt(document.getElementById('targetX').value);
      const targetY = parseInt(document.getElementById('targetY').value);
      const steps = parseInt(document.getElementById('steps').value);
      const probLeft = parseFloat(document.getElementById('probLeft').value);
      const probRight = parseFloat(document.getElementById('probRight').value);
      const probUp = parseFloat(document.getElementById('probUp').value);
      const probDown = parseFloat(document.getElementById('probDown').value);
      const probStay = parseFloat(document.getElementById('probStay').value);
      const iterations = parseInt(document.getElementById('iterations').value);
      const showHeatmap = document.getElementById('showHeatmap').checked;
      const onlyCalculation = document.getElementById('onlyCalculation').checked;

      let countSuccess = 0;
      let notReachedCount = 0;
      let hitBarrierCount = 0;
      let bouncedOffBarrierCount = 0;
      let timesToTarget = [];
      let timesToFirstReturn = [];
      let maxDeviationFromStart = 0;
      let heatmapData = {};
      let successfulTraces = [];
      let unsuccessfulTraces = [];
      let areaTraces = [];

      // Create area traces
      for (const area of areas) {
        const trace = {
          x: [area.x1, area.x2, area.x2, area.x1, area.x1],
          y: [area.y1, area.y1, area.y2, area.y2, area.y1],
          mode: 'lines',
          fill: 'toself',
          fillcolor: area.type === 'barrier' ? 'rgba(255, 165, 0, 0.5)' : (area.type === 'reflecting-barrier' ? 'rgba(173, 216, 230, 0.5)' : 'rgba(144, 238, 144, 0.5)'),
          line: { color: area.type === 'barrier' ? 'orange' : (area.type === 'reflecting-barrier' ? 'lightblue' : 'green') }
        };
        areaTraces.push(trace);
      }

      for (let i = 0; i < iterations; i++) {
        let positionX = startX;
        let positionY = startY;
        let xValues = [startX];
        let yValues = [startY];
        let pathValid = true;
        let reachedTarget = false;
        let stepsToTarget = -1;
        let firstReturn = false;
        let maxDeviation = 0;

        for (let j = 0; j < steps; j++) {
          const random = Math.random();
          let newPositionX = positionX;
          let newPositionY = positionY;

          if (random < probLeft) {
            newPositionX--;
          } else if (random < probLeft + probRight) {
            newPositionX++;
          } else if (random < probLeft + probRight + probUp) {
            newPositionY++;
          } else if (random < probLeft + probRight + probUp + probDown) {
            newPositionY--;
          }

          for (const area of areas) {
            if (isInArea(newPositionX, newPositionY, area)) {
              if (area.type === 'barrier') {
                hitBarrierCount++;
                pathValid = false;
                break;
              } else if (area.type === 'reflecting-barrier') {
                if (newPositionX < area.x1) {
                  newPositionX = area.x1 + (area.x1 - newPositionX);
                } else if (newPositionX > area.x2) {
                  newPositionX = area.x2 - (newPositionX - area.x2);
                }
                if (newPositionY < area.y1) {
                  newPositionY = area.y1 + (area.y1 - newPositionY);
                } else if (newPositionY > area.y2) {
                  newPositionY = area.y2 - (newPositionY - area.y2);
                }
                bouncedOffBarrierCount++;
              } else if (area.type === 'target') {
                stepsToTarget = j + 1;
                reachedTarget = true;
                pathValid = true;
                break;
              }
            }
          }

          if (!pathValid) {
            break;
          }

          positionX = newPositionX;
          positionY = newPositionY;
          xValues.push(positionX);
          yValues.push(positionY);

          maxDeviation = Math.max(maxDeviation, Math.abs(positionX - startX), Math.abs(positionY - startY));

          if (!firstReturn && positionX === startX && positionY === startY) {
            timesToFirstReturn.push(j + 1);
            firstReturn = true;
          }

          if (reachedTarget) break;
          if (positionX === targetX && positionY === targetY) {
            stepsToTarget = j + 1;
            reachedTarget = true;
            countSuccess++;
            break;
          }

          // Обновление данных для тепловой карты
          const key = `${positionX},${positionY}`;
          if (heatmapData[key]) {
            heatmapData[key]++;
          } else {
            heatmapData[key] = 1;
          }
        }

        if (!reachedTarget) {
          notReachedCount++;
        }

        if (stepsToTarget !== -1) {
          timesToTarget.push(stepsToTarget);
        }

        maxDeviationFromStart = Math.max(maxDeviationFromStart, maxDeviation);

        if (pathValid && !showHeatmap && !onlyCalculation) {
          successfulTraces.push({
            x: xValues,
            y: yValues,
            mode: 'lines',
            line: { color: 'green' }
          });
        } else if (!pathValid && !showHeatmap && !onlyCalculation) {
          unsuccessfulTraces.push({
            x: xValues,
            y: yValues,
            mode: 'lines',
            line: { color: 'gray' }
          });
        }
      }

      // Calculation results
      const meanTimeToTarget = timesToTarget.reduce((a, b) => a + b, 0) / timesToTarget.length;
      const minTimeToTarget = Math.min(...timesToTarget);
      const maxTimeToTarget = Math.max(...timesToTarget);
      const varianceTimeToTarget = timesToTarget.map(time => Math.pow(time - meanTimeToTarget, 2)).reduce((a, b) => a + b, 0) / timesToTarget.length;
      const stdDeviationTimeToTarget = Math.sqrt(varianceTimeToTarget);
      const coefficientOfVariation = stdDeviationTimeToTarget / meanTimeToTarget;
      const probability = countSuccess / iterations;
      const probabilityMinTime = timesToTarget.filter(time => time === minTimeToTarget).length / timesToTarget.length;
      const probabilityMaxTime = timesToTarget.filter(time => time === maxTimeToTarget).length / timesToTarget.length;
      const probabilityFirstReturn = timesToFirstReturn.length / iterations;

      document.getElementById('output').innerHTML =
        'Среднее время достижения цели: ' + meanTimeToTarget.toFixed(4) + ' шагов.<br>' +
        'Минимальное время достижения цели: ' + minTimeToTarget + ' шагов.<br>' +
        'Максимальное время достижения цели: ' + maxTimeToTarget + ' шагов.<br>' +
        'Дисперсия: ' + varianceTimeToTarget.toFixed(4) + '.<br>' +
        'Стандартное отклонение: ' + stdDeviationTimeToTarget.toFixed(4) + '.<br>' +
        'Коэффициент вариации: ' + coefficientOfVariation.toFixed(4) + '.<br>' +
        'Вероятность достичь цели: ' + (probability).toFixed(4) + '.<br>' +
        'Количество симуляций, достигших цели: ' + countSuccess + '.<br>' +
        'Количество симуляций, не достигших цели: ' + notReachedCount + '.<br>' +
        'Количество симуляций, разбившихся об барьеры: ' + hitBarrierCount + '.<br>' +
        'Количество симуляций, оттолкнувшихся от барьера: ' + bouncedOffBarrierCount + '.<br>' +
        'Вероятность достижения цели за минимальное время: ' + (probabilityMinTime).toFixed(4) + '.<br>' +
        'Вероятность достижения цели за максимальное время: ' + (probabilityMaxTime).toFixed(4) + '.<br>' +
        'Вероятность пересечения начальной точки: ' + (probabilityFirstReturn).toFixed(4) + '%.<br>' +
        'Максимальное отклонение от начальной точки: ' + maxDeviationFromStart + '.<br>';

      const targetHitProbability = (countSuccess / iterations) * 100;
      let allTraces = areaTraces.concat(unsuccessfulTraces).concat(successfulTraces);

      // Создание тепловой карты, если она включена
      if (showHeatmap) {
        const heatmapX = [];
        const heatmapY = [];
        const heatmapZ = [];

        for (const key in heatmapData) {
          const [x, y] = key.split(',').map(Number);
          heatmapX.push(x);
          heatmapY.push(y);
          heatmapZ.push(heatmapData[key]);
        }

        const heatmapTrace = {
          x: heatmapX,
          y: heatmapY,
          z: heatmapZ,
          type: 'heatmap',
          colorscale: 'Viridis'
        };

        allTraces.push(heatmapTrace);
      }

      if (!onlyCalculation) {
        Plotly.newPlot('randomWalkPlot', allTraces, { title: `Random Walks (${iterations} Iterations, Target Hit Probability: ${targetHitProbability.toFixed(2)}%)` });
      }
    }

    function isInArea(x, y, area) {
      return x >= Math.min(area.x1, area.x2) && x <= Math.max(area.x1, area.x2) &&
             y >= Math.min(area.y1, area.y2) && y <= Math.max(area.y1, area.y2);
    } 
</script>