<script setup>
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
</script>
 
<template>
<div>
  <div class="w-[52rem] h-[29.25rem] m-auto mt-24">
    <div v-for="i in row" :key="i" class="flex"> <!-- เอาไว้วนลูปสร้าง row -->
        <div
        v-for="j in column"
        :key="`${i}-${j}`"
        :id="`${i}-${j}`"
        class="w-13 h-13 hover:bg-[#48bd7c]"
        :class="(i + j) % 2 === 0 ? 'bg-[#88deb4]' : 'bg-[#5fc794]'"> <!-- เอาไว้สลับสีตาราง หลักการคู่คี่-->
        <!-- cellLocation.push(`${i}-${j}`) อยากใส่ค่า i-j ใน array-->
        </div> <!-- เอาไว้วานลูปสร้าง column -->
        <!-- อยากใส่ค่า ["i-j"] แต่ละรอบใน board (board.push(cellLocation))-->
      </div>
  </div>
</div>

</template>
 
<style scoped>

</style>