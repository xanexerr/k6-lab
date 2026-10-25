# K6 lab results

## Part 0

- Step 0.1, http_req_duration: 274.28 ms for the single request (avg = min = med = max = p(90) = p(95)).
- Step 0.2, requests, rate and p(95), and why the rate stays below 5: 80 requests, 3.80 requests/s, p(95) = 281.76 ms. Each iteration includes an HTTP request, its response time, and a 1 second sleep, so five VUs do not each complete five iterations per second.
