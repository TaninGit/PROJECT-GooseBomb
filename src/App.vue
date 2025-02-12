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
const musicPlayer = ref("");
const onMusic = ref(true);

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

function backToHome() {
  isStarted.value = false;
}

function playMusic() {
  onMusic.value = !onMusic.value;
  if (onMusic.value) musicPlayer.value.play();
  else musicPlayer.value.pause();
}
function startMusic() {
  if (onMusic.value) musicPlayer.value.play();
}
</script>

<template>
  <div @click="startMusic">
    <audio controls class="hidden" ref="musicPlayer" autoplay loop>
      <source src="./assets/BGM.mp3" type="audio/mp3" />
      <p>
        Your browser doesn't support this audio file. Here is a
        <a href="BGM.mp3">link to the audio</a> instead.
      </p>
    </audio>
    <div
      v-show="!isStarted"
      class="flex flex-col items-center pt-15 w-screen h-screen bg-cover bg-center bg-[url(/src/assets/images/Background.PNG)]"
    >
      <img
        class="animate-bounce w-200"
        src="./assets/images/Topic/GooseBomb_topic.png"
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
              class="bg-[#FDF7F4] w-[950px] h-[600px] fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 shadow-[inset_0px_0px_10px_5px_rgba(0,0,0,0.3)] flex flex-col items-center p-6"
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
                class="text-xl w-[900px] h-[500px] mt-7 overflow-auto custom-scrollbar"
              >
                <div class="flex flex-wrap">
                  <div class="tutorial_step_box">
                    <div class="relative w-[565px]">
                      <img
                        src="./assets/images/Character/GooseIcon.PNG"
                        alt="GooseIcon"
                        class="w-13 h-13 absolute -top-5.5 -left-2.5 -rotate-20 z-10"
                      />
                      <img
                        src="./assets/images/Tutorial/step_1.png"
                        alt="tutorial_step_1"
                        class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                      />
                    </div>
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 1 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >เป้าหมาย</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        เป้าหมายของเกม Goose Bomb
                        คือการขุดพื้นที่เพื่อสร้างบ่อน้ำ
                        โดยหลีกเลี่ยงอึของน้องห่าน ด้วยการปักธงไว้นั่นเอง
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_2.png"
                      alt="tutorial_step_2"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 2 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >จำนวนอึ</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        เมื่อเริ่มเกมแถบข้างบนจะบอกถึงจำนวนอึทั้งหมดของน้องห่านในกระดาน
                        โดยจะเปลี่ยนไปตามเลเวลที่เลือก และจำนวนจะลดลงเรื่อยๆ
                        เมื่อเราปักธง
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_3.png"
                      alt="tutorial_step_3"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 3 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >ขุดช่อง</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        เมื่อต้องการขุดช่องสามารถกดเลือกช่องในตารางที่จะขุดได้เลย
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_4.png"
                      alt="tutorial_step_4"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 4 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >โหมดปักธง</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        หากต้องการเปลี่ยนจากโหมดขุดช่องเป็นโหมดปักธงให้กดปุ่มธงด้านขวาบนของตาราง
                        พื้นหลังของปุ่มก็จะเปลี่ยนเป็นสีแดงนั่นเอง
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_5.png"
                      alt="tutorial_step_5"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 5 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >ปัก/ถอนธง</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        เมื่ออยู่ในโหมดปักธงสามารถกดช่องที่ต้องการจะปักธงได้เลยและหากต้องการถอนธงออกให้กดไปที่ช่องที่ปักธงไว้อีกครั้ง
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_6.png"
                      alt="tutorial_step_6"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 6 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >เข้าโหมดขุด</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        หากต้องการกลับไปที่โหมดขุดให้กดปุ่มธงด้านขวาบนอีกครั้ง
                        เมื่อพื้นหลังกลับไปเป็นสีเดิมแล้วก็สามารถขุดช่องได้ตามปกติเลย
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_7.png"
                      alt="tutorial_step_7"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 7 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >อึใกล้ฉัน</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        เมื่อเริ่มขุดตัวเลขจะบ่งบอกถึงจำนวนอึของน้องห่าน
                        ที่อยู่ในรัศมีของตัวมัน 1 บล็อค
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <div class="relative w-[565px]">
                      <img
                        src="./assets/images/Character/poop.PNG"
                        alt="GooseIcon"
                        class="w-14 h-14 absolute -top-6.5 -left-5 -rotate-18 z-10"
                      />
                      <img
                        src="./assets/images/Tutorial/step_8.png"
                        alt="tutorial_step_8"
                        class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                      />
                    </div>
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 8 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >ตัวอย่าง</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        ในตัวอย่างนี้จะสังเกตได้ว่าพื้นหญ้ามุมซ้ายล่างจะเหลืออยู่ช่องเดียวทำให้มั่นใจได้เลยว่าตรงนั้นเป็นอึของน้องแน่ๆ
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_9.png"
                      alt="tutorial_step_9"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 9 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >ตัวอย่าง</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        เมื่อคุณรู้ตำแหน่งของอึน้องแล้วก็สามารถกดปักธงเพื่อทำสัญลักษณ์ไว้ได้เลย
                      </p>
                    </div>
                  </div>
                  <div class="tutorial_step_box">
                    <img
                      src="./assets/images/Tutorial/step_10.png"
                      alt="tutorial_step_10"
                      class="w-42 h-42 mr-1.5 border-solid border-4 border-[#643B35]"
                    />
                    <div class="flex flex-col px-3 gap-1">
                      <div>
                        <span class="font-primary tutorial_step_text"
                          >Step 10 :
                        </span>
                        <span class="font-thai font-black tutorial_step_text"
                          >แพ้/ชนะ</span
                        >
                      </div>
                      <p class="font-thai text-lg">
                        เกมจะจบทนทีเมื่อไปขุดช่องที่เป็นอึ
                        และจะชนะก็ต่อเมื่อเราทำการขุดบ่อน้ำทั้งหมดโดยไม่โดนอึของน้องห่านนั่นเอง
                      </p>
                    </div>
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
          class="bg-[#fff4de] border-[#643B35] border-25 rounded-md w-[600px] h-[550px] shadow-[inset_0px_0px_10px_5px_rgba(0,0,0,0.3)] fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 z-10"
        >
          <img
            src="./assets/images/musicIcon.png"
            alt="music icon"
            class="absolute w-20 top-1/3 left-0 translate-x-1/4"
          />
          <h1
            class="absolute top-1/3 left-1/2 -translate-x-12/13 text-7xl font-bold font-primary text-[#643B35] z-60"
          >
            Music
          </h1>
          <input
            @click="playMusic"
            v-model="onMusic"
            type="checkbox"
            checked="checked"
            class="toggle toggle-xl scale-150 border-[#fff4de] bg-[#C9A480] text-[#fff4de] checked:bg-[#643B35] checked:text-[#fff4de] absolute top-1/3 right-1/4 translate-x-9/11 translate-y-9/11"
          />
          <img
            src="./assets/images/Topic/pause_topic.png"
            alt="pause"
            class="absolute top-[45%] left-1/2 -translate-x-1/2 -translate-y-13/7 z-60"
          />
          <img
            @click="
              backToHome();
              closePopUp();
            "
            src="./assets/images/Button/home_button.PNG"
            alt="home button"
            class="absolute w-30 bottom-[10%] left-0 translate-x-1/4 z-60"
          />
          <img
            @click="
              togglePause();
              closePopUp();
            "
            src="./assets/images/Button/play_button.PNG"
            alt="play button"
            class="absolute w-30 bottom-[10%] left-1/2 -translate-x-1/2 z-60"
          />
          <img
            @click="
              resetGame();
              closePopUp();
            "
            src="./assets/images/Button/reset_button.PNG"
            alt="restart button"
            class="absolute w-30 bottom-[10%] right-0 -translate-x-1/4 z-60"
          />
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
                getCellBackground(cell, index),
                { 'bomb-cell': gameOver && bombLocation.includes(cell) },
                {
                  'flagged-cell':
                    flaggedCells.includes(cell) &&
                    !revealedCells.includes(cell),
                }
              "
              :id="`${cell}`"
              v-on:click="clickTile"
            >
              <span
                v-if="revealedCells.includes(cell)"
                class="font-secondary"
                :class="
                  'text_stroke',
                  { isOne: cellNumbers[cellLocation.indexOf(cell)] === 1 },
                  { isTwo: cellNumbers[cellLocation.indexOf(cell)] === 2 },
                  { isThree: cellNumbers[cellLocation.indexOf(cell)] === 3 },
                  { isFour: cellNumbers[cellLocation.indexOf(cell)] === 4 },
                  { isFive: cellNumbers[cellLocation.indexOf(cell)] === 5 },
                  { 'text-7xl': selectedLevel === 'easy' },
                  { 'text-4xl': selectedLevel === 'medium' },
                  { 'text-xl': selectedLevel === 'hard' }
                "
              >
                {{ cellNumbers[cellLocation.indexOf(cell)] || "" }}
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.custom-scrollbar {
  scrollbar-width: thin;
  scrollbar-color: #945850 #fff4da;
}

.tutorial_step_box {
  padding-left: 0.5em;
  width: 50%;
  height: 225px;
  display: flex;
  justify-content: center;
  padding-top: 22px;
}

.tutorial_step_text {
  font-size: 1.7rem;
  color: #ff8f5f;
  text-shadow: 2px 2px 0px #fff4da, -2px -2px 0px #fff4da, 2px -2px 0px #fff4da,
    -2px 2px 0px #fff4da;
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
