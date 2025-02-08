# PROJECT1-SEC-1-GooseBomb

## รายชื่อสมาชิก

| ลำดับ | รหัสนักศึกษา   | ชื่อ-นามสกุล                 | GitHub Username |     สัปดาห์ที่ 1     |     สัปดาห์ที่ 2   |    สัปดาห์ที่ 3    |
|-------|-----------------|---------------------|-----------------|-----------------|-----------------|-----------------|
| 1     | 66130500018     | ชนันธร ศรีวรชัย         |   mook2005      |    Figma Design  |  clickTile, checkTile function, watchEffect, edit template |  togglePause, playTime, incrementTime function, edit template  |
| 2     | 66130500021     | ชัญญา นิ่มถาวร         |    CNProud       | Graphic design  | setBombs, changeLevel, resetGame, edit template | add images folder, update resetGame, clickTile function, edit template |
| 3     | 66130500034     | ทนิน เก้าเอี้ยน          |    TaninGit      |  Figma Design   | startGame function, edit template | add background and animation on home page |
| 4     | 66130500054     | เบญญาพร เมธาวิวรรธน์กุล  |    Benyaporn54   | Graphic design  | checkWin function | update getBombBackground, getCellBackground function |
| 5     | 66130500061     | ปัณณพร อโนทัยสินทวี     |    Butt3rPann    | Figma Design    | getBombBackground, getCellBackground function, edit template | edit bomb count |


--------------


# การแบ่งงาน

✅ **018** - Figma Design, clickTile function, checkTile function, watchEffect, togglePause function, playTime function, incrementTime function, edit template

✅ **021** - Graphic design, setBombs function, changeLevel function, resetGame function, clickSetting function, edit template

✅ **034** - Figma Design, startGame function, gooseToggle function, clickPlay function, popUp function, edit template

✅ **054** - Graphic design, checkWin function,  getBombBackground function, getCellBackground function, clickHome function, musicControl function, edit template

✅ **061** - Figma Design, getBombBackground function, getCellBackground function, displayFlagClicked function, clickTutorial function, edit template


--------------


# รายละเอียด Project

## เกมขุดบ่อน้ำให้ห่านในทุ่งหญ้า (Puzzle Game)

**Project นี้** เป็นเกมแนว **puzzle** ที่ตั้งอยู่ในฉากของทุ่งหญ้าสีเขียว โดยผู้เล่นจะรับบทเป็นผู้ดูแลพื้นที่สำหรับเลี้ยงห่าน 🦢 ซึ่งมีภารกิจในการสร้างบ่อน้ำเพื่อให้ห่านมีพื้นที่สำหรับดื่มน้ำและว่ายน้ำอย่างมีความสุข

---

## 🕹️ กติกาและเป้าหมายของเกม

### เป้าหมายหลัก
- ขุดพื้นที่ในทุ่งหญ้าเพื่อค้นหาจุดสร้าง **บ่อน้ำ**  
- หลีกเลี่ยง **สิ่งสกปรก** 💩 ที่ห่านทิ้งไว้ในพื้นที่  
  *(หากผู้เล่นขุดโดนสิ่งสกปรก เกมจะจบลงทันที)*

### การเล่น
1. พื้นที่ในทุ่งหญ้าถูกแบ่งออกเป็น **ช่องสี่เหลี่ยม**  
2. ผู้เล่นสามารถเลือกขุดในแต่ละช่องได้  
   - หากขุดเจอช่องว่าง ระบบจะแสดง **ตัวเลข** ในช่องนั้น  
   - ตัวเลขดังกล่าวบอกจำนวน **สิ่งสกปรก** 💩 ที่อยู่ในช่องรอบข้าง  
3. ผู้เล่นสามารถทำ **เครื่องหมาย** 🚩 ในช่องที่คิดว่าเป็นสิ่งสกปรกได้ เพื่อป้องกันการขุดผิดพลาด  
4. หากขุดจนเจอ **บ่อน้ำ** ทั้งหมด โดยไม่ขุดโดนสิ่งสกปรก จะถือว่าภารกิจสำเร็จ!

---

## 🦢 องค์ประกอบของเกม

1. **ห่าน** 🦢  
   - เป็นตัวละครหลักที่ผู้เล่นต้องดูแล  

2. **สิ่งสกปรก** 💩  
   - อุปสรรคในเกมที่ผู้เล่นต้องหลีกเลี่ยง  

3. **บ่อน้ำ**  
   - จุดที่ผู้เล่นต้องค้นหาในเกม  

4. **ตัวเลขในช่อง**  
   - ตัวบ่งบอกจำนวน **สิ่งสกปรก** 💩 ที่อยู่ในบริเวณรอบข้าง  

5. **เครื่องหมาย** 🚩  
   - ใช้สำหรับทำสัญลักษณ์ในช่องที่คิดว่าเป็นสิ่งสกปรก เพื่อป้องกันการขุดพลาด
