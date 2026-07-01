### Storage

SeaweedFS object storage on [Synology CSI driver](../synology/).

### Credentials

```bash
export S3_ACCESS_KEY=<access-key>
export S3_SECRET_KEY=<access-secret-key>
```

# SeaweedFS Benchmarks

## Environment

SeaweedFS deployed via [SeaweedFS Operator](https://github.com/seaweedfs/seaweedfs-operator) with the CR at [seaweed.yaml](https://github.com/mmontes11/k8s-infrastructure/blob/345bde9d5c5a51908ac504d8e206aeef9e27/infrastructure/seaweedfs-operator/seaweedfs/seaweed.yaml).

Master, filer, and admin run on default nodes. Volume and S3 run on `compute-large` nodes.

| Component | Replicas | CPU Request | Memory Request | Memory Limit | Node Selector |
|-----------|----------|-------------|----------------|--------------|---------------|
| master | 1 | 100m | 256Mi | 512Mi | default |
| filer | 1 | 200m | 256Mi | 1Gi | default |
| volume | 1 | 500m | 1Gi | 4Gi | compute-large |
| s3 | 1 | 500m | 1Gi | 4Gi | compute-large |
| admin | 1 | 100m | 128Mi | 512Mi | default |

**Image:** `chrislusf/seaweedfs:4.36`

**Metadata store:** Multi-tenant MariaDB (`seaweedfs` database) with connection pooling (max open/idle, max lifetime, interpolate params).

**Storage:** 2Ti PV pre-provisioned via label selector (`app.kubernetes.io/name: seaweedfs, app.kubernetes.io/component: volume`), backed by Synology CSI driver (`synology-retain` storage class).

**TLS:** Inter-component gRPC mTLS enabled (homelab ClusterIssuer). Edge TLS terminated at traefik-internal gateway.

**S3 endpoints:**
- External: `s3.mmontes-internal.duckdns.org` (port 443, TLS)
- In-cluster: `seaweedfs-s3.storage.svc.cluster.local:8333` (plain HTTP)

**Buckets:** `pvc`, `database`, `management`

## Mixed - 1MiB object size - external

```bash
  warp mixed --host s3.mmontes-internal.duckdns.org \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m \
  --tls
```

```
Report: DELETE. Concurrency: 64. Ran: 4m56s
 * Average: 9.94 obj/s
 * Reqs: Avg: 183.5ms, 50%: 27.2ms, 90%: 245.6ms, 99%: 4327.7ms, Fastest: 4.9ms, Slowest: 10060.9ms, StdDev: 658.4ms

Throughput, split into 296 x 1s:
 * Fastest: 26.49 obj/s
 * 50% Median: 9.19 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 4m58s
 * Average: 44.60 MiB/s, 44.60 obj/s
 * Reqs: Avg: 911.4ms, 50%: 495.9ms, 90%: 2359.1ms, 99%: 5881.0ms, Fastest: 31.3ms, Slowest: 11308.2ms, StdDev: 1153.5ms
 * TTFB: Avg: 438ms, Best: 4ms, 25th: 51ms, Median: 137ms, 75th: 393ms, 90th: 1.162s, 99th: 4.945s, Worst: 10.82s StdDev: 914ms

Throughput, split into 298 x 1s:
 * Fastest: 74.4MiB/s, 74.45 obj/s
 * 50% Median: 49.2MiB/s, 49.22 obj/s
 * Slowest: 7.7MiB/s, 7.73 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 4m58s
 * Average: 14.88 MiB/s, 14.88 obj/s
 * Reqs: Avg: 1395.0ms, 50%: 791.6ms, 90%: 3701.4ms, 99%: 7152.9ms, Fastest: 37.8ms, Slowest: 15601.1ms, StdDev: 1579.2ms
 * TTFB: Avg: 1.386s, Best: 31ms, 25th: 395ms, Median: 783ms, 75th: 1.83s, 90th: 3.693s, 99th: 7.148s, Worst: 15.583s StdDev: 1.578s

Throughput, split into 298 x 1s:
 * Fastest: 28.0MiB/s, 28.00 obj/s
 * 50% Median: 15.5MiB/s, 15.50 obj/s
 * Slowest: 3.9MiB/s, 3.86 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 4m57s
 * Average: 29.74 obj/s
 * Reqs: Avg: 174.6ms, 50%: 20.8ms, 90%: 192.0ms, 99%: 4696.6ms, Fastest: 2.3ms, Slowest: 11104.2ms, StdDev: 713.0ms

Throughput, split into 297 x 1s:
 * Fastest: 64.18 obj/s
 * 50% Median: 32.00 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 4m58s
 * Average: 59.47 MiB/s, 99.10 obj/s

Throughput, split into 298 x 1s:
 * Fastest: 94.6MiB/s, 180.74 obj/s
 * 50% Median: 60.7MiB/s, 107.90 obj/s
 * Slowest: 12.4MiB/s, 12.52 obj/s
```

## Mixed - 1MiB object size - in-cluster

```bash
  warp mixed --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m \
  --insecure
```

```
Report: DELETE. Concurrency: 64. Ran: 4m56s
 * Average: 19.32 obj/s
 * Reqs: Avg: 7.6ms, 50%: 5.9ms, 90%: 12.5ms, 99%: 38.8ms, Fastest: 2.0ms, Slowest: 243.9ms, StdDev: 6.7ms

Throughput, split into 296 x 1s:
 * Fastest: 90.82 obj/s
 * 50% Median: 16.00 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 4m58s
 * Average: 86.39 MiB/s, 86.39 obj/s
 * Reqs: Avg: 392.9ms, 50%: 204.6ms, 90%: 784.3ms, 99%: 3872.2ms, Fastest: 2.0ms, Slowest: 7564.6ms, StdDev: 705.7ms
 * TTFB: Avg: 390ms, Best: 1ms, 25th: 46ms, Median: 201ms, 75th: 406ms, 90th: 782ms, 99th: 3.87s, Worst: 7.561s StdDev: 706ms

Throughput, split into 298 x 1s:
 * Fastest: 415.2MiB/s, 415.19 obj/s
 * 50% Median: 72.9MiB/s, 72.87 obj/s
 * Slowest: 2.7MiB/s, 2.75 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 4m58s
 * Average: 28.79 MiB/s, 28.79 obj/s
 * Reqs: Avg: 1315.3ms, 50%: 647.3ms, 90%: 3856.8ms, 99%: 6605.1ms, Fastest: 16.6ms, Slowest: 9852.4ms, StdDev: 1672.5ms
 * TTFB: Avg: 1.31s, Best: 12ms, 25th: 236ms, Median: 642ms, 75th: 1.697s, 90th: 3.852s, 99th: 6.6s, Worst: 9.848s StdDev: 1.673s

Throughput, split into 298 x 1s:
 * Fastest: 136.8MiB/s, 136.76 obj/s
 * 50% Median: 22.2MiB/s, 22.24 obj/s
 * Slowest: 4.2MiB/s, 4.18 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 4m56s
 * Average: 57.92 obj/s
 * Reqs: Avg: 5.2ms, 50%: 3.8ms, 90%: 9.4ms, 99%: 27.8ms, Fastest: 1.2ms, Slowest: 244.1ms, StdDev: 5.8ms

Throughput, split into 296 x 1s:
 * Fastest: 270.55 obj/s
 * 50% Median: 48.00 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 4m58s
 * Average: 115.18 MiB/s, 191.96 obj/s

Throughput, split into 298 x 1s:
 * Fastest: 552.0MiB/s, 913.31 obj/s
 * 50% Median: 92.8MiB/s, 162.85 obj/s
 * Slowest: 9.2MiB/s, 9.21 obj/s
```

## Get - 1MiB object size - in-cluster


```bash
 warp get --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m \
  --insecure
```

```
Report: GET. Concurrency: 64. Ran: 4m57s
 * Average: 630.80 MiB/s, 630.80 obj/s
 * Reqs: Avg: 101.5ms, 50%: 103.9ms, 90%: 135.4ms, 99%: 179.0ms, Fastest: 2.7ms, Slowest: 3001.7ms, StdDev: 34.9ms
 * TTFB: Avg: 56ms, Best: 1ms, 25th: 40ms, Median: 59ms, 75th: 71ms, 90th: 84ms, 99th: 129ms, Worst: 2.929s StdDev: 32ms

Throughput, split into 297 x 1s:
 * Fastest: 714.0MiB/s, 714.04 obj/s
 * 50% Median: 632.6MiB/s, 632.63 obj/s
 * Slowest: 493.3MiB/s, 493.32 obj/s
```

## Get - 128MiB object size - in-cluster


```bash
 warp get --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --objects 50 \
  --obj.size 128MiB \
  --duration 5m \
  --insecure
```

```
Report: GET. Concurrency: 8. Ran: 5m3s
 * Average: 115.11 MiB/s, 0.90 obj/s
 * Reqs: Avg: 8925.8ms, 50%: 9363.0ms, 90%: 12734.6ms, 99%: 12734.6ms, Fastest: 1443.6ms, Slowest: 18840.1ms, StdDev: 2463.1ms
 * TTFB: Avg: 1.949s, Best: 3ms, 25th: 1.678s, Median: 2.12s, 75th: 2.67s, 90th: 3.023s, 99th: 3.023s, Worst: 5.342s StdDev: 734ms

Throughput, split into 303 x 1s:
 * Fastest: 285.2MiB/s, 2.23 obj/s
 * 50% Median: 108.2MiB/s, 0.85 obj/s
 * Slowest: 65.4MiB/s, 0.51 obj/s
```

## Put - 1MiB object size - in-cluster

```bash
 warp put --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --obj.size 1MiB \
  --duration 5m \
  --insecure
```

```
Report: PUT. Concurrency: 64. Ran: 5m0s
 * Average: 101.52 MiB/s, 101.52 obj/s
 * Reqs: Avg: 764.2ms, 50%: 276.5ms, 90%: 1379.7ms, 99%: 6460.6ms, Fastest: 17.2ms, Slowest: 11139.9ms, StdDev: 1517.0ms
 * TTFB: Avg: 758ms, Best: 13ms, 25th: 237ms, Median: 270ms, 75th: 331ms, 90th: 1.373s, 99th: 6.453s, Worst: 11.134s StdDev: 1.517s

Throughput, split into 300 x 1s:
 * Fastest: 526.1MiB/s, 526.09 obj/s
 * 50% Median: 14.8MiB/s, 14.80 obj/s
 * Slowest: 6.2MiB/s, 6.16 obj/s
```


## Put - 128MiB object size - in-cluster

```bash
 warp put --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --obj.size 128MiB \
  --duration 5m \
  --insecure
```

```
Report: PUT. Concurrency: 8. Ran: 5m1s
 * Average: 106.17 MiB/s, 0.83 obj/s
 * Reqs: Avg: 9685.4ms, 50%: 10293.3ms, 90%: 12005.2ms, 99%: 12011.8ms, Fastest: 1049.9ms, Slowest: 16394.3ms, StdDev: 1965.7ms
 * TTFB: Avg: 4.601s, Best: 266ms, 25th: 3.564s, Median: 4.766s, 75th: 6.983s, 90th: 7.862s, 99th: 7.874s, Worst: 14.364s StdDev: 2.453s

Throughput, split into 301 x 1s:
 * Fastest: 216.9MiB/s, 1.69 obj/s
 * 50% Median: 99.3MiB/s, 0.78 obj/s
 * Slowest: 41.4MiB/s, 0.32 obj/s
```
