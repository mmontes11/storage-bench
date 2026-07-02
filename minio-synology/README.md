### Storage

MinIO object storage on [Synology CSI driver](../synology/).

### Credentials

```bash
export S3_ACCESS_KEY=<access-key>
export S3_SECRET_KEY=<access-secret-key>
```

# MinIO Benchmarks

## Environment

MinIO deployed via [MinIO Operator](https://github.com/minio/operator) (chart `operator`/`tenant` v7.1.1) with the tenant CR at [minio-tenant-helmrelease.yaml](https://github.com/mmontes11/k8s-infrastructure/blob/4289ee45881f4e84d4c11e9a2bb1fd6ddeaa7dfe/infrastructure/minio-operator/minio-tenant/minio-tenant-helmrelease.yaml).

MinIO runs control plane, S3 API, and storage in a single server process, so there's no per-component split like SeaweedFS's master/filer/volume/s3/admin — everything runs in the one-server, one-pool tenant on `compute-large` nodes.

| Component | Replicas | CPU Request | Memory Request | Memory Limit | Node Selector |
|-----------|----------|-------------|-----------------|--------------|---------------|
| minio (pool-0) | 1 | 1000m | 1Gi | 4Gi | compute-large |

**Image:** MinIO Operator-managed (chart default, no explicit override).

**Metadata store:** None — metadata lives alongside object data on MinIO's own erasure-coded volume, unlike SeaweedFS's MariaDB-backed filer.

**Storage:** 2Ti PV, statically pre-provisioned (PVC `data0-minio-pool-0-0` bound by name to a pre-created PV), backed by Synology CSI driver (`synology-ext4-retain` storage class) — the same static-binding approach used for SeaweedFS's volume server.

**TLS:** No inter-component TLS (single-process pod, nothing internal to secure; `requestAutoCert: false`). Edge TLS terminated at the traefik-internal gateway, same as SeaweedFS.

**Network:** External client network is 1Gbps — relevant for the external-endpoint benchmark below.

**S3 endpoints:**
- External: `minio.mmontes-internal.duckdns.org` (port 443, TLS)
- In-cluster: `minio.storage.svc.cluster.local:80` (plain HTTP)

**Buckets:** `pvc`, `database`, `management`

**Console:** `minio-console.mmontes-internal.duckdns.org` (management UI, not benchmarked)

## Mixed - 1MiB object size - external

```bash
 warp mixed --host minio.mmontes-internal.duckdns.org:443 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 1m \
  --tls
```

```
Reqs: 8191, Errs:0, Objs:8191, Bytes: 4905.0MiB
 -    DELETE Average: 14 Obj/s; Current 14 Obj/s, 155.7 ms/req
 -       GET Average: 62 Obj/s, 61.7MiB/s; Current 62 Obj/s, 61
 -       PUT Average: 21 Obj/s, 20.7MiB/s; Current 22 Obj/s, 22
 -      STAT Average: 41 Obj/s; Current 41 Obj/s, 23.5 ms/req


Report: DELETE. Concurrency: 64. Ran: 57s
 * Average: 13.80 obj/s
 * Reqs: Avg: 143.8ms, 50%: 60.8ms, 90%: 365.5ms, 99%: 630.6ms, Fastest: 5.0ms, Slowest: 813.4ms, StdDev: 143.7ms

Throughput, split into 57 x 1s:
 * Fastest: 25.00 obj/s
 * 50% Median: 13.68 obj/s
 * Slowest: 4.74 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 57s
 * Average: 61.54 MiB/s, 61.54 obj/s
 * Reqs: Avg: 377.2ms, 50%: 269.9ms, 90%: 836.3ms, 99%: 1480.4ms, Fastest: 35.3ms, Slowest: 1875.9ms, StdDev: 317.3ms
 * TTFB: Avg: 34ms, Best: 5ms, 25th: 12ms, Median: 16ms, 75th: 21ms, 90th: 106ms, 99th: 392ms, Worst: 1.028s StdDev: 70ms

Throughput, split into 57 x 1s:
 * Fastest: 79.5MiB/s, 79.46 obj/s
 * 50% Median: 62.1MiB/s, 62.11 obj/s
 * Slowest: 43.8MiB/s, 43.78 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 57s
 * Average: 20.81 MiB/s, 20.81 obj/s
 * Reqs: Avg: 1895.8ms, 50%: 1944.2ms, 90%: 2750.1ms, 99%: 3234.1ms, Fastest: 350.0ms, Slowest: 4385.9ms, StdDev: 697.5ms
 * TTFB: Avg: 1.788s, Best: 283ms, 25th: 1.298s, Median: 1.844s, 75th: 2.306s, 90th: 2.637s, 99th: 3.141s, Worst: 4.361s StdDev: 665ms

Throughput, split into 57 x 1s:
 * Fastest: 34.6MiB/s, 34.63 obj/s
 * 50% Median: 21.1MiB/s, 21.09 obj/s
 * Slowest: 8.9MiB/s, 8.90 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 57s
 * Average: 41.20 obj/s
 * Reqs: Avg: 21.4ms, 50%: 16.3ms, 90%: 22.3ms, 99%: 213.8ms, Fastest: 4.4ms, Slowest: 715.0ms, StdDev: 34.4ms

Throughput, split into 57 x 1s:
 * Fastest: 69.17 obj/s
 * 50% Median: 40.83 obj/s
 * Slowest: 21.58 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 57s
 * Average: 82.36 MiB/s, 137.35 obj/s

Throughput, split into 57 x 1s:
 * Fastest: 90.1MiB/s, 173.65 obj/s
 * 50% Median: 81.7MiB/s, 134.49 obj/s
 * Slowest: 79.5MiB/s, 107.23 obj/s
```

## Mixed - 1MiB object size - in-cluster

```bash
 warp mixed --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 1m
```

```
Reqs: 17732, Errs:0, Objs:17732, Bytes: 10.36GiB
 -    DELETE Average: 30 Obj/s; Current 39 Obj/s, 180.9 ms/req
 -       GET Average: 135 Obj/s, 135.0MiB/s; Current 146 Obj/s,
 -       PUT Average: 45 Obj/s, 44.9MiB/s; Current 48 Obj/s, 48
 -      STAT Average: 90 Obj/s; Current 105 Obj/s, 21.7 ms/req


Report: DELETE. Concurrency: 64. Ran: 57s
 * Average: 29.96 obj/s
 * Reqs: Avg: 113.1ms, 50%: 59.2ms, 90%: 259.6ms, 99%: 848.8ms, Fastest: 0.6ms, Slowest: 2384.1ms, StdDev: 165.1ms

Throughput, split into 57 x 1s:
 * Fastest: 51.00 obj/s
 * 50% Median: 29.54 obj/s
 * Slowest: 4.81 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 57s
 * Average: 135.59 MiB/s, 135.59 obj/s
 * Reqs: Avg: 89.1ms, 50%: 71.5ms, 90%: 202.5ms, 99%: 302.5ms, Fastest: 1.5ms, Slowest: 560.0ms, StdDev: 82.7ms
 * TTFB: Avg: 87ms, Best: 1ms, 25th: 8ms, Median: 69ms, 75th: 149ms, 90th: 201ms, 99th: 301ms, Worst: 558ms StdDev: 83ms

Throughput, split into 57 x 1s:
 * Fastest: 175.3MiB/s, 175.29 obj/s
 * 50% Median: 145.8MiB/s, 145.83 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 57s
 * Average: 45.12 MiB/s, 45.12 obj/s
 * Reqs: Avg: 1044.0ms, 50%: 960.7ms, 90%: 1633.3ms, 99%: 2016.9ms, Fastest: 309.0ms, Slowest: 4261.2ms, StdDev: 321.4ms
 * TTFB: Avg: 1.04s, Best: 305ms, 25th: 846ms, Median: 957ms, 75th: 1.103s, 90th: 1.63s, 99th: 2.013s, Worst: 4.25s StdDev: 321ms

Throughput, split into 57 x 1s:
 * Fastest: 66.7MiB/s, 66.69 obj/s
 * 50% Median: 47.3MiB/s, 47.30 obj/s
 * Slowest: 14.9MiB/s, 14.86 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 57s
 * Average: 90.24 obj/s
 * Reqs: Avg: 18.9ms, 50%: 7.5ms, 90%: 58.0ms, 99%: 121.3ms, Fastest: 0.3ms, Slowest: 308.9ms, StdDev: 27.8ms

Throughput, split into 57 x 1s:
 * Fastest: 128.00 obj/s
 * 50% Median: 96.91 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 57s
 * Average: 180.71 MiB/s, 300.92 obj/s

Throughput, split into 57 x 1s:
 * Fastest: 230.0MiB/s, 404.97 obj/s
 * 50% Median: 207.9MiB/s, 331.41 obj/s
 * Slowest: 16.4MiB/s, 21.24 obj/s
```

## Get - 1MiB object size - in-cluster

```bash
 warp get --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 1m
```

```
Reqs: 50529, Errs:0, Objs:50529, Bytes: 49.34GiB
 -       GET Average: 854 Obj/s, 854.2MiB/s; Current 808 Obj/s, 807.8MiB/s, 76.2 ms/req, TTFB: 9.6ms


Report: GET. Concurrency: 64. Ran: 57s
 * Average: 854.24 MiB/s, 854.24 obj/s
 * Reqs: Avg: 74.1ms, 50%: 73.0ms, 90%: 90.0ms, 99%: 110.2ms, Fastest: 5.3ms, Slowest: 206.9ms, StdDev: 13.1ms
 * TTFB: Avg: 9ms, Best: 2ms, 25th: 8ms, Median: 9ms, 75th: 10ms, 90th: 11ms, 99th: 15ms, Worst: 32ms StdDev: 2ms

Throughput, split into 57 x 1s:
 * Fastest: 948.3MiB/s, 948.26 obj/s
 * 50% Median: 864.0MiB/s, 864.04 obj/s
 * Slowest: 738.2MiB/s, 738.21 obj/s
```

## Get - 128MiB object size - in-cluster

```bash
 warp get --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --objects 50 \
  --obj.size 128MiB \
  --duration 1m
```

```
Reqs: 80, Errs:0, Objs:80, Bytes: 10.00GiB
 -       GET Average: 1 Obj/s, 169.9MiB/s; Current 0 Obj/s, 29.8MiB/s, 5622.3 ms/req, TTFB: 357.7ms


Report: GET. Concurrency: 8. Ran: 1m1s
 * Average: 175.20 MiB/s, 1.37 obj/s
 * Reqs: Avg: 5827.1ms, 50%: 6931.7ms, 90%: 8371.9ms, 99%: 8442.5ms, Fastest: 122.6ms, Slowest: 9366.9ms, StdDev: 2482.3ms
 * TTFB: Avg: 291ms, Best: 1ms, 25th: 234ms, Median: 343ms, 75th: 455ms, 90th: 517ms, 99th: 555ms, Worst: 811ms StdDev: 187ms

Throughput, split into 61 x 1s:
 * Fastest: 614.6MiB/s, 4.80 obj/s
 * 50% Median: 157.1MiB/s, 1.23 obj/s
 * Slowest: 112.1MiB/s, 0.88 obj/s
```

## Put - 1MiB object size - in-cluster

```bash
 warp put --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --obj.size 1MiB \
  --duration 1m
```

```
Reqs: 5925, Errs:0, Objs:5925, Bytes: 5925.0MiB
 -       PUT Average: 99 Obj/s, 98.9MiB/s; Current 92 Obj/s, 92.2MiB/s, 754.0 ms/req, TTFB: 747.8ms


Report: PUT. Concurrency: 64. Ran: 58s
 * Average: 98.86 MiB/s, 98.86 obj/s
 * Reqs: Avg: 721.2ms, 50%: 703.2ms, 90%: 847.6ms, 99%: 1182.8ms, Fastest: 44.4ms, Slowest: 2024.0ms, StdDev: 132.9ms
 * TTFB: Avg: 715ms, Best: 41ms, 25th: 681ms, Median: 697ms, 75th: 723ms, 90th: 841ms, 99th: 1.176s, Worst: 2.018s StdDev: 133ms

Throughput, split into 58 x 1s:
 * Fastest: 114.9MiB/s, 114.93 obj/s
 * 50% Median: 102.1MiB/s, 102.12 obj/s
 * Slowest: 32.2MiB/s, 32.20 obj/s
```

## Put - 128MiB object size - in-cluster

```bash
 warp put --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --obj.size 128MiB \
  --duration 1m
```

```
Reqs: 47, Errs:0, Objs:47, Bytes: 6016.0MiB
 -       PUT Average: 1 Obj/s, 96.4MiB/s; Current 0 Obj/s, 11.0MiB/s, 10712.6 ms/req, TTFB: 4683.1ms


Report: PUT. Concurrency: 8. Ran: 1m4s
 * Average: 104.44 MiB/s, 0.82 obj/s
 * Reqs: Avg: 9424.0ms, 50%: 9432.1ms, 90%: 10285.2ms, 99%: 10286.2ms, Fastest: 7008.0ms, Slowest: 12213.0ms, StdDev: 743.9ms
 * TTFB: Avg: 4.066s, Best: 630ms, 25th: 3.978s, Median: 4.069s, 75th: 4.789s, 90th: 4.851s, 99th: 4.856s, Worst: 7.118s StdDev: 768ms

Throughput, split into 64 x 1s:
 * Fastest: 129.9MiB/s, 1.01 obj/s
 * 50% Median: 104.9MiB/s, 0.82 obj/s
 * Slowest: 90.9MiB/s, 0.71 obj/s
```
