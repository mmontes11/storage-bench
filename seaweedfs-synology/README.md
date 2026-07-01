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
Reqs: 34666, Errs:0, Objs:34666, Bytes: 20.30GiB
 -    DELETE Average: 12 Obj/s; Current 13 Obj/s, 34.2 ms/req
 -       GET Average: 52 Obj/s, 52.1MiB/s; Current 60 Obj/s, 60.3MiB/s
 -       PUT Average: 17 Obj/s, 17.3MiB/s; Current 17 Obj/s, 17.1MiB/s
 -      STAT Average: 35 Obj/s; Current 27 Obj/s, 38.6 ms/req


Report: DELETE. Concurrency: 64. Ran: 4m57s
 * Average: 11.56 obj/s
 * Reqs: Avg: 53.8ms, 50%: 36.2ms, 90%: 73.3ms, 99%: 495.6ms, Fastest: 5.8ms, Slowest: 5017.6ms, StdDev: 80.6ms

Throughput, split into 297 x 1s:
 * Fastest: 26.33 obj/s
 * 50% Median: 11.57 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 4m58s
 * Average: 52.15 MiB/s, 52.15 obj/s
 * Reqs: Avg: 868.3ms, 50%: 483.1ms, 90%: 2310.6ms, 99%: 4677.0ms, Fastest: 41.3ms, Slowest: 11756.7ms, StdDev: 988.6ms
 * TTFB: Avg: 257ms, Best: 5ms, 25th: 27ms, Median: 34ms, 75th: 152ms, 90th: 761ms, 99th: 2.782s, Worst: 7.514s StdDev: 592ms

Throughput, split into 298 x 1s:
 * Fastest: 81.5MiB/s, 81.49 obj/s
 * 50% Median: 57.7MiB/s, 57.65 obj/s
 * Slowest: 5.9MiB/s, 5.90 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 4m57s
 * Average: 17.35 MiB/s, 17.35 obj/s
 * Reqs: Avg: 1151.2ms, 50%: 609.2ms, 90%: 3053.1ms, 99%: 5579.9ms, Fastest: 41.9ms, Slowest: 14700.9ms, StdDev: 1215.5ms
 * TTFB: Avg: 1.086s, Best: 33ms, 25th: 329ms, Median: 533ms, 75th: 1.437s, 90th: 2.999s, 99th: 5.53s, Worst: 14.641s StdDev: 1.223s

Throughput, split into 297 x 1s:
 * Fastest: 30.1MiB/s, 30.13 obj/s
 * 50% Median: 17.8MiB/s, 17.77 obj/s
 * Slowest: 1030.8KiB/s, 1.01 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 4m57s
 * Average: 34.69 obj/s
 * Reqs: Avg: 47.8ms, 50%: 29.2ms, 90%: 48.1ms, 99%: 428.0ms, Fastest: 2.2ms, Slowest: 5017.6ms, StdDev: 83.6ms

Throughput, split into 297 x 1s:
 * Fastest: 68.89 obj/s
 * 50% Median: 35.64 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 4m58s
 * Average: 69.52 MiB/s, 115.83 obj/s

Throughput, split into 298 x 1s:
 * Fastest: 99.5MiB/s, 189.21 obj/s
 * 50% Median: 75.7MiB/s, 124.92 obj/s
 * Slowest: 8.6MiB/s, 11.38 obj/s
```

## Mixed - 1MiB object size - in-cluster

```bash
Reqs: 103811, Errs:0, Objs:103811, Bytes: 60.80GiB
 -    DELETE Average: 34 Obj/s; Current 5 Obj/s, 6.0 ms/req
 -       GET Average: 154 Obj/s, 153.9MiB/s; Current 41 Obj/s, 40.7MiB
 -       PUT Average: 51 Obj/s, 51.3MiB/s; Current 17 Obj/s, 16.7MiB/s
 -      STAT Average: 103 Obj/s; Current 17 Obj/s, 3.9 ms/req


Report: DELETE. Concurrency: 64. Ran: 4m57s
 * Average: 34.22 obj/s
 * Reqs: Avg: 13.6ms, 50%: 10.5ms, 90%: 23.4ms, 99%: 83.6ms, Fastest: 1.7ms, Slowest: 1096.6ms, StdDev: 15.2ms

Throughput, split into 297 x 1s:
 * Fastest: 122.39 obj/s
 * 50% Median: 30.73 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 4m57s
 * Average: 153.92 MiB/s, 153.92 obj/s
 * Reqs: Avg: 241.9ms, 50%: 143.3ms, 90%: 484.3ms, 99%: 2313.1ms, Fastest: 1.8ms, Slowest: 8398.0ms, StdDev: 414.4ms
 * TTFB: Avg: 238ms, Best: 1ms, 25th: 47ms, Median: 139ms, 75th: 248ms, 90th: 481ms, 99th: 2.309s, Worst: 8.366s StdDev: 414ms

Throughput, split into 297 x 1s:
 * Fastest: 543.3MiB/s, 543.31 obj/s
 * 50% Median: 142.2MiB/s, 142.23 obj/s
 * Slowest: 5.1MiB/s, 5.11 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 4m57s
 * Average: 51.29 MiB/s, 51.29 obj/s
 * Reqs: Avg: 592.6ms, 50%: 297.5ms, 90%: 1647.2ms, 99%: 3677.3ms, Fastest: 15.4ms, Slowest: 8347.3ms, StdDev: 818.8ms
 * TTFB: Avg: 588ms, Best: 12ms, 25th: 137ms, Median: 293ms, 75th: 651ms, 90th: 1.643s, 99th: 3.672s, Worst: 8.335s StdDev: 819ms

Throughput, split into 297 x 1s:
 * Fastest: 179.7MiB/s, 179.73 obj/s
 * 50% Median: 44.8MiB/s, 44.79 obj/s
 * Slowest: 3.0MiB/s, 3.04 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 4m57s
 * Average: 102.58 obj/s
 * Reqs: Avg: 9.8ms, 50%: 6.8ms, 90%: 18.1ms, 99%: 64.4ms, Fastest: 1.0ms, Slowest: 1103.6ms, StdDev: 16.8ms

Throughput, split into 297 x 1s:
 * Fastest: 360.72 obj/s
 * 50% Median: 93.00 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 4m57s
 * Average: 205.21 MiB/s, 342.01 obj/s

Throughput, split into 297 x 1s:
 * Fastest: 719.7MiB/s, 1191.99 obj/s
 * 50% Median: 181.8MiB/s, 313.98 obj/s
 * Slowest: 8.1MiB/s, 8.14 obj/s
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
