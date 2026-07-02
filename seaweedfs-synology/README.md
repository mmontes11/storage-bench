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
  --duration 1m \
  --tls
```

```
Reqs: 8227, Errs:0, Objs:8227, Bytes: 4915.0MiB
 -    DELETE Average: 14 Obj/s; Current 0 Obj/s, 54.9 ms/req
 -       GET Average: 62 Obj/s, 62.3MiB/s; Current 6 Obj/s, 6.5MiB/s, 738.2 ms/req, TTFB: 94.4ms
 -       PUT Average: 21 Obj/s, 20.6MiB/s; Current 13 Obj/s, 13.2MiB/s, 1023.2 ms/req, TTFB: 990.8ms
 -      STAT Average: 42 Obj/s; Current 0 Obj/s, 34.6 ms/req


Report: DELETE. Concurrency: 64. Ran: 57s
 * Average: 13.79 obj/s
 * Reqs: Avg: 63.0ms, 50%: 39.1ms, 90%: 124.0ms, 99%: 335.9ms, Fastest: 6.7ms, Slowest: 500.1ms, StdDev: 69.8ms

Throughput, split into 57 x 1s:
 * Fastest: 22.88 obj/s
 * 50% Median: 14.62 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 57s
 * Average: 62.42 MiB/s, 62.42 obj/s
 * Reqs: Avg: 778.6ms, 50%: 390.2ms, 90%: 2209.0ms, 99%: 3882.7ms, Fastest: 47.2ms, Slowest: 7431.3ms, StdDev: 898.4ms
 * TTFB: Avg: 175ms, Best: 5ms, 25th: 27ms, Median: 34ms, 75th: 285ms, 90th: 425ms, 99th: 1.949s, Worst: 5.911s StdDev: 422ms

Throughput, split into 57 x 1s:
 * Fastest: 78.4MiB/s, 78.35 obj/s
 * 50% Median: 67.7MiB/s, 67.69 obj/s
 * Slowest: 9.9MiB/s, 9.88 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 57s
 * Average: 20.62 MiB/s, 20.62 obj/s
 * Reqs: Avg: 820.8ms, 50%: 440.9ms, 90%: 2039.2ms, 99%: 3814.4ms, Fastest: 98.4ms, Slowest: 9731.4ms, StdDev: 851.1ms
 * TTFB: Avg: 779ms, Best: 77ms, 25th: 309ms, Median: 417ms, 75th: 944ms, 90th: 1.98s, 99th: 3.615s, Worst: 9.717s StdDev: 821ms

Throughput, split into 57 x 1s:
 * Fastest: 28.7MiB/s, 28.70 obj/s
 * 50% Median: 22.1MiB/s, 22.07 obj/s
 * Slowest: 5.9MiB/s, 5.94 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 57s
 * Average: 41.54 obj/s
 * Reqs: Avg: 45.2ms, 50%: 30.6ms, 90%: 77.0ms, 99%: 260.4ms, Fastest: 4.6ms, Slowest: 1062.8ms, StdDev: 55.7ms

Throughput, split into 57 x 1s:
 * Fastest: 54.77 obj/s
 * 50% Median: 45.05 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 57s
 * Average: 83.04 MiB/s, 138.37 obj/s

Throughput, split into 57 x 1s:
 * Fastest: 98.4MiB/s, 170.86 obj/s
 * 50% Median: 94.1MiB/s, 150.04 obj/s
 * Slowest: 19.7MiB/s, 19.73 obj/s
```

## Mixed - 1MiB object size - in-cluster

```bash
 warp mixed --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 1m
```

```bash
Reqs: 25985, Errs:0, Objs:25985, Bytes: 15.22GiB
 -    DELETE Average: 42 Obj/s; Current 36 Obj/s, 6.9 ms/req
 -       GET Average: 186 Obj/s, 186.0MiB/s; Current 30 Obj/s, 30.0MiB/s, 200.3 ms/req, TTFB: 198.4ms
 -       PUT Average: 63 Obj/s, 62.8MiB/s; Current 56 Obj/s, 56.2MiB/s, 725.6 ms/req, TTFB: 720.8ms
 -      STAT Average: 126 Obj/s; Current 108 Obj/s, 4.2 ms/req


Report: DELETE. Concurrency: 64. Ran: 57s
 * Average: 42.05 obj/s
 * Reqs: Avg: 23.4ms, 50%: 18.0ms, 90%: 35.3ms, 99%: 217.4ms, Fastest: 2.2ms, Slowest: 1054.2ms, StdDev: 29.7ms

Throughput, split into 57 x 1s:
 * Fastest: 117.29 obj/s
 * 50% Median: 40.10 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 59s
 * Average: 186.01 MiB/s, 186.01 obj/s
 * Reqs: Avg: 308.6ms, 50%: 214.8ms, 90%: 658.3ms, 99%: 2292.7ms, Fastest: 2.1ms, Slowest: 5507.6ms, StdDev: 434.2ms
 * TTFB: Avg: 301ms, Best: 1ms, 25th: 125ms, Median: 207ms, 75th: 318ms, 90th: 649ms, 99th: 2.282s, Worst: 5.506s StdDev: 434ms

Throughput, split into 59 x 1s:
 * Fastest: 512.7MiB/s, 512.65 obj/s
 * 50% Median: 174.4MiB/s, 174.45 obj/s
 * Slowest: 9.7MiB/s, 9.68 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 1m0s
 * Average: 61.01 MiB/s, 61.01 obj/s
 * Reqs: Avg: 658.0ms, 50%: 447.8ms, 90%: 1475.0ms, 99%: 4059.8ms, Fastest: 17.9ms, Slowest: 6005.9ms, StdDev: 784.6ms
 * TTFB: Avg: 653ms, Best: 13ms, 25th: 262ms, Median: 443ms, 75th: 702ms, 90th: 1.469s, 99th: 4.054s, Worst: 6.002s StdDev: 785ms

Throughput, split into 60 x 1s:
 * Fastest: 177.4MiB/s, 177.43 obj/s
 * 50% Median: 54.3MiB/s, 54.35 obj/s
 * Slowest: 4.0MiB/s, 3.96 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 57s
 * Average: 126.10 obj/s
 * Reqs: Avg: 15.6ms, 50%: 12.1ms, 90%: 25.3ms, 99%: 66.3ms, Fastest: 1.1ms, Slowest: 1079.8ms, StdDev: 23.2ms

Throughput, split into 57 x 1s:
 * Fastest: 337.41 obj/s
 * 50% Median: 122.09 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 1m0s
 * Average: 244.00 MiB/s, 406.49 obj/s

Throughput, split into 60 x 1s:
 * Fastest: 690.1MiB/s, 1144.78 obj/s
 * 50% Median: 225.4MiB/s, 389.39 obj/s
 * Slowest: 9.8MiB/s, 9.80 obj/s
```

## Get - 1MiB object size - in-cluster


```bash
 warp get --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 1m \
  --insecure
```

```
Reqs: 40092, Errs:0, Objs:40092, Bytes: 39.15GiB
 -       GET Average: 679 Obj/s, 679.4MiB/s; Current 675 Obj/s, 675.3MiB/s, 97.6 ms/req, TTFB: 62.0ms


Report: GET. Concurrency: 64. Ran: 57s
 * Average: 677.67 MiB/s, 677.67 obj/s
 * Reqs: Avg: 94.7ms, 50%: 93.6ms, 90%: 127.7ms, 99%: 219.2ms, Fastest: 13.7ms, Slowest: 1345.0ms, StdDev: 50.9ms
 * TTFB: Avg: 61ms, Best: 8ms, 25th: 42ms, Median: 61ms, 75th: 75ms, 90th: 90ms, 99th: 182ms, Worst: 1.292s StdDev: 49ms

Throughput, split into 57 x 1s:
 * Fastest: 764.1MiB/s, 764.09 obj/s
 * 50% Median: 676.6MiB/s, 676.57 obj/s
 * Slowest: 580.9MiB/s, 580.94 obj/s

```

## Get - 128MiB object size - in-cluster


```bash
 warp get --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --objects 50 \
  --obj.size 128MiB \
  --duration 1m \
  --insecure
```

```
Reqs: 47, Errs:0, Objs:47, Bytes: 6016.0MiB
 -       GET Average: 1 Obj/s, 94.5MiB/s; Current 0 Obj/s, 56.8MiB/s, 8002.7 ms/req, TTFB: 825.0ms


Report: GET. Concurrency: 8. Ran: 1m4s
 * Average: 97.89 MiB/s, 0.76 obj/s
 * Reqs: Avg: 10345.1ms, 50%: 10864.0ms, 90%: 13326.3ms, 99%: 13326.3ms, Fastest: 2651.7ms, Slowest: 16054.9ms, StdDev: 2111.4ms
 * TTFB: Avg: 958ms, Best: 11ms, 25th: 800ms, Median: 1.021s, 75th: 1.282s, 90th: 1.47s, 99th: 1.47s, Worst: 1.789s StdDev: 338ms

Throughput, split into 64 x 1s:
 * Fastest: 137.7MiB/s, 1.08 obj/s
 * 50% Median: 94.2MiB/s, 0.74 obj/s
 * Slowest: 50.7MiB/s, 0.40 obj/s
```

## Put - 1MiB object size - in-cluster

```bash
 warp put --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --obj.size 1MiB \
  --duration 1m \
  --insecure
```

```
Reqs: 6945, Errs:0, Objs:6945, Bytes: 6945.0MiB
 -       PUT Average: 102 Obj/s, 102.0MiB/s; Current 0 Obj/s, 240.0KiB/s, 679.5 ms/req, TTFB: 673.6ms


Report: PUT. Concurrency: 64. Ran: 1m1s
 * Average: 101.09 MiB/s, 101.09 obj/s
 * Reqs: Avg: 1112.9ms, 50%: 719.7ms, 90%: 1364.0ms, 99%: 6186.0ms, Fastest: 34.8ms, Slowest: 9700.3ms, StdDev: 1325.0ms
 * TTFB: Avg: 1.107s, Best: 30ms, 25th: 676ms, Median: 714ms, 75th: 779ms, 90th: 1.358s, 99th: 6.18s, Worst: 9.695s StdDev: 1.325s

Throughput, split into 61 x 1s:
 * Fastest: 492.2MiB/s, 492.17 obj/s
 * 50% Median: 12.3MiB/s, 12.30 obj/s
 * Slowest: 6.9MiB/s, 6.94 obj/s

```


## Put - 128MiB object size - in-cluster

```bash
 warp put --host seaweedfs-s3.storage.svc.cluster.local:8333 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --obj.size 128MiB \
  --duration 1m \
  --insecure
```

```
Reqs: 47, Errs:0, Objs:47, Bytes: 6016.0MiB
 -       PUT Average: 1 Obj/s, 99.0MiB/s; Current 0 Obj/s, 11.3MiB/s, 9200.4 ms/req, TTFB: 5334.2ms


Report: PUT. Concurrency: 8. Ran: 1m0s
 * Average: 111.59 MiB/s, 0.87 obj/s
 * Reqs: Avg: 9931.0ms, 50%: 10747.6ms, 90%: 11528.4ms, 99%: 11547.3ms, Fastest: 1359.4ms, Slowest: 14584.6ms, StdDev: 1619.8ms
 * TTFB: Avg: 4.721s, Best: 199ms, 25th: 3.5s, Median: 5.785s, 75th: 7.188s, 90th: 7.396s, 99th: 7.412s, Worst: 8.616s StdDev: 2.689s

Throughput, split into 60 x 1s:
 * Fastest: 293.4MiB/s, 2.29 obj/s
 * 50% Median: 95.1MiB/s, 0.74 obj/s
 * Slowest: 77.1MiB/s, 0.60 obj/s

```
