# K6 lab results

## Part 0
- Step 0.1, http_req_duration: avg=308.27ms (avg=308.27ms min=308.27ms med=308.27ms max=308.27ms p(90)=308.27ms p(95)=308.27ms because there was only 1 request)

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