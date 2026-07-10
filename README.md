# PROJECT1-SEC-1-GooseBomb

# การแบ่งงาน

✅ **018** (20%) - Figma Design, clickTile function, checkTile function, watchEffect, togglePause function, playTime function, incrementTime function, edit template

✅ **021** (20%) - Graphic Design, Figma Design, setBombs function, changeLevel function, resetGame function, watchEffect, checkWin function, edit template

✅ **034** (20%) - Figma Design, startGame function, gooseToggle function, clickPlay function, popUp function, edit template, create animation on home page

✅ **054** (20%) - Graphic Design, checkWin function, getCellBackground function, backToHome function, playMusic function, startMusic function, musicControl function, edit template

✅ **061** (20%) - Figma Design, getCellBackground function,  getCellNumbersSize function, getCellNumbersColor function, edit template


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


--------------


# วิธีการเล่นเกม

![tutorial](https://github.com/user-attachments/assets/e58910c7-7098-4e09-b197-5a06dac4827f)

# web demo

**[Web demo](https://project-goosebomb.onrender.com)**


--------------


# คลิปสาธิต features ของแอปพลิเคชัน

🎥 https://drive.google.com/file/d/1yxK2eiTMJEtSOBDLUHEPywh6gISqx3KY/view?usp=sharing


--------------

# แรงบันดาลใจในการพัฒนาแอปพลิเคชัน และแหล่งข้อมูลอ้างอิง

## 🎨 แรงบันดาลใจในการพัฒนาแอปพลิเคชัน

โปรเจกต์นี้ได้รับแรงบันดาลใจมาจากเกม **Minesweeper** ซึ่งเป็นเกมที่ผู้เล่นต้องใช้ตรรกะในการเปิดช่องให้หมดโดยหลีกเลี่ยงระเบิด ใน Project นี้ได้ดัดแปลงแนวคิดจาก Minesweeper มาเป็นการขุดหาบ่อน้ำให้กับห่าน 🦢 โดยต้องหลีกเลี่ยง **สิ่งสกปรก** 💩 ที่ห่านทิ้งไว้แทน

## 📚 แหล่งข้อมูลอ้างอิง

- **การเขียน CSS**  
  - อ้างอิงแนวทางจาก Project รุ่นพี่ ที่เคยพัฒนาแอปพลิเคชันแนวเกมมาก่อน  
  - ใช้ ChatGPT เป็นเครื่องมือช่วยแนะนำและแก้ไข code CSS ให้เหมาะสม

- **เพลงประกอบ (Background Music)**  
  - ใช้เพลงจาก **[YouTube](https://youtu.be/lt5je1VIU48?si=Vkr7K-_SCnH6gXt5)** เพื่อเพิ่มบรรยากาศให้กับเกม 🎵  
