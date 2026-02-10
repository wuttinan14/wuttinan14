# STM32 + Rotary Encoder (E38S6G5-600B-G24N)

ตัวอย่างนี้เป็นโค้ดสำหรับอ่านค่า **incremental rotary encoder** รุ่น **E38S6G5-600B-G24N** ด้วย STM32 โดยใช้โหมด **Timer Encoder Interface** ของ HAL

## แนวคิด
- ต่อสัญญาณ `A` และ `B` เข้าขา `TIMx_CH1` และ `TIMx_CH2`
- เปิด `TIM` ในโหมด Encoder แล้วอ่านค่าตัวนับจาก `__HAL_TIM_GET_COUNTER()`
- คำนวณ
  - ตำแหน่งสะสม (counts)
  - มุม (degrees)
  - ความเร็ว (RPM)

## ข้อควรระวังเรื่องแรงดัน
Encoder รุ่นนี้มักใช้ไฟเลี้ยงได้สูง (เช่น 5–24V) และบางรุ่นเป็นเอาต์พุตแบบ open collector / NPN

- ห้ามป้อนสัญญาณเกินแรงดันที่ขา GPIO ของ STM32
- ถ้าเอาต์พุต encoder เป็น 24V ให้ใช้วงจรแปลงระดับแรงดันก่อน (เช่น optocoupler หรือ transistor level shifting)
- ถ้าใช้ open collector ต้องมี pull-up ที่แรงดันที่ปลอดภัยต่อ STM32 (เช่น 3.3V)

## การนับ PPR/CPR
รุ่น 600B โดยทั่วไปหมายถึง 600 pulse/rev (หนึ่งช่อง)
เมื่ออ่านแบบ quadrature x4:

- `CPR_X4 = 600 * 4 = 2400 counts/rev`

สามารถปรับได้ใน `ENCODER_PPR` และ `QUAD_FACTOR`

## ไฟล์ตัวอย่าง
- `stm32_encoder_example/main.c` : โค้ดตัวอย่าง HAL พร้อมฟังก์ชันคำนวณมุมและ RPM
