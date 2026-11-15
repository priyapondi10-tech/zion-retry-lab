# Retry Lab

A complete dependency-free, local discrete-event simulator for AI/API retry policies. Compares no retries, capped exponential backoff with configurable jitter, and retries with a shared closed/open/half-open circuit breaker. It complements an LLM gateway or model observatory **without making any real service calls**.

Live: https://priyapondi10-tech.github.io/zion-retry-lab/

## Run / test / deploy

```sh
python3 -m http.server 8080
node --test test.mjs
```

Open http://localhost:8080/. Requires a modern ES-module browser; Node 20+ for tests. No install, build, paid API, key or backend. GitHub Pages serves main/root with `.nojekyll`.

## Features

- Healthy, random-brownout and transient-outage presets; custom workload, failure rate, backoff, jitter, request deadlines, breaker and hypothetical per-attempt price.
- Shared comparison of success, amplification, failed-fast rejections, timeouts, all-request and successful-request P95, attempt timelines and full event traces.
- Reproducible stateless noise keyed by seed/request/attempt; policy scheduling does not accidentally reshuffle random failures. A binary min-heap orders events stably.
- JSON reports and CSV event exports; responsive, keyboard-accessible UI; no storage, analytics or network fetch. CSP blocks connections and form submissions.

## Model and limitations

Evenly spaced arrivals, constant attempt latency and independent failure noise outside the configured outage. An attempt starting at or after outage start and before outage end fails even if the outage ends before its completion. The end-to-end deadline includes retry waiting. Every started attempt is charged; rejected attempts are not. Backoff is base×2^(attempt−1), capped, with none/full/equal jitter. At most 1,000 requests and six retries are allowed.

The shared breaker opens after N consecutive **completed** failed attempts. Existing calls finish. While open it rejects new attempts immediately; after cooldown it permits one half-open probe, closing on success and reopening on failure. Rejected requests are not queued for later. A retry may itself be rejected. Deadlines are per logical request. Latency P95 for all requests includes rejections; a lower value can mean more failures. Successful P95 is separate and null if no request succeeds.

No token-varying bills, capacity/rate limits, Retry-After, real cancellation propagation, distributed breaker consistency or semantic idempotency is modeled. This is not a load test, reliability forecast, or recommendation to retry unsafe writes/payments. Use representative production measurements and safe idempotency controls separately.

## Network / prepared domain

All four utilities and the staging homepage are bidirectionally linked. Planned domain: `retry-lab.ziontechgroup.com`; no Pages custom domain or CNAME file is configured while the connected Cloudflare zone is pending. The HTTPS github.io URL is the working address. Before activating a prepared DNS zone, verify GitHub domain ownership, claim each hostname in Pages and validate TLS to avoid takeover. Production and existing DNS are not changed.
