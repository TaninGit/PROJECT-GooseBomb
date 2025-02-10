<script setup>
import { ref, watch, watchEffect } from "vue";

const cellLocation = ref([]);
const board = ref([]);
const bombLocation = ref([]);
const revealedCells = ref([]);
const flaggedCells = ref([]);
const cellNumbers = ref([]);

const timer = ref(0);
let duration = null;
let isPaused = ref(false);

let flagEnabled = ref(false);
let gameOver = ref(false);

const selectedLevel = ref("medium");
const levels = {
  easy: { row: 5, column: 8, bombCount: 10 },
  medium: { row: 9, column: 16, bombCount: 35 },
  hard: { row: 15, column: 25, bombCount: 75 },
};

const column = ref(levels[selectedLevel.value].column);
const row = ref(levels[selectedLevel.value].row);

let bombCount = ref(levels[selectedLevel.value].bombCount);
const isStarted = ref(false);

let isFirstEvent = ref(true);

const gooseSrc = [
  "src/assets/images/Character/GooseFly.PNG",
  "src/assets/images/Character/GooseFlyV2.PNG",
];
const currentGoose = ref(gooseSrc[0]);

function gooseToggle() {
  setTimeout(() => {
    currentGoose.value =
      currentGoose.value === gooseSrc[0] ? gooseSrc[1] : gooseSrc[0];
    gooseToggle();
  }, 800);
}

function clickPlay() {
  isStarted.value = true;
}

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
    if (
      !isPaused.value &&
      (revealedCells.value.length ||
        flaggedCells.value.length ||
        !isFirstEvent.value)
    ) {
      timer.value++;
      duration = setTimeout(incrementTime, 1000);
    }
  }
  incrementTime();
}

watchEffect(() => {
  if (isStarted.value === false) {
    gooseToggle();
  } else if (cellLocation.value.length === 0 && isStarted.value === true) {
    startGame();
  }
});

function clickTile(event) {
  if (gameOver.value || isPaused.value) return;
  const cell = event.target.id;

  if (flagEnabled.value) {
    const index = flaggedCells.value.indexOf(cell);
    if (!revealedCells.value.includes(cell) && cell !== "") {
      if (index !== -1) {
        flaggedCells.value.splice(index, 1);
      } else {
        flaggedCells.value.push(cell);
      }
    }
    if (isFirstEvent.value) {
      playTime();
      isFirstEvent.value = false;
    }
    return;
  }

  if (bombLocation.value.includes(cell)) {
    gameOver.value = true;
    isPaused.value = true;
    return;
  }

  checkTile(cell);

  if (isFirstEvent.value) {
    playTime();
    isFirstEvent.value = false;
  }
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
  checkWin();
}

watch(
  [flaggedCells.value, revealedCells.value],
  () => {
    for (let i = flaggedCells.value.length - 1; i >= 0; i--) {
      if (revealedCells.value.includes(flaggedCells.value[i]))
        flaggedCells.value.splice(i, 1);
    }
    bombCount.value =
      levels[selectedLevel.value].bombCount - flaggedCells.value.length;
  },
  { immediate: true }
);

function setFlag() {
  flagEnabled.value = !flagEnabled.value;
}

function getCellBackground(cell, index) {
  const isEven =
    (Math.floor(index / column.value) + (index % column.value)) % 2 === 0;
  if (revealedCells.value.includes(cell))
    return isEven ? "bg-[#74b6dc]" : "bg-[#9cc3da]";
  else return isEven ? "bg-[#5fc794]" : "bg-[#88deb7]";
}

function checkWin() {
  if (
    revealedCells.value.length ==
    cellLocation.value.length - bombLocation.value.length
  ) {
    alert("Congratulations! The well can now be built, free from any mess!");
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
  isPaused.value = false;
  clearTimeout(duration);
  duration = null;
  timer.value = 0;
  isFirstEvent.value = true;
  bombCount.value = levels[selectedLevel.value].bombCount;
  startGame();
}

const popupStyle = ref("display:none;");
function openPopUp() {
  popupStyle.value = "display:block;";
}
function closePopUp() {
  popupStyle.value = "display:none;";
}
</script>

<template>
  <div
    v-show="!isStarted"
    class="flex flex-col items-center pt-15 w-screen h-screen bg-cover bg-center bg-[url(/src/assets/images/Background.PNG)]"
  >
    <img
      class="animate-bounce w-200"
      src="./assets/images/topic.png"
      alt="topicGooseBomb"
    />
    <div class="flex flex-row space-x-10">
      <img
        @click="clickPlay"
        src="./assets/images/Button/play_button.PNG"
        alt="play button"
        class="w-40 pt-5"
      />
      <img
        @click="openPopUp"
        src="./assets/images/Button/info_button.PNG"
        alt="how to play button"
        class="w-40 pt-5"
      />
      <div :style="popupStyle" class="absolute">
        <div
          class="bg-black opacity-60 h-screen w-screen fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2"
        ></div>
        <div
          class="bg-[#643B35] w-[1000px] h-[650px] fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 z-1"
        >
          <div
            class="bg-[#FDF7F4] w-[950px] h-[600px] fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 shadow-[inset_0px_0px_10px_5px_rgba(0,0,0,0.3)] flex flex-col items-center p-8"
          >
            <img
              @click="closePopUp()"
              src="./assets/images/Button/cross_button.PNG"
              alt="close how to play"
              class="w-20 mr-7 mt-3 absolute top-2 left-6"
            />
            <img
              src="./assets/images/Topic/tutorial_topic.png"
              alt="tutorial_topic"
              class="h-18"
            />
            <div
              class="text-xl bg-amber-600 w-[900px] h-[500px] mt-7 overflow-auto"
            >
              <div class="flex flex-wrap">
                <div class="bg-blue-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_1.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-red-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_2.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-green-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_3.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-yellow-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_4.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-purple-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_5.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-pink-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_6.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-gray-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_7.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-orange-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_8.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-teal-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_9.png"
                    alt=""
                    class="w-46"
                  />
                </div>
                <div class="bg-indigo-500" :class="'tutorial_step'">
                  <img
                    src="./assets/images/Tutorial/step_10.png"
                    alt=""
                    class="w-46"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="flex">
      <img
        :src="currentGoose"
        alt="gooseFly"
        class="w-[200px] mr-150 -rotate-20 animate-wiggle"
      />
      <img
        src="./assets/images/Character/GooseFrontView.PNG"
        alt="gooseHalt"
        class="w-[120px] animate-wiggle"
      />
    </div>
  </div>

  <div
    v-show="isStarted"
    class="w-screen h-screen bg-cover bg-center bg-[url(/src/assets/images/Background.PNG)] flex items-center justify-center"
  >
    <img
      @click="
        togglePause();
        openPopUp();
      "
      src="./assets/images/Button/pause_button.PNG"
      alt="Pause"
      class="w-25 mr-7 mt-3 absolute top-0 right-0"
    />

    <div v-bind:style="popupStyle" class="absolute z-50">
      <div
        class="bg-black opacity-60 h-screen w-screen fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2"
      ></div>
      <div
        class="bg-white w-[600px] h-[550px] fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2"
      >
        <button
          class="border border-red-500"
          @click="
            togglePause();
            closePopUp();
          "
        >
          Close it
        </button>
        <!-- text here -->
      </div>
    </div>

    <div class="bg-[#643B35] w-fit p-5">
      <div
        class="flex flex-col items-center bg-[#AE8774] w-fit p-7 shadow-[inset_0px_0px_10px_5px_rgba(0,0,0,0.3)]"
      >
        <div
          class="font-primary bg-[#74B6DC] w-[55rem] h-[8rem] m-auto drop-shadow-lg flex justify-center items-center"
        >
          <div class="flex justify-center items-center absolute left-5">
            <select
              v-model="selectedLevel"
              @change="changeLevel(selectedLevel)"
              class="border-3 border-amber-800 p-2 text-xl font-bold font-primary bg-[#FFF4DA] text-[#643B35]"
            >
              <option value="easy">Easy</option>
              <option value="medium">Medium</option>
              <option value="hard">Hard</option>
            </select>
          </div>
          <div class="flex flex-row justify-center items-center space-x-15">
            <div class="flex flex-row items-center space-x-5">
              <img
                src="./assets/images/Character/GooseRedFlag.PNG"
                alt="GooseRedFlag"
                class="w-17"
              />
              <h1 class="text-7xl text-[#B2373C]" :class="'text_stroke'">
                {{ bombCount }}
              </h1>
            </div>
            <div class="flex flex-row items-center space-x-5">
              <img
                src="./assets/images/Character/Clock.PNG"
                alt="Clock"
                class="h-19"
              />
              <h2 class="text-7xl text-[#614224]" :class="'text_stroke'">
                {{ timer }}
              </h2>
            </div>
          </div>
          <div class="flex justify-center items-center absolute right-5">
            <button
              class="border-3 border-red-700 p-5 px-7 py-2 items-center"
              v-on:click="setFlag"
              :class="flagEnabled ? 'bg-red-300' : 'bg-red-100'"
            >
              <img
                src="./assets/images/Character/RedFlag.PNG"
                alt="RedFlag"
                width="25"
                height="30"
              />
            </button>
          </div>
        </div>
        <div
          class="w-[55rem] h-[31rem] m-auto"
          :style="{
            display: 'grid',
            gridTemplateColumns: `repeat(${column}, 1fr)`,
            gridTemplateRows: `repeat(${row}, 1fr)`,
          }"
        >
          <div
            v-for="(cell, index) in cellLocation"
            :key="index"
            class="hover:brightness-90 flex items-center justify-center w-[100%] h-[100%]"
            :class="
              (getCellBackground(cell, index),
              { 'bomb-cell': gameOver && bombLocation.includes(cell) },
              {
                'flagged-cell':
                  flaggedCells.includes(cell) && !revealedCells.includes(cell),
              })
            "
            :id="`${cell}`"
            v-on:click="clickTile"
          >
            <span
              v-if="revealedCells.includes(cell)"
              class="font-secondary"
              :class="
                ('text_stroke',
                { isOne: cellNumbers[cellLocation.indexOf(cell)] === 1 },
                { isTwo: cellNumbers[cellLocation.indexOf(cell)] === 2 },
                { isThree: cellNumbers[cellLocation.indexOf(cell)] === 3 },
                { isFour: cellNumbers[cellLocation.indexOf(cell)] === 4 },
                { isFive: cellNumbers[cellLocation.indexOf(cell)] === 5 },
                { 'text-7xl': selectedLevel === 'easy' },
                { 'text-4xl': selectedLevel === 'medium' },
                { 'text-xl': selectedLevel === 'hard' })
              "
            >
              {{ cellNumbers[cellLocation.indexOf(cell)] || "" }}
            </span>
          </div>
        </div>
      </div>
    </div>

    <div class="flex items-center justify-center pt-4">
      <button
        class="border-2 border-green-600 p-3 rounded-2xl px-9 py-5 items-center ml-4"
        @click="resetGame"
      >
        🔄Restart Game
      </button>
    </div>
  </div>
</template>

<style scoped>
.tutorial_step {
  width: 50%;
  height: 225px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.flagged-cell {
  background-image: url("./assets/images/Character/RedFlag.PNG");
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
  background-size: 55%;
}

.bomb-cell {
  background-color: #b2373c;
  background-image: url("./assets/images/Character/poop.PNG");
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
  background-size: 65%;
}

.animate-bounce {
  animation-timeline: auto;
  animation-range-start: normal;
  animation-range-end: normal;
  animation: 3s ease 0s infinite normal none running bounce;
}

@keyframes wiggle {
  0% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(10deg);
  }
  100% {
    transform: rotate(0deg);
  }
}

@layer utilities {
  .animate-wiggle {
    animation: wiggle 1s ease-in-out infinite;
  }
}

.text_stroke {
  text-shadow: 1px 1px 0px #fff4da, -1px -1px 0px #fff4da, 1px -1px 0px #fff4da,
    -1px 1px 0px #fff4da;
}

.isOne {
  color: #fcf358;
}

.isTwo {
  color: #ff8f5f;
}

.isThree {
  color: #f88bbe;
}

.isFour {
  color: #b2373c;
}

.isFive {
  color: #614224;
}
</style>
