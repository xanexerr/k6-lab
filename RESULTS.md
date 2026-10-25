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
| A4 | Spike | 5 | 1.03 | 357.12 ms | 379.74 ms | 0% | 100% (196/196) |
| A5 | Soak | 2 | 0.98 | 373.62 ms | 1.03 s | 0% | 100% (868/868) |

## Part B

1. See the measured results table above. All five tests passed their thresholds.
2. Each VU sends a request, waits for its response, then sleeps for 1 second before the next iteration. This think time and request latency mean a VU produces fewer than one request per second, so five VUs stay below five requests per second.
3. The overall p(95) was 376.44 ms at 3 users and 377.75 ms at 5 users, so it did not meaningfully grow in these runs. The results suggest the public service stayed within its operational envelope at these loads; they do not establish behavior at higher loads.
4. Response time did not rise during the spike: the 10-second bucket averages were about 288–293 ms at 5 VUs, compared with about 291–303 ms in the first 30 seconds at 1 VU. The first bucket after dropping back to 1 VU averaged 289 ms, so no recovery delay was observed (the response time remained at baseline through the spike).
5. The first-minute response p(95) was 382.96 ms and the last-minute p(95) was 368.51 ms; the average fell from 312.68 ms to 296.18 ms. The error rate was 0% for the run. Response time and errors did not get worse over time. A multi-hour soak could reveal a memory leak.
6. p(95) describes the slow tail: 95% of requests finish within that time. An average can hide the slowest requests and outliers.
7. A lab run can differ because its network path and latency differ from the internet route, and because its hardware, configuration, and competing traffic differ from the shared public service.
8. Hundreds of users could overload a service, disrupt its other users, and consume its resources. Stress, spike, and breakpoint tests require authorization from the system owner.
9. Vague requirement: “The app should load quickly.” Testable version: “With 3 users for 30 seconds, 95% of pizza recommendations respond within 1 second, 99% within 1.5 seconds, and fewer than 1% fail.” K6 thresholds: `http_req_duration: ['p(95)<1000', 'p(99)<1500']`, `http_req_failed: ['rate<0.01']`, `checks: ['rate>0.99']`.
