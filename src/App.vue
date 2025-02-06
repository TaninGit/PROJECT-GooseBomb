<script setup>
import { ref, watchEffect } from "vue";

const cellLocation = ref([]);
const board = ref([]);
const bombLocation = ref([]);
const revealedCells = ref([]);
const flaggedCells = ref([]);
const cellNumbers = ref([]);

const timer = ref(0);
let duration = null;
let isPaused = ref(false);

let flagEnabled = false;
let gameOver = ref(false);

const bgFlagBtn = ref("bg-red-100");

const selectedLevel = ref("medium");
const levels = {
  easy: { row: 5, column: 8, bombCount: 10 },
  medium: { row: 9, column: 16, bombCount: 35 },
  hard: { row: 15, column: 25, bombCount: 75 },
};

const column = ref(levels[selectedLevel.value].column);
const row = ref(levels[selectedLevel.value].row);

let bombCount = ref(levels[selectedLevel.value].bombCount);

let isFirstEvent = true

function setBombs() {
  let bombleft = bombCount.value;
  while (bombleft > 0) {
    let r = Math.floor(Math.random() * row.value) + 1;
    let c = Math.floor(Math.random() * column.value) + 1;
    let location = `${r}-${c}`;

    if (!bombLocation.value.includes(location)) {
      bombLocation.value.push(location);
      bombleft -= 1;
    }
  }
}

function startGame() {
  setBombs();
  let newBoard = [];
  let newCells = [];
  timer.value = 0;
  
  for (let r = 1; r <= row.value; r++) {
    let rowArr = [];
    for (let c = 1; c <= column.value; c++) {
      let tile = `${r}-${c}`;
      rowArr.push(tile);
      newCells.push(tile);
    }
    newBoard.push(rowArr);
  }

  board.value = newBoard;
  cellLocation.value = newCells;
}

function togglePause() {
  if (isPaused.value) {
    isPaused.value = false;
    playTime();
  } else {
    isPaused.value = true;
    if (duration) {
      clearTimeout(duration);
      duration = null;
    }
  }
}

function playTime() {
  if (isPaused.value || gameOver.value) return;

  function incrementTime() {
    if (!gameOver.value && !isPaused.value) {
      timer.value++;
      duration = setTimeout(incrementTime, 1000);
    }
  }
  incrementTime();
}


watchEffect(() => {
  if (cellLocation.value.length === 0) {
    startGame();
  }
});

function clickTile(event) {
  if (gameOver.value || isPaused.value) return;
  const cell = event.target.id;

  if(isFirstEvent) {
    playTime();
    isFirstEvent = false;
  }

  if (flagEnabled) {
    const index = flaggedCells.value.indexOf(cell);
    if (!revealedCells.value.includes(cell)) {
      if (index !== -1) {
        flaggedCells.value.splice(index, 1);
        bombCount.value += 1
      } else if (!revealedCells.value.includes(index)) {
        flaggedCells.value.push(cell);
        bombCount.value -= 1
      }
    }
    return;
  }

  if (bombLocation.value.includes(cell)) {
    gameOver.value = true;
    isPaused.value = true;
    return;
  }

  checkTile(cell);
}

function checkTile(cell) {
  if (revealedCells.value.includes(cell)) return;
  revealedCells.value.push(cell);
  
  const [r, c] = cell.split("-").map(Number);
  let bombAround = 0;
  let neighbors = [];
  
  for (let dr = -1; dr <= 1; dr++) {
    for (let dc = -1; dc <= 1; dc++) {
      if (dr === 0 && dc === 0) continue;
      let nr = r + dr;
      let nc = c + dc;
      let neighbor = `${nr}-${nc}`;
      
      if (nr > 0 && nr <= row.value && nc > 0 && nc <= column.value) {
        neighbors.push(neighbor);
        if (bombLocation.value.includes(neighbor)) {
          bombAround++;
        }
      }
    }
  }

  cellNumbers.value[cellLocation.value.indexOf(cell)] = bombAround;
  
  if (bombAround === 0) {
    for (let neighbor of neighbors) {
      checkTile(neighbor);
    }
  }
  checkWin()
}

function setFlag() {
  flagEnabled = !flagEnabled;
  if (flagEnabled) 
    bgFlagBtn.value = "bg-red-300";
  else 
    bgFlagBtn.value = "bg-red-100";
}

function getCellBackground(cell, index) {
  const isEven = (Math.floor(index / column.value) + index % column.value) % 2 === 0;
  if (revealedCells.value.includes(cell) ) 
    return isEven ? 'bg-[#74b6dc]' : 'bg-[#9cc3da]'
  else
    return isEven ? 'bg-[#5fc794]' : 'bg-[#88deb7]'
}

function getBombBackground(cell) {
  if (gameOver.value) {
    if (bombLocation.value.includes(cell)) 
    return 'bg-[#b2373c]'
    else if (flaggedCells.value.includes(cell)) 
      return ''
  }
}

function checkWin() {
  if(revealedCells.value.length == cellLocation.value.length-bombLocation.value.length){
    alert('Congratulations! The well can now be built, free from any mess!');
  }
}

function changeLevel(level) {
  selectedLevel.value = level;
  row.value = levels[level].row;
  column.value = levels[level].column;
  bombCount.value = levels[level].bombCount;
  resetGame();
}

function resetGame() {
  cellLocation.value = [];
  board.value = [];
  bombLocation.value = [];
  revealedCells.value = [];
  flaggedCells.value = [];
  cellNumbers.value = [];
  gameOver.value = false;
  startGame();
}



</script>

<template>
  <div>
    <div>
      <h1 class="font-bold text-center text-4xl pt-10">
        Bomb Count : {{ bombCount }}
      </h1>
      <h2 class="text-center text-2xl">
        Time: {{ timer }} sec
      </h2>
    </div>
    <div class="flex justify-center mt-5">
      <select v-model="selectedLevel" @change="changeLevel(selectedLevel)" class="border-3 border-amber-800 p-2 text-xl font-bold">
        <option value="easy">Easy</option>
        <option value="medium">Medium</option>
        <option value="hard">Hard</option>
      </select>
    </div>

    <div class="w-[52rem] h-[29.25rem] m-auto mt-15" 
      :style="{
      display: 'grid',
      gridTemplateColumns: `repeat(${column}, 1fr)`,
      gridTemplateRows: `repeat(${row}, 1fr)`,
      }">
      <div
        v-for="(cell, index) in cellLocation" 
        :key="index"
        class="hover:brightness-90 flex items-center justify-center w-[100%] h-[100%]"
        :class="[
          getCellBackground(cell, index),
          getBombBackground(cell),
          { 'flagged-cell': !(gameOver && bombLocation.includes(cell)) && flaggedCells.includes(cell)}
          ]"
        :id="`${cell}`"
        v-on:click="clickTile"> 
        <span v-if="gameOver && bombLocation.includes(cell)">
          <img src="./assets/images/Character/poop.PNG" alt="Bomb" class="w-3/5 h-auto block mx-auto">
        </span>
        <span v-else-if="revealedCells.includes(cell) && !bombLocation.includes(cell)">
          {{ cellNumbers[cellLocation.indexOf(cell)] || '' }}
        </span>
      </div>
    </div>

    <div class="flex items-center justify-center pt-4">
      <button
        class="border-2 border-red-500 p-5 rounded-2xl px-14 py-5 items-center"
        v-on:click="setFlag"
        :class="bgFlagBtn">
        <img src="./assets/images/Character/RedFlag.PNG" alt="RedFlag" width="30" height="30">
      </button>
      <button class="ml-4 border-2 border-blue-500 p-5 rounded-2xl" v-on:click="togglePause">
         {{ isPaused ? '▶ Resume Game' : '⏸ Pause Game' }}
      </button>
    </div>
  </div>
</template>

<style scoped>
.flagged-cell {
  background-image: url('./assets/images/Character/RedFlag.PNG');
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
  width: 100%;
  height: 100%;
}
</style>
