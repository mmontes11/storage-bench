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
 * Average: 666.55 MiB/s, 666.55 obj/s
 * Reqs: Avg: 96.1ms, 50%: 95.7ms, 90%: 133.4ms, 99%: 256.8ms, Fastest: 3.0ms, Slowest: 2137.2ms, StdDev: 52.1ms
 * TTFB: Avg: 71ms, Best: 2ms, 25th: 49ms, Median: 71ms, 75th: 88ms, 90th: 106ms, 99th: 233ms, Worst: 2.109s StdDev: 51ms

Throughput, split into 297 x 1s:
 * Fastest: 759.0MiB/s, 759.04 obj/s
 * 50% Median: 668.7MiB/s, 668.69 obj/s
 * Slowest: 550.1MiB/s, 550.05 obj/s
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
Report: GET. Concurrency: 8. Ran: 5m2s
 * Average: 136.79 MiB/s, 1.07 obj/s
 * Reqs: Avg: 7791.3ms, 50%: 8318.4ms, 90%: 12227.6ms, 99%: 12355.7ms, Fastest: 210.7ms, Slowest: 15877.6ms, StdDev: 2840.4ms
 * TTFB: Avg: 1.674s, Best: 3ms, 25th: 1.125s, Median: 1.894s, 75th: 2.576s, 90th: 3.161s, 99th: 3.256s, Worst: 4.823s StdDev: 960ms

Throughput, split into 302 x 1s:
 * Fastest: 393.2MiB/s, 3.07 obj/s
 * 50% Median: 127.3MiB/s, 0.99 obj/s
 * Slowest: 72.3MiB/s, 0.56 obj/s
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
Report: PUT. Concurrency: 64. Ran: 5m3s
 * Average: 101.99 MiB/s, 101.99 obj/s
 * Reqs: Avg: 867.0ms, 50%: 391.1ms, 90%: 1262.2ms, 99%: 6761.5ms, Fastest: 32.6ms, Slowest: 10735.1ms, StdDev: 1522.2ms
 * TTFB: Avg: 861ms, Best: 29ms, 25th: 356ms, Median: 385ms, 75th: 453ms, 90th: 1.256s, 99th: 6.756s, Worst: 10.731s StdDev: 1.522s

Throughput, split into 303 x 1s:
 * Fastest: 522.3MiB/s, 522.26 obj/s
 * 50% Median: 12.1MiB/s, 12.10 obj/s
 * Slowest: 6.0MiB/s, 5.99 obj/s
```


## Put - 128MiB object size - in-cluster

```bash
 warp put --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --objects 50 \
  --obj.size 128MiB \
  --duration 5m \
  --insecure
```

```
Report: PUT. Concurrency: 8. Ran: 5m4s
 * Average: 104.31 MiB/s, 0.81 obj/s
 * Reqs: Avg: 9816.5ms, 50%: 10400.6ms, 90%: 12753.1ms, 99%: 12753.1ms, Fastest: 1628.4ms, Slowest: 19434.3ms, StdDev: 2278.4ms
 * TTFB: Avg: 4.795s, Best: 279ms, 25th: 3.078s, Median: 5.771s, 75th: 7.975s, 90th: 8.873s, 99th: 8.873s, Worst: 14.031s StdDev: 3.281s

Throughput, split into 304 x 1s:
 * Fastest: 217.7MiB/s, 1.70 obj/s
 * 50% Median: 93.1MiB/s, 0.73 obj/s
 * Slowest: 73.7MiB/s, 0.58 obj/s
```

# Minio comparison

## Mixed 1MiB

| Metric | SeaweedFS | MinIO |
|--------|-----------|-------|
| Total throughput | 115.18 MiB/s | 105.92 MiB/s |
| Total obj/s | 191.96 | 176.52 |
| GET avg | 86.39 MiB/s | 79.43 MiB/s |
| PUT avg | 28.79 MiB/s | 26.49 MiB/s |
| DELETE avg | 19.32 obj/s | 17.67 obj/s |
| STAT avg | 57.92 obj/s | 52.93 obj/s |

## Get 1MiB

| Metric | SeaweedFS | MinIO |
|--------|-----------|-------|
| GET avg | 666.55 MiB/s | 111.69 MiB/s |

## Put 1MiB

| Metric | SeaweedFS | MinIO |
|--------|-----------|-------|
| PUT avg | 101.99 MiB/s | 57.87 MiB/s |

## Put 128MiB

| Metric | SeaweedFS | MinIO |
|--------|-----------|-------|
| PUT avg | 104.31 MiB/s | 85.66 MiB/s |

SeaweedFS outperforms MinIO across all benchmarks, with particularly significant advantages in read throughput (6x for 1MiB gets) and write performance (1.8x for 1MiB puts, 1.2x for 128MiB puts).
