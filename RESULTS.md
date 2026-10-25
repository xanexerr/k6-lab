# K6 lab results

## Part 0

- Step 0.1, http_req_duration: 274.28 ms for the single request (avg = min = med = max = p(90) = p(95)).
- Step 0.2, requests, rate and p(95), and why the rate stays below 5: 80 requests, 3.80 requests/s, p(95) = 281.76 ms. Each iteration includes an HTTP request, its response time, and a 1 second sleep, so five VUs do not each complete five iterations per second.
- Step 0.3: exactly 20 iterations and 20 requests; p(95) = 276.27 ms.
- Step 0.4: all 3 checks passed; the response was 200, included a pizza name, and every ingredient was vegetarian.
- Step 0.5: the missing-token request returned 401 and passed its check; http_req_failed was 0% because 401 was declared an expected status.
- Step 0.6, exit code of the normal run and of the run with P95=300: normal run exit code 0 (p(95) = 389.38 ms); P95=300 exit code 99 (measured p(95) = 388.34 ms in that run).
- Step 0.7, rules that passed, p(95), p(99), exit code after your change: the original p(95) < 1000 ms, p(99) < 1500 ms, failure rate < 1%, and checks > 99% all passed (measured p(95) = 375.76 ms, p(99) = 379.47 ms). Changing p(95) to < 1 ms failed the threshold and returned exit code 99.
- Step 0.8, what was wrong, and how the checks showed it: the request sent lowercase `pepperoni` and `knife`, while the catalog uses `Pepperoni` and `Knife`; the case-sensitive exclusions were ignored. The initial 5-VU run passed status checks but failed 39 of 150 restriction checks (12 pepperoni results and 27 knife results). After correcting the values, all 225 checks passed. Repeated requests exposed the random unwanted results that a single request could miss.

## Part A results

| Step | Test type | Users | Req/s | p(95) | Max | Failed | Checks |
| ---- | ------------ | ----- | ----- | ----- | --- | ------ | ------ |
| A1 | Smoke | 1 | 0.50 | 335.78 ms | 370.25 ms | 0% | 100% (20/20) |
| A2 | Average load | 3 | 1.30 | 376.44 ms | 392.38 ms | 0% | 100% (320/320) |
| A3 | Stress | 5 | 2.24 | 377.75 ms | 398.56 ms | 0% | 100% (544/544) |
