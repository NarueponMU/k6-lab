# K6 lab results

## Part 0
- Step 0.1, http_req_duration: avg=308.27ms min=308.27ms med=308.27ms max=308.27ms p(90)=308.27ms p(95)=308.27ms because there was only 1 request

- Step 0.2, requests, rate and p(95), and why the rate stays below 5: 75 requests, 3.67 req/s, p(95)=392.12ms. One iteration takes about 1.36s (≈0.3s request + 1s sleep) ดังนั้นผู้ใช้งานจำลอง (Virtual User - VU) 1 คน จึงส่งคำขอได้เพียง 1 / 1.36 = 0.74 คำขอต่อวินาที และ 5 VUs × 0.74 = 3.7 คำขอต่อวินาที ซึ่งอยู่ต่ำกว่า 5

- Step 0.3, 5 VUs and 20 iterations: exactly 20 iterations and 20 requests, finished in 5.9s, p(95)=349.89ms. A duration gives load over time, while a number of iterations gives an exact amount of work.

- Step 0.6, exit code of the normal run and of the run with P95=300: normal run = 0 (p(95)=626.37ms < 1500), P95=300 run = 99 (p(95)=574.94ms > 300, threshold crossed)

- Step 0.7, rules that passed, p(95), p(99), exit code after your change: All rules passed: p(95)<1000 (p(95)=410.28ms), p(99)<1500 (p(99)=420.69ms), http_req_failed rate<0.01 (0.00%), exit code 0. After changing p(95)<1000 to p(95)<300, the p(95) rule failed (p(95)=427.81ms) and the exit code was 99.

- Step 0.8, what was wrong, and how the checks showed it: The request excluded 'pepperoni' and 'knife' in lowercase, but the API names are 'Pepperoni' and 'Knife' (seen with explore.js), so the API excluded nothing and returned random pizzas. Under 5 VUs for 20s, 'no pepperoni' failed 3/75 (96% passed) and 'no knife needed' failed 27/75 (64% passed), while status was always 200. One manual request would probably return a pizza that looks correct, but 75 checked requests showed the wrong answers as a failure rate. After changing the names to 'Pepperoni' and 'Knife', every check passed.

## Part A results
| Step | Test type    | Users | Req/s | p(95) | Max | Failed | Checks |
| ---- | ------------ | ----- | ----- | ----- | --- | ------ | ------ |
| A1   | Smoke        | 1     | 0.51/s| 839ms |988ms| 0.00%  | 100.00%|
| A2   | Average load | 3     | 1.28/s| 412ms |567ms| 0.00%  | 100.00%|
| A3   | Stress       | 5     | 2.21/s| 375ms |1.23s| 0.00%  | 100.00%|
| A4   | Spike        | 5     | 1.01/s| 420ms |466ms| 0.00%  | 100.00%|
| A5   | Soak         | 2     | 0.98/s| 436ms |756ms| 0.00%  | 100.00%|

## Part B Reflection
### 1. Fill in the results table for Steps A1 to A5.
| Step | Test type    | Users | Req/s | p(95) | Max | Failed | Checks |
| ---- | ------------ | ----- | ----- | ----- | --- | ------ | ------ |
| A1   | Smoke        | 1     | 0.51/s| 839ms |988ms| 0.00%  | 100.00%|
| A2   | Average load | 3     | 1.28/s| 412ms |567ms| 0.00%  | 100.00%|
| A3   | Stress       | 5     | 2.21/s| 375ms |1.23s| 0.00%  | 100.00%|
| A4   | Spike        | 5     | 1.01/s| 420ms |466ms| 0.00%  | 100.00%|
| A5   | Soak         | 2     | 0.98/s| 436ms |756ms| 0.00%  | 100.00%|

**ตอบ:** ทุกเทสต์ผ่าน threshold ครบ โดยมี failed requests 0.00% และ checks 100.00% ส่วน p(95) ของ Step A2 ถึง A5 อยู่ระหว่าง 375 ms ถึง 436 ms ที่ smoke test มี p(95) สูงกว่า (839 ms) เพราะมีแค่ 18 requests ทำให้ request ที่ช้าเพียงตัวเดียวก็เป็นตัวกำหนดค่า p(95) ได้

### 2. In Step 0.2, why was the request rate below 5 requests per second with 5 virtual users?
**ตอบ:** ใน Step 0.2 มี 5 VUs ส่งไป 75 requests ที่ 3.67 req/s แต่ละ VU จะส่ง request ถัดไปได้ก็ต่อเมื่อทำ iteration ปัจจุบันเสร็จ และ 1 iteration ใช้เวลาเฉลี่ยประมาณ 1.36 s (request ประมาณ 0.3 s และ sleep 1 s) ดังนั้น 1 VU ส่งได้แค่ 1 / 1.36 ≈ 0.74 req/s และ 5 VUs ส่งได้ 5 × 0.74 ≈ 3.7 req/s ซึ่งต่ำกว่า 5 rate จะถึง 5 req/s ได้ก็ต่อเมื่อ 1 iteration ใช้เวลาไม่เกิน 1 s ซึ่งเป็นไปไม่ได้ เพราะแค่ sleep อย่างเดียวก็ใช้ 1 s แล้ว

### 3. Did the response time grow from the average-load test to the stress test? Explain your answer with the operational envelope.
**ตอบ:** ไม่เพิ่มขึ้น p(95) ของ average-load test (3 users) อยู่ที่ 412 ms และของ stress test (5 users) อยู่ที่ 375 ms ขณะที่ request rate เพิ่มขึ้นตามจำนวน users (1.28 → 2.21 req/s เพิ่มประมาณ ×1.7 เท่ากับจำนวน users ที่เพิ่ม ×1.7) และไม่มี request ที่ fail เลย แปลว่า 5 users ยังอยู่ลึกภายใน operational envelope ของ QuickPizza ระบบยังมี capacity เหลืออีกมาก โหลดที่เพิ่มขึ้นจึงไม่มีผลที่มองเห็นได้ ส่วนต่างประมาณ 40 ms เป็นแค่ noise ปกติจากอินเทอร์เน็ตและจากผู้ใช้คนอื่นบนเว็บที่ใช้ร่วมกัน ถ้าจะเห็น response time เพิ่มขึ้นจริง ต้องทดสอบใกล้หรือเกินขอบของ envelope ซึ่งห้ามทำกับเว็บสาธารณะ

### 4. In the spike test, how long did the response time take to return to normal after the spike?
**ตอบ:** Response time แทบไม่เพิ่มขึ้นในช่วง spike ในรายงาน p(95) ขึ้นสูงสุดประมาณ 435 ms ที่ 23:01:50 ตอนที่มี 5 users และลดกลับมาที่ประมาณ 385 ms ตอน 23:02:00 คือภายในประมาณ 10 วินาที (1 จุดบนกราฟ) หลัง spike จบ ค่าสูงสุดนี้สูงกว่าจุดสูงสุดก่อนและหลัง spike (ประมาณ 425 ms ตอนมี 1 user) แค่ 10–50 ms ซึ่งยังอยู่ในระดับ noise ปกติ spike แค่ 5 users เบาเกินกว่าจะ stress QuickPizza ได้ จึงแทบไม่มีอะไรต้อง recover

### 5. In the soak test, did the response time or the error rate get worse over time? Name one kind of defect that a soak test of several hours could reveal.
**ตอบ:** ไม่แย่ลง p(95) อยู่ระหว่างประมาณ 350 ms ถึง 450 ms ตั้งแต่ช่วงนาทีแรกจนถึงช่วงนาทีสุดท้าย และ failure rate เป็น 0.00% ตลอด 11 นาที จุดที่สูงจุดเดียว (ประมาณ 650 ms) อยู่ตอนเริ่มต้นช่วง ramp-up ซึ่งมี request น้อยมากในช่วงแรก soak test ที่รันหลายชั่วโมงอาจเจอ memory leak ได้ คือ server จองหน่วยความจำเล็กน้อยทุกครั้งที่รับ request แล้วไม่คืน ทำให้ memory ค่อยๆ โตขึ้นจน server ช้าลงหรือ crash ปัญหานี้ต้องใช้เวลาหลายชั่วโมงกว่าจะปรากฏ เทสต์ 10 นาทีกับ 2 users จึงหาไม่เจอ

### 6. Why do the thresholds in this lab use p(95) rather than the average?
**ตอบ:** Average ซ่อนผู้ใช้ที่ช้าที่สุดไว้ เช่น ใน smoke test ค่า average อยู่ที่ 404 ms แต่ p(95) อยู่ที่ 839 ms แปลว่ามีผู้ใช้บางคนรอนานเป็นสองเท่าของที่ average บอก นอกจากนี้ request ที่ช้ามากไม่กี่ตัวยังถูกเฉลี่ยกลบด้วย request ที่เร็วจำนวนมาก ทำให้ average ดูดีทั้งที่ผู้ใช้จริงกำลังเจอปัญหา ส่วน p(95) หมายถึง 95% ของ requests เร็วกว่าค่านี้ และมีแค่ 1 ใน 20 ที่ช้ากว่า จึงสะท้อนประสบการณ์ของผู้ใช้เกือบทุกคน requirements และ thresholds จึงใช้ percentile แทน average

### 7. Your results come from a shared public site reached over the internet. Give two reasons why the same tests against a system in a lab could give different numbers.
**ตอบ:** (1) Network: request ของเราวิ่งผ่านอินเทอร์เน็ตจากเครือข่ายที่บ้าน latency จึงเปลี่ยนไปตามเส้นทาง คุณภาพ Wi-Fi และ traffic บนอินเทอร์เน็ต ในแล็บ load generator กับระบบที่ทดสอบอยู่บนเครือข่ายเดียวกันที่เร็วและเสถียร response time จึงต่ำกว่าและนิ่งกว่า (2) Shared system: QuickPizza มีคนทั่วโลกใช้งานพร้อมกัน traffic ของคนอื่นจึงปนกับของเราและเปลี่ยนไปทุกนาที ในแล็บระบบรองรับแค่เทสต์ของเรา ผลจึงขึ้นกับโหลดของเราเท่านั้นและทำซ้ำได้

### 8. Why must you never run a stress, spike or breakpoint test with hundreds of users against a site that you do not own?
**ตอบ:** Stress, spike หรือ breakpoint test ที่ใช้หลายร้อย users ส่ง traffic แบบเดียวกับ denial-of-service (DoS) attack อาจทำให้เว็บช้าลงหรือล่มสำหรับผู้ใช้จริง และทำให้เจ้าของเว็บเสียค่า server และ bandwidth ถ้าไม่มีการอนุญาตเป็นลายลักษณ์อักษร อาจผิด terms of service ของเว็บและผิดกฎหมาย (เช่น พ.ร.บ.คอมพิวเตอร์ของไทย) และระบบ security ของเจ้าของเว็บอาจ block เรา นอกจากนี้ผลที่ได้ก็เชื่อถือไม่ได้ เพราะเราไม่รู้ว่ามีอะไรรันอยู่บนระบบนั้นอีกบ้าง การทดสอบแบบนี้จึงควรทำกับระบบของเราเองหรือเมื่อได้รับอนุญาตเป็นลายลักษณ์อักษรเท่านั้น

### 9. Write one vague performance requirement for an app you use every day. Rewrite it as a testable requirement, and show the K6 thresholds that check it.
**ตอบ:** Vague requirement: "แอปสั่งอาหารควรโหลดรายชื่อร้านอาหารได้เร็ว"
   Testable requirement: "เมื่อมีผู้ใช้ 50 คนใช้งานพร้อมกันเป็นเวลา 5 นาที API รายชื่อร้านอาหารต้องตอบ 95% ของ requests ภายใน 800 ms และ 99% ภายใน 1.5 s และมี requests ที่ fail น้อยกว่า 0.5%"
   K6 options และ thresholds ที่ใช้ตรวจ:

```javascript
export const options = {
  vus: 50,
  duration: '5m',
  thresholds: {
    http_req_duration: ['p(95)<800', 'p(99)<1500'],
    http_req_failed: ['rate<0.005'],
  },
};
```