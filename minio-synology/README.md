### Storage

MinIO object storage on [Synology CSI driver](../synology/).

### Network

1Gbps

# Mixed - 1MiB object size

```bash
 warp mixed --host 10.0.0.90:443 \
  --access-key <access-key> \
  --secret-key <secret-key> \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m \
  --tls \
  --insecure
```

```
Report: DELETE. Concurrency: 64. Ran: 4m57s
 * Average: 17.67 obj/s
 * Reqs: Avg: 107.7ms, 50%: 25.0ms, 90%: 338.4ms, 99%: 730.3ms, Fastest: 1.7ms, Slowest: 2736.1ms, StdDev: 165.4ms

Throughput, split into 297 x 1s:
 * Fastest: 38.35 obj/s
 * 50% Median: 16.40 obj/s
 * Slowest: 2.47 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 4m58s
 * Average: 79.43 MiB/s, 79.43 obj/s
 * Reqs: Avg: 229.8ms, 50%: 166.1ms, 90%: 502.8ms, 99%: 948.7ms, Fastest: 10.8ms, Slowest: 1715.8ms, StdDev: 198.3ms
 * TTFB: Avg: 73ms, Best: 1ms, 25th: 8ms, Median: 14ms, 75th: 71ms, 90th: 212ms, 99th: 702ms, Worst: 1.575s StdDev: 148ms

Throughput, split into 298 x 1s:
 * Fastest: 107.2MiB/s, 107.23 obj/s
 * 50% Median: 80.5MiB/s, 80.52 obj/s
 * Slowest: 5.6MiB/s, 5.63 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 4m58s
 * Average: 26.49 MiB/s, 26.49 obj/s
 * Reqs: Avg: 1608.3ms, 50%: 1637.3ms, 90%: 2278.1ms, 99%: 2553.3ms, Fastest: 175.6ms, Slowest: 4924.3ms, StdDev: 521.1ms

Throughput, split into 298 x 1s:
 * Fastest: 42.5MiB/s, 42.47 obj/s
 * 50% Median: 26.3MiB/s, 26.34 obj/s
 * Slowest: 10.0MiB/s, 9.99 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 4m57s
 * Average: 52.93 obj/s
 * Reqs: Avg: 24.8ms, 50%: 12.4ms, 90%: 44.5ms, 99%: 272.1ms, Fastest: 0.9ms, Slowest: 749.6ms, StdDev: 49.9ms

Throughput, split into 297 x 1s:
 * Fastest: 89.00 obj/s
 * 50% Median: 53.50 obj/s
 * Slowest: 1.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 4m58s
 * Average: 105.92 MiB/s, 176.52 obj/s

Throughput, split into 298 x 1s:
 * Fastest: 121.6MiB/s, 248.50 obj/s
 * 50% Median: 106.4MiB/s, 178.16 obj/s
 * Slowest: 18.6MiB/s, 22.45 obj/s
```

# Get - 1MiB object size


```bash
 warp get --host 10.0.0.90:443 \
  --access-key <access-key> \
  --secret-key <secret-key> \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m \
  --tls \
  --insecure
```

```
Report: GET. Concurrency: 64. Ran: 4m57s
 * Average: 111.69 MiB/s, 111.69 obj/s
 * Reqs: Avg: 572.0ms, 50%: 525.6ms, 90%: 880.4ms, 99%: 1315.3ms, Fastest: 101.8ms, Slowest: 2126.5ms, StdDev: 229.7ms
 * TTFB: Avg: 35ms, Best: 3ms, 25th: 33ms, Median: 35ms, 75th: 36ms, 90th: 37ms, 99th: 39ms, Worst: 203ms StdDev: 9ms

Throughput, split into 297 x 1s:
 * Fastest: 113.1MiB/s, 113.12 obj/s
 * 50% Median: 111.8MiB/s, 111.76 obj/s
 * Slowest: 107.1MiB/s, 107.09 obj/s
```

# Put - 1MiB object size

```bash
 warp put --host 10.0.0.90:443 \
  --access-key <access-key> \
  --secret-key <secret-key> \
  --concurrent 64 \
  --obj.size 1MiB \
  --duration 5m \
  --tls \
  --insecure
```

```
Report: PUT. Concurrency: 64. Ran: 4m57s
 * Average: 57.87 MiB/s, 57.87 obj/s
 * Reqs: Avg: 1109.3ms, 50%: 1118.1ms, 90%: 1231.8ms, 99%: 1259.2ms, Fastest: 111.1ms, Slowest: 1607.0ms, StdDev: 125.2ms

Throughput, split into 297 x 1s:
 * Fastest: 76.2MiB/s, 76.22 obj/s
 * 50% Median: 58.4MiB/s, 58.35 obj/s
 * Slowest: 43.6MiB/s, 43.59 obj/s
```

# Put - 128MiB object size

```bash
 warp put --host 10.0.0.90:443 \
  --access-key <access-key> \
  --secret-key <secret-key> \
  --concurrent 64 \
  --obj.size 128MiB \
  --duration 5m \
  --tls \
  --insecure
```

```
Report: PUT. Concurrency: 64. Ran: 6m19s
 * Average: 85.66 MiB/s, 0.67 obj/s
 * Reqs: Avg: 95619.0ms, 50%: 95622.4ms, 90%: 95654.6ms, 99%: 95679.0ms, Fastest: 93505.5ms, Slowest: 98936.8ms, StdDev: 27.1ms

Throughput, split into 379 x 1s:
 * Fastest: 87.5MiB/s, 0.68 obj/s
 * 50% Median: 85.2MiB/s, 0.67 obj/s
 * Slowest: 82.9MiB/s, 0.65 obj/s
```

# Put - 256MiB object size

```bash
 warp put --host 10.0.0.90:443 \
  --access-key <access-key> \
  --secret-key <secret-key> \
  --concurrent 64 \
  --obj.size 256MiB \
  --duration 5m \
  --tls \
  --insecure
```

```
Report: PUT. Concurrency: 64. Ran: 6m7s
 * Average: 88.78 MiB/s, 0.35 obj/s
 * Reqs: Avg: 184543.8ms, 50%: 184547.4ms, 90%: 184590.4ms, 99%: 184717.0ms, Fastest: 182123.8ms, Slowest: 186980.2ms, StdDev: 51.9ms

Throughput, split into 367 x 1s:
 * Fastest: 89.8MiB/s, 0.35 obj/s
 * 50% Median: 87.8MiB/s, 0.34 obj/s
 * Slowest: 87.8MiB/s, 0.34 obj/s
```

