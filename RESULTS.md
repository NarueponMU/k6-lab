# K6 lab results

## Part 0
- Step 0.1, http_req_duration: avg=308.27ms (avg=308.27ms min=308.27ms med=308.27ms max=308.27ms p(90)=308.27ms p(95)=308.27ms because there was only 1 request)
- Step 0.2, requests, rate and p(95), and why the rate stays below 5: 75 requests, 3.67 req/s, p(95)=392.12ms. One iteration takes about 1.36s (≈0.3s request + 1s sleep) ดังนั้นผู้ใช้งานจำลอง (Virtual User - VU) 1 คน จึงส่งคำขอได้เพียง 1 / 1.36 = 0.74 คำขอต่อวินาที และ 5 VUs × 0.74 = 3.7 คำขอต่อวินาที ซึ่งอยู่ต่ำกว่า 5
- Step 0.6, exit code of the normal run and of the run with P95=300:
- Step 0.7, rules that passed, p(95), p(99), exit code after your change:
- Step 0.8, what was wrong, and how the checks showed it:

## Part A results
| Step | Test type    | Users | Req/s | p(95) | Max | Failed | Checks |
| ---- | ------------ | ----- | ----- | ----- | --- | ------ | ------ |
| A1   | Smoke        | 1     |       |       |     |        |        |
| A2   | Average load | 3     |       |       |     |        |        |
| A3   | Stress       | 5     |       |       |     |        |        |
| A4   | Spike        | 5     |       |       |     |        |        |
| A5   | Soak         | 2     |       |       |     |        |        |

## Part B
1.
2.
3.
4.
5.
6.
7.
8.
9.