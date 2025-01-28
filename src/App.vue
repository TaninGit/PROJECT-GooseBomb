<script setup>
import { ref } from 'vue'

const cellLocation = [] // เก็บพิกัดแต่ละช่อง เช่น "3-4","8-16"
const board = [] // เก็บพิกัดช่องทั้งหมด เช่น [["3-4"], ["8-16"]]
const bombLocation = []

const column = 16
const row = 9

let bombCount = 35
let cellClick = 0 // จำนวนกดพื้นที่ไม่ใช่ระเบิด
let flagEnabled = false;
let gameOver = false;

window.onload = () => {  // ฟังก์ชัน startGame จะทำงานตอนหน้าเว็บโหลดเสร็จ
    startGame();
}

function setBombs() {
  // เอาไว้ทดสอบ
  // bombLocation.push("3-4")
  // bombLocation.push("7-8")
  // bombLocation.push("3-9")
  // bombLocation.push("9-15")
  // bombLocation.push("2-9")

  let bombleft = bombCount
  while(bombleft > 0) {
    let r = Math.floor(Math.random() * row) + 1
    let c = Math.floor(Math.random() * column) + 1
    let location = `${r.toString()}-${c.toString()}`

    if(!bombLocation.includes(location)) {
      bombLocation.push(location)
      bombleft -= 1
    }
  }
}

function startGame() {
  setBombs();
  // console log เอาไว้เช็คพิกัดระเบิดที่ได้จากการสุ่มจาก setBombs
  console.log(bombLocation) 
}

function clickTile(event){

}

const bgFlagBtn = ref('bg-red-100')

function setFlag() {
  flagEnabled = !flagEnabled
  if (flagEnabled)
    bgFlagBtn.value = 'bg-red-300'
  else
    bgFlagBtn.value = 'bg-red-100'
}

</script>
 
<template>
<div>
  <div>
    <h1 class="font-bold text-center text-4xl pt-10">Bomb Count : {{ bombCount }}</h1>
  </div>
  <div class="w-[52rem] h-[29.25rem] m-auto mt-24">
    <div v-for="i in row" :key="i" class="flex"> <!-- เอาไว้วนลูปสร้าง row -->
        <div v-for="j in column"
        :key="`${i}-${j}`"
        :id="`${i}-${j}`"
        class="w-13 h-13 hover:bg-[#48bd7c]"
        :class="(i + j) % 2 === 0 ? 'bg-[#88deb4]' : 'bg-[#5fc794]'"
        :cellLocation.push(`${i}-${j}`)
        :board.push(cellLocation[i])
        v-on:click="clickTile"> <!-- clickTile function ตรงนี้ -->  
        </div>
      </div>
  </div>
  <div class="flex items-center justify-center pt-4">
    <button 
    class="border-2 border-red-500 p-5 rounded-2xl px-14 py-5 items-center" 
    v-on:click="setFlag"
    :class="bgFlagBtn">🚩</button> <!-- setFlag function ตรงนี้-->  
  </div>
</div>
</template>
 
<style scoped>

</style>