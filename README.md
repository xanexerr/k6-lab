# k6-lab

Grafana k6 performance-testing exercises for QuickPizza. Tests target the public practice service at `https://quickpizza.grafana.com` and use only the low user counts specified in the lab.

## Run

Install Grafana k6, then run a script from this directory:

```powershell
k6 run 01-first-test.js
k6 run --vus 5 --duration 20s 01-first-test.js
k6 run a1-smoke.js
k6 run --out web-dashboard=export=reports/a2-average.html a2-average.js
```

The `a3-stress.js`, `a4-spike.js`, and `a5-soak.js` scripts use the same journey with their respective stages. They write HTML reports under `reports/` when run with the `web-dashboard=export=...` option. See [RESULTS.md](RESULTS.md) for the measured results and notes.
