<template>
  <div class="control-panel" style="width: 100%">
    <h1>Одномерное случайное блуждание с графиком</h1>
  </div>
  <div class="container">
    <div class="control-panel">
      <!-- <el-input-number v-model="startX" style="width: 240px" placeholder="Please input" /> -->
      <label>Начальная точка: <input type="number" id="startX" value="0" /></label>
      <label>Целевая точка: <input type="number" id="targetX" value="5" /></label>
      <input type="checkbox" id="deferTargetCheckbox" onchange="toggleDeferTarget()" />
      <label for="deferTargetCheckbox">Не регистрировать до</label>
      <div id="deferTargetStep" style="display: none">
        <label>Шаг: <input type="number" id="deferStep" value="1" /></label>
      </div>
      <label>Шаги (n): <input type="number" id="steps" value="10" /></label>
      <label
        >Вероятность движения влево: <input type="number" step="0.01" id="probLeft" value="0.5"
      /></label>
      <label
        >Вероятность движения вправо: <input type="number" step="0.01" id="probRight" value="0.5"
      /></label>
      <label>Длина шага влево: <input type="number" id="stepLeft" value="1" /></label>
      <label>Длина шага вправо: <input type="number" id="stepRight" value="1" /></label>
      <label>Положительный барьер: <input type="number" id="positiveBarrier" value="1000" /></label>
      <label>Отрицательный барьер: <input type="number" id="negativeBarrier" value="1000" /></label>
      <label
        >Количество итераций симуляции: <input type="number" id="iterations" value="1000"
      /></label>
      <input type="checkbox" id="reflectingBarrierCheckbox" />
      <label for="reflectingBarrierCheckbox">Отражающий барьер</label>
      <label
        >Среднек. отклонение: <input type="number" step="0.01" id="stdDeviation" value="1"
      /></label>
      <label
        >Заданная вероятность Р: <input type="number" step="0.01" id="probabilityP" value="0.5"
      /></label>
      <label
        >Верхняя граница интервала: <input type="number" step="0.01" id="upperInterval" value="1"
      /></label>
      <hr />
      <label>От: <input type="number" id="periodFrom" /></label>
      <label>До: <input type="number" id="periodTo" /></label>
      <label
        >Коэффициент изменения вероятности: <input type="number" step="0.01" id="probChange"
      /></label>
      <button onclick="addPeriod()">Добавить период</button>
      <div id="periods"></div>
      <button @click="simulate()">Simulate</button>
    </div>
    <div id="randomWalkPlot" class="chart-panel"></div>
    <div class="result-panel">
      <button type="button" onclick="simulateRandomWalk()">Рассчитать</button>
      <p id="output"></p>
      <div id="chartSelection">
        <label for="dataType">Тип данных:</label>
        <select id="dataType">
          <option value="graph">Данные графика</option>
          <option value="calculation">Данные расчета</option>
        </select>
        <label for="xAxis">Выберите ось X:</label>
        <select id="xAxis">
          <option value="stepsToTarget">Количество шагов до цели</option>
          <option value="firstReturn">Время до первого возвращения</option>
          <option value="countSuccess">Количество успехов</option>
          <option value="hitBarrierCount">Количество ударов об барьер</option>
          <option value="bouncedOffBarrierCount">Количество отражений от барьера</option>
          <option value="maxDeviationFromStart">Максимальное отклонение от начальной точки</option>
        </select>
        <label for="yAxis">Выберите ось Y:</label>
        <select id="yAxis">
          <option value="count">Количество симуляций</option>
        </select>
        <button onclick="drawChart()">Построить график</button>
      </div>
      <div id="customChart"></div>
    </div>
  </div>

  <!-- <VuePlotly :data="data" :layout="layout" :display-mode-bar="false"></VuePlotly> -->
  <div id="randomWalkPlot"></div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
class OneD {
  public startX: number = 0
  public targetX: number = 5
  public deferTargetCheckbox: boolean = false
  public steps: number = 10
  public probLeft: number = 0.5
  public probRight: number = 0.5
  public stepLeft: number = 1
  public stepRight: number = 1
  public positiveBarrier: number = 1000
  public negativeBarrier: number = 1000
  public iterations: number = 1000
  public reflectingBarrierCheckbox: boolean = false
  public deferTargetStep: number = 0
  public deferStep: number = 0
  public stdDeviation: number = 1
  public probabilityP: number = 0.5
  public upperInterval: number = 1
}
const calculationOneD = ref(new OneD())
let periods = []
let probHistory = []
let simulationData = {
  graph: {
    stepsToTarget: [],
    firstReturn: []
  },
  calculation: {
    stepsToTarget: [],
    firstReturn: [],
    countSuccess: 0,
    hitBarrierCount: 0,
    bouncedOffBarrierCount: 0,
    maxDeviationFromStart: 0
  }
}

function toggleDeferTarget() {
  const deferTargetStep = document.getElementById('deferTargetStep')
  if (document.getElementById('deferTargetCheckbox').checked) {
    deferTargetStep.style.display = 'block'
  } else {
    deferTargetStep.style.display = 'none'
  }
}

function addPeriod() {
  const from = parseInt(document.getElementById('periodFrom').value)
  const to = parseInt(document.getElementById('periodTo').value)
  const change = parseFloat(document.getElementById('probChange').value)
  const periodId = periods.length
  periods.push({ id: periodId, from, to, change })

  const periodDiv = document.getElementById('periods')
  const periodBlock = document.createElement('div')
  periodBlock.className = 'period-block'
  periodBlock.id = `period-${periodId}`
  periodBlock.innerHTML = `
              <span class="remove-period" onclick="removePeriod(${periodId})">×</span>
              <p>От: ${from}, До: ${to}, Коэффициент: ${change}</p>
            `
  periodDiv.appendChild(periodBlock)
}

function removePeriod(id) {
  periods = periods.filter((period) => period.id !== id)
  document.getElementById(`period-${id}`).remove()
}

function simulate() {
  const startX = parseInt(document.getElementById('startX').value)
  const targetX = parseInt(document.getElementById('targetX').value)
  const steps = parseInt(document.getElementById('steps').value)
  const initialProbLeft = parseFloat(document.getElementById('probLeft').value)
  const initialProbRight = parseFloat(document.getElementById('probRight').value)
  const stepLeft = parseInt(document.getElementById('stepLeft').value)
  const stepRight = parseInt(document.getElementById('stepRight').value)
  const positiveBarrier = document.getElementById('positiveBarrier').value
    ? parseInt(document.getElementById('positiveBarrier').value)
    : null
  const negativeBarrier = document.getElementById('negativeBarrier').value
    ? parseInt(document.getElementById('negativeBarrier').value)
    : null
  const reflectingBarrier = document.getElementById('reflectingBarrierCheckbox').checked
  const iterations = parseInt(document.getElementById('iterations').value)
  const deferTarget = document.getElementById('deferTargetCheckbox').checked
  const deferStep = deferTarget ? parseInt(document.getElementById('deferStep').value) : 0
  const stdDeviation = parseFloat(document.getElementById('stdDeviation').value)
  const probabilityP = parseFloat(document.getElementById('probabilityP').value)
  const upperInterval = parseFloat(document.getElementById('upperInterval').value)

  let data = []
  let countSuccess = 0
  let countCrossTarget = 0
  let shapes = []
  probHistory = []
  simulationData.graph.stepsToTarget = []
  simulationData.graph.firstReturn = []

  for (let i = 0; i < iterations; i++) {
    let probLeft = initialProbLeft
    let probRight = initialProbRight
    let position = startX
    let x = [0]
    let y = [startX]
    let hitTarget = false
    let probArray = [probRight] // To store probability of each step
    let firstReturn = -1

    for (let j = 0; j < steps; j++) {
      for (let period of periods) {
        if (j >= period.from && j <= period.to) {
          probRight += period.change
          probLeft = 1 - probRight
          if (probRight < 0) probRight = 0
          if (probRight > 1) probRight = 1
          probLeft = 1 - probRight
        }
      }

      let previousPosition = position

      if (Math.random() < probRight) {
        position += stepRight
      } else {
        position -= stepLeft
      }

      // Check if the target is crossed
      if (
        (previousPosition < targetX && position >= targetX) ||
        (previousPosition > targetX && position <= targetX)
      ) {
        hitTarget = true
        countCrossTarget++
        simulationData.graph.stepsToTarget.push(j + 1)
        break
      }

      if (
        reflectingBarrier &&
        ((positiveBarrier !== null && position === positiveBarrier) ||
          (negativeBarrier !== null && position === negativeBarrier))
      ) {
        position = startX
      } else if (
        !reflectingBarrier &&
        ((positiveBarrier !== null && position === positiveBarrier) ||
          (negativeBarrier !== null && position === negativeBarrier))
      ) {
        simulationData.calculation.hitBarrierCount++
        break
      }

      // Check for first return to start
      if (position === startX && firstReturn === -1) {
        firstReturn = j + 1
        simulationData.graph.firstReturn.push(firstReturn)
      }

      x.push(j + 1)
      y.push(position)
      probArray.push(probRight) // Store probability of current step
    }

    if (hitTarget) countSuccess++ // Only count as success if target was hit

    probHistory.push(probArray) // Store probability array for this iteration

    let color = hitTarget ? 'green' : 'gray'
    data.push({
      x: x,
      y: y,
      mode: 'lines+markers',
      line: { color: color },
      marker: {
        size: y.map((pos) => (pos === targetX ? 10 : 2)),
        color: y.map((pos) => (pos === targetX ? 'red' : color))
      }
    })
  }

  // Create shaded areas for periods
  for (let period of periods) {
    shapes.push({
      type: 'rect',
      xref: 'x',
      yref: 'paper',
      x0: period.from,
      x1: period.to,
      y0: 0,
      y1: 1,
      fillcolor: 'rgba(255, 165, 0, 0.2)',
      line: { width: 0 }
    })
  }

  const probabilityOfCrossTarget = ((countCrossTarget / iterations) * 100).toFixed(4)

  Plotly.newPlot('randomWalkPlot', data, {
    title: `Random Walks: Probability of Hitting Target ${probabilityOfCrossTarget}%`,
    shapes: shapes,
    xaxis: {
      title: 'Steps'
    },
    yaxis: {
      title: 'Position'
    },
    hovermode: 'closest' // Добавление интерактивных всплывающих подсказок
  })

  // Add click event to show probability
  document.getElementById('randomWalkPlot').on('plotly_click', function (data) {
    let pointIndex = data.points[0].pointIndex
    let curveNumber = data.points[0].curveNumber
    let prob = probHistory[curveNumber][pointIndex]
    alert(`Шаг: ${pointIndex + 1}, Вероятность хода вправо: ${prob.toFixed(2)}`)
  })
}

function updateChart(data) {
  Plotly.react('randomWalkPlot', data, {
    title: `Random Walks: Probability of Hitting Target ${probabilityOfCrossTarget}%`,
    shapes: shapes
  })
}

function simulateRandomWalk() {
  const startX = parseInt(document.getElementById('startX').value)
  const targetX = parseInt(document.getElementById('targetX').value)
  const n = parseInt(document.getElementById('steps').value)
  const initialProbLeft = parseFloat(document.getElementById('probLeft').value)
  const initialProbRight = parseFloat(document.getElementById('probRight').value)
  const stepLeft = parseInt(document.getElementById('stepLeft').value)
  const stepRight = parseInt(document.getElementById('stepRight').value)
  const positiveBarrier = document.getElementById('positiveBarrier').value
    ? parseInt(document.getElementById('positiveBarrier').value)
    : null
  const negativeBarrier = document.getElementById('negativeBarrier').value
    ? parseInt(document.getElementById('negativeBarrier').value)
    : null
  const iterations = parseInt(document.getElementById('iterations').value)
  const reflectingBarrier = document.getElementById('reflectingBarrierCheckbox').checked
  const deferTarget = document.getElementById('deferTargetCheckbox').checked
  const deferStep = deferTarget ? parseInt(document.getElementById('deferStep').value) : 0
  const stdDeviation = parseFloat(document.getElementById('stdDeviation').value)
  const probabilityP = parseFloat(document.getElementById('probabilityP').value)
  const upperInterval = parseFloat(document.getElementById('upperInterval').value)

  let countSuccess = 0
  let timesToTarget = []
  let timesToFirstReturn = []
  let hitBarrierCount = 0
  let bouncedOffBarrierCount = 0
  let maxDeviationFromStart = 0
  let countFirstReturn = 0
  let countMinTime = 0
  let countMaxTime = 0

  for (let i = 0; i < iterations; i++) {
    let x = startX
    let stepsToTarget = -1
    let firstReturn = false
    let hitBarrier = false
    let maxDeviation = 0

    for (let step = 0; step < n; step++) {
      // Apply period changes
      let probLeft = initialProbLeft
      let probRight = initialProbRight
      for (let period of periods) {
        if (step >= period.from && step <= period.to) {
          probRight += period.change
          probLeft = 1 - probRight
          if (probRight < 0) probRight = 0
          if (probRight > 1) probRight = 1
        }
      }

      const rand = Math.random()
      if (rand < probLeft) x -= stepLeft
      else x += stepRight

      // Track maximum deviation from start
      maxDeviation = Math.max(maxDeviation, Math.abs(x - startX))

      // Check for first return to start
      if (!firstReturn && x === startX) {
        timesToFirstReturn.push(step + 1)
        firstReturn = true
        countFirstReturn++
      }

      // Check if target reached
      if (x === targetX && stepsToTarget === -1 && (!deferTarget || step >= deferStep)) {
        stepsToTarget = step + 1
        timesToTarget.push(stepsToTarget)
        countSuccess++
        break
      }

      // Handle reflecting barriers
      if (reflectingBarrier) {
        if (
          (positiveBarrier !== null && x === positiveBarrier) ||
          (negativeBarrier !== null && x === negativeBarrier)
        ) {
          x = startX
          bouncedOffBarrierCount++
        }
      } else {
        if (
          (positiveBarrier !== null && x === positiveBarrier) ||
          (negativeBarrier !== null && x === negativeBarrier)
        ) {
          hitBarrierCount++
          hitBarrier = true
          break
        }
      }
    }

    if (hitBarrier) {
      continue // Skip further processing if hit barrier
    }

    maxDeviationFromStart = Math.max(maxDeviationFromStart, maxDeviation)

    if (stepsToTarget === -1) {
      timesToTarget.push(n)
    }
  }

  const meanTimeToTarget = timesToTarget.reduce((a, b) => a + b, 0) / timesToTarget.length
  const minTimeToTarget = Math.min(...timesToTarget)
  const maxTimeToTarget = Math.max(...timesToTarget)
  const varianceTimeToTarget =
    timesToTarget.map((time) => Math.pow(time - meanTimeToTarget, 2)).reduce((a, b) => a + b, 0) /
    timesToTarget.length
  const stdDeviationTimeToTarget = Math.sqrt(varianceTimeToTarget)
  const coefficientOfVariation = stdDeviationTimeToTarget / meanTimeToTarget
  const probability = countSuccess / iterations
  const notReachedCount = iterations - countSuccess

  // Calculate probabilities for minimum and maximum times to target
  countMinTime = timesToTarget.filter((time) => time === minTimeToTarget).length
  countMaxTime = timesToTarget.filter((time) => time === maxTimeToTarget).length
  const probabilityMinTime = countMinTime / iterations
  const probabilityMaxTime = countMaxTime / iterations
  const probabilityFirstReturn = countFirstReturn / iterations

  simulationData.calculation.stepsToTarget = timesToTarget
  simulationData.calculation.firstReturn = timesToFirstReturn
  simulationData.calculation.countSuccess = countSuccess
  simulationData.calculation.hitBarrierCount = hitBarrierCount
  simulationData.calculation.bouncedOffBarrierCount = bouncedOffBarrierCount
  simulationData.calculation.maxDeviationFromStart = maxDeviationFromStart

  document.getElementById('output').innerHTML =
    'Среднее время достижения цели: ' +
    meanTimeToTarget.toFixed(4) +
    ' шагов.<br>' +
    'Минимальное время достижения цели: ' +
    minTimeToTarget +
    ' шагов.<br>' +
    'Максимальное время достижения цели: ' +
    maxTimeToTarget +
    ' шагов.<br>' +
    'Дисперсия: ' +
    varianceTimeToTarget.toFixed(4) +
    '.<br>' +
    'Стандартное отклонение: ' +
    stdDeviationTimeToTarget.toFixed(4) +
    '.<br>' +
    'Коэффициент вариации: ' +
    coefficientOfVariation.toFixed(4) +
    '.<br>' +
    'Вероятность достичь цели: ' +
    probability.toFixed(4) +
    '.<br>' +
    'Количество симуляций, достигших цели: ' +
    countSuccess +
    '.<br>' +
    'Количество симуляций, не достигших цели: ' +
    notReachedCount +
    '.<br>' +
    'Количество симуляций, разбившихся об барьеры: ' +
    hitBarrierCount +
    '.<br>' +
    'Количество симуляций, оттолкнувшихся от барьера: ' +
    bouncedOffBarrierCount +
    '.<br>' +
    'Вероятность достижения цели за минимальное время: ' +
    probabilityMinTime.toFixed(4) +
    '.<br>' +
    'Вероятность достижения цели за максимальное время: ' +
    probabilityMaxTime.toFixed(4) +
    '.<br>' +
    'Вероятность пересечения начальной точки: ' +
    probabilityFirstReturn.toFixed(4) +
    '.<br>' +
    'Максимальное отклонение от начальной точки: ' +
    maxDeviationFromStart +
    '.<br>'
}

function drawChart() {
  const dataType = document.getElementById('dataType').value
  const xAxis = document.getElementById('xAxis').value
  const yAxis = document.getElementById('yAxis').value

  let xData = simulationData.calculation[xAxis]
  let yData = []

  // Prepare yData based on selected yAxis (count in this case)
  let uniqueX = [...new Set(xData)]
  uniqueX.sort((a, b) => a - b)
  for (let x of uniqueX) {
    yData.push(xData.filter((value) => value === x).length)
  }

  const trace = {
    x: uniqueX,
    y: yData,
    type: 'bar'
  }

  const layout = {
    title: 'Пользовательский график',
    xaxis: { title: xAxis },
    yaxis: { title: yAxis }
  }

  Plotly.newPlot('customChart', [trace], layout)
}
</script>
