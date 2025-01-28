<script setup>
import { ref } from "vue";

const cellLocation = []; // เก็บพิกัดแต่ละช่อง เช่น "3-4","8-16"
const board = []; // เก็บพิกัดช่องทั้งหมด เช่น [["3-4"], ["8-16"]]
const bombLocation = [];

const column = 16;
const row = 9;

let bombCount = 35;
let cellClick = 0; // จำนวนกดพื้นที่ไม่ใช่ระเบิด
let flagEnabled = false;
let gameOver = false;

window.onload = () => {
  // ฟังก์ชัน startGame จะทำงานตอนหน้าเว็บโหลดเสร็จ
  startGame();
};

function setBombs() {
  // เอาไว้ทดสอบ
  // bombLocation.push("3-4")
  // bombLocation.push("7-8")
  // bombLocation.push("3-9")
  // bombLocation.push("9-15")
  // bombLocation.push("2-9")

  let bombleft = bombCount;
  while (bombleft > 0) {
    let r = Math.floor(Math.random() * row) + 1;
    let c = Math.floor(Math.random() * column) + 1;
    let location = `${r.toString()}-${c.toString()}`;

    if (!bombLocation.includes(location)) {
      bombLocation.push(location);
      bombleft -= 1;
    }
  }
}

function startGame() {
  setBombs();
  for (let r = 1; r <= row; r++) {
    let row = [];
    for (let c = 1; c <= column; c++) {
      let tile = r.toString() + "-" + c.toString();
      row.push(tile);
      cellLocation.push(tile);
    }
    board.push(row);
  }
}

function clickTile(event) {
}

const bgFlagBtn = ref("bg-red-100");

function setFlag() {
  flagEnabled = !flagEnabled;
  if (flagEnabled) bgFlagBtn.value = "bg-red-300";
  else bgFlagBtn.value = "bg-red-100";
}
</script>

<template>
  <div>
    <div>
      <h1 class="font-bold text-center text-4xl pt-10">
        Bomb Count : {{ bombCount }}
      </h1>
    </div>

    <!-- grid-rows-9 grid-cols-16" สร้างตาราง 9 แถว 16 คอลั่่มน์ -->
    <div class="w-[52rem] h-[29.25rem] m-auto mt-24 grid grid-rows-9 grid-cols-16">
    <div
      v-for="(cell, index) in cellLocation" 
      :key="index"
      class="w-13 h-13 hover:bg-[#48bd7c]"
      :class="[
        'w-13 h-13',
        // สลับสีของแต่ละ tile
        (Math.floor(index / 16) + index % 16) % 2 === 0  
           ? 'bg-[#5fc794]'
           : 'bg-[#88deb7]' 
      ]"
      :id="`${cell}`"
      @click="clickTile"
    ></div>
  </div>

    <div class="flex items-center justify-center pt-4">
      <button
        class="border-2 border-red-500 p-5 rounded-2xl px-14 py-5 items-center"
        v-on:click="setFlag"
        :class="bgFlagBtn"
      >
        🚩
      </button>
      <!-- setFlag function ตรงนี้-->
    </div>
  </div>
</template>

<style scoped></style>
