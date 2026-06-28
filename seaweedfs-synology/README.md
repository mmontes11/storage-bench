### Storage

SeaweedFS object storage on [Synology CSI driver](../synology/).

### Credentials

```bash
export S3_ACCESS_KEY=<access-key>
export S3_SECRET_KEY=<access-secret-key>
```

# Scenario 1: all components running in compute-large

All SeaweedFS components are scheduled on `compute-large` nodes via nodeSelector and tolerations.

## Environment

Deployed via [SeaweedFS Operator](https://github.com/seaweedfs/seaweedfs-operator) with the CR at [seaweed.yaml](https://github.com/mmontes11/k8s-infrastructure/blob/824b47b8ba07341d51908ac504d8e206aeef9e27/infrastructure/seaweedfs-operator/seaweedfs/seaweed.yaml).

| Component | Replicas | CPU Request | Memory Request | Memory Limit | Node Selector |
|-----------|----------|-------------|----------------|--------------|---------------|
| master | 1 | 100m | 256Mi | 512Mi | compute-large |
| filer | 1 | 200m | 256Mi | 1Gi | compute-large |
| volume | 1 | 500m | 1Gi | 4Gi | compute-large |
| s3 | 1 | 500m | 1Gi | 4Gi | compute-large |
| admin | 1 | 100m | 128Mi | 512Mi | compute-large |

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
Reqs: 33538, Errs:0, Objs:33538, Bytes: 19.64GiB
 -    DELETE Average: 11 Obj/s; Current 11 Obj/s, 27.4 ms/req
 -       GET Average: 50 Obj/s, 50.4MiB/s; Current 47 Obj/s, 47.3MiB/s, 1098.9 ms/req, TTFB: 456.4ms
 -       PUT Average: 17 Obj/s, 16.8MiB/s; Current 17 Obj/s, 17.1MiB/s, 1234.6 ms/req, TTFB: 1230.6ms
 -      STAT Average: 34 Obj/s; Current 51 Obj/s, 30.2 ms/req


Report: DELETE. Concurrency: 64. Ran: 4m57s
 * Average: 11.21 obj/s
 * Reqs: Avg: 27.8ms, 50%: 13.4ms, 90%: 49.6ms, 99%: 303.5ms, Fastest: 3.3ms, Slowest: 446.3ms, StdDev: 54.7ms

Throughput, split into 297 x 1s:
 * Fastest: 25.00 obj/s
 * 50% Median: 11.88 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 4m58s
 * Average: 50.41 MiB/s, 50.41 obj/s
 * Reqs: Avg: 952.5ms, 50%: 486.5ms, 90%: 2656.0ms, 99%: 5107.0ms, Fastest: 45.2ms, Slowest: 9632.9ms, StdDev: 1114.3ms
 * TTFB: Avg: 159ms, Best: 2ms, 25th: 12ms, Median: 20ms, 75th: 63ms, 90th: 412ms, 99th: 2.102s, Worst: 6.334s StdDev: 429ms

Throughput, split into 298 x 1s:
 * Fastest: 78.3MiB/s, 78.26 obj/s
 * 50% Median: 54.4MiB/s, 54.35 obj/s
 * Slowest: 7.0MiB/s, 6.99 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 4m58s
 * Average: 16.79 MiB/s, 16.79 obj/s
 * Reqs: Avg: 1049.2ms, 50%: 480.3ms, 90%: 2961.0ms, 99%: 5423.3ms, Fastest: 104.4ms, Slowest: 8377.9ms, StdDev: 1187.0ms
 * TTFB: Avg: 1.029s, Best: 82ms, 25th: 338ms, Median: 464ms, 75th: 1.261s, 90th: 2.93s, 99th: 5.378s, Worst: 8.372s StdDev: 1.178s

Throughput, split into 298 x 1s:
 * Fastest: 34.7MiB/s, 34.71 obj/s
 * 50% Median: 17.1MiB/s, 17.14 obj/s
 * Slowest: 4.4MiB/s, 4.45 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 4m57s
 * Average: 33.66 obj/s
 * Reqs: Avg: 25.2ms, 50%: 11.5ms, 90%: 22.1ms, 99%: 240.3ms, Fastest: 2.3ms, Slowest: 5049.6ms, StdDev: 59.5ms

Throughput, split into 297 x 1s:
 * Fastest: 71.79 obj/s
 * 50% Median: 33.82 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 4m58s
 * Average: 67.20 MiB/s, 112.04 obj/s

Throughput, split into 298 x 1s:
 * Fastest: 94.7MiB/s, 184.45 obj/s
 * 50% Median: 73.6MiB/s, 118.71 obj/s
 * Slowest: 14.2MiB/s, 14.16 obj/s
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
Reqs: 65676, Errs:0, Objs:65676, Bytes: 38.47GiB
 -    DELETE Average: 22 Obj/s; Current 4 Obj/s, 4.6 ms/req
 -       GET Average: 98 Obj/s, 97.6MiB/s; Current 18 Obj/s, 17.9MiB/s, 402.9 ms/req, TTFB: 400.6ms
 -       PUT Average: 33 Obj/s, 32.5MiB/s; Current 4 Obj/s, 4.5MiB/s, 1345.2 ms/req, TTFB: 1339.9ms
 -      STAT Average: 65 Obj/s; Current 29 Obj/s, 2.2 ms/req


Report: DELETE. Concurrency: 64. Ran: 4m57s
 * Average: 21.73 obj/s
 * Reqs: Avg: 21.0ms, 50%: 3.2ms, 90%: 8.7ms, 99%: 814.3ms, Fastest: 1.1ms, Slowest: 5033.7ms, StdDev: 122.2ms

Throughput, split into 297 x 1s:
 * Fastest: 92.40 obj/s
 * 50% Median: 18.00 obj/s
 * Slowest: 0.00 obj/s

──────────────────────────────────

Report: GET. Concurrency: 64. Ran: 5m0s
 * Average: 96.99 MiB/s, 96.99 obj/s
 * Reqs: Avg: 458.4ms, 50%: 278.5ms, 90%: 955.6ms, 99%: 3994.3ms, Fastest: 1.4ms, Slowest: 8298.0ms, StdDev: 744.3ms
 * TTFB: Avg: 456ms, Best: 1ms, 25th: 85ms, Median: 276ms, 75th: 496ms, 90th: 952ms, 99th: 3.991s, Worst: 8.296s StdDev: 744ms

Throughput, split into 300 x 1s:
 * Fastest: 404.7MiB/s, 404.67 obj/s
 * 50% Median: 78.5MiB/s, 78.50 obj/s
 * Slowest: 4.6MiB/s, 4.62 obj/s

──────────────────────────────────

Report: PUT. Concurrency: 64. Ran: 5m0s
 * Average: 32.37 MiB/s, 32.37 obj/s
 * Reqs: Avg: 1227.8ms, 50%: 656.7ms, 90%: 3494.6ms, 99%: 6213.1ms, Fastest: 13.2ms, Slowest: 10362.4ms, StdDev: 1519.3ms
 * TTFB: Avg: 1.223s, Best: 10ms, 25th: 291ms, Median: 652ms, 75th: 1.551s, 90th: 3.489s, 99th: 6.208s, Worst: 10.358s StdDev: 1.519s

Throughput, split into 300 x 1s:
 * Fastest: 133.3MiB/s, 133.29 obj/s
 * 50% Median: 25.1MiB/s, 25.06 obj/s
 * Slowest: 2.2MiB/s, 2.16 obj/s

──────────────────────────────────

Report: STAT. Concurrency: 64. Ran: 4m57s
 * Average: 65.23 obj/s
 * Reqs: Avg: 21.5ms, 50%: 1.6ms, 90%: 5.3ms, 99%: 904.4ms, Fastest: 0.5ms, Slowest: 5036.2ms, StdDev: 127.3ms

Throughput, split into 297 x 1s:
 * Fastest: 283.28 obj/s
 * 50% Median: 55.00 obj/s
 * Slowest: 0.00 obj/s


──────────────────────────────────

Report: Total. Concurrency: 64. Ran: 5m0s
 * Average: 129.36 MiB/s, 215.55 obj/s

Throughput, split into 300 x 1s:
 * Fastest: 538.0MiB/s, 913.65 obj/s
 * 50% Median: 104.9MiB/s, 175.70 obj/s
 * Slowest: 9.7MiB/s, 9.74 obj/s
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
Report: GET. Concurrency: 64. Ran: 5m1s
 * Average: 611.94 MiB/s, 611.94 obj/s
 * Reqs: Avg: 127.0ms, 50%: 40.3ms, 90%: 264.1ms, 99%: 2346.4ms, Fastest: 1.8ms, Slowest: 5617.2ms, StdDev: 501.7ms
 * TTFB: Avg: 110ms, Best: 1ms, 25th: 15ms, Median: 26ms, 75th: 51ms, 90th: 229ms, 99th: 2.313s, Worst: 5.57s StdDev: 498ms

Throughput, split into 301 x 1s:
 * Fastest: 771.0MiB/s, 771.00 obj/s
 * 50% Median: 619.7MiB/s, 619.67 obj/s
 * Slowest: 5.6MiB/s, 5.58 obj/s
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
Report: GET. Concurrency: 8. Ran: 5m7s
 * Average: 114.32 MiB/s, 0.89 obj/s
 * Reqs: Avg: 9050.6ms, 50%: 9549.3ms, 90%: 14286.8ms, 99%: 14286.8ms, Fastest: 279.5ms, Slowest: 19637.7ms, StdDev: 3298.3ms
 * TTFB: Avg: 1.979s, Best: 2ms, 25th: 1.559s, Median: 2.363s, 75th: 2.898s, 90th: 3.455s, 99th: 3.455s, Worst: 7.205s StdDev: 1.072s

Throughput, split into 307 x 1s:
 * Fastest: 265.5MiB/s, 2.07 obj/s
 * 50% Median: 108.6MiB/s, 0.85 obj/s
 * Slowest: 13.6MiB/s, 0.11 obj/s
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
Report: PUT. Concurrency: 64. Ran: 5m2s
 * Average: 100.41 MiB/s, 100.41 obj/s
 * Reqs: Avg: 694.8ms, 50%: 134.8ms, 90%: 2349.5ms, 99%: 5897.3ms, Fastest: 13.0ms, Slowest: 11724.2ms, StdDev: 1484.9ms
 * TTFB: Avg: 687ms, Best: 9ms, 25th: 55ms, Median: 127ms, 75th: 387ms, 90th: 2.341s, 99th: 5.888s, Worst: 11.719s StdDev: 1.484s

Throughput, split into 302 x 1s:
 * Fastest: 372.2MiB/s, 372.18 obj/s
 * 50% Median: 30.2MiB/s, 30.22 obj/s
 * Slowest: 7.6MiB/s, 7.56 obj/s
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
 * Average: 102.42 MiB/s, 0.80 obj/s
 * Reqs: Avg: 9959.3ms, 50%: 10469.5ms, 90%: 12662.9ms, 99%: 12662.9ms, Fastest: 722.4ms, Slowest: 22671.3ms, StdDev: 2179.8ms
 * TTFB: Avg: 4.979s, Best: 114ms, 25th: 3.08s, Median: 5.796s, 75th: 7.908s, 90th: 8.56s, 99th: 8.56s, Worst: 14.466s StdDev: 2.963s

Throughput, split into 301 x 1s:
 * Fastest: 254.5MiB/s, 1.99 obj/s
 * 50% Median: 96.8MiB/s, 0.76 obj/s
 * Slowest: 70.1MiB/s, 0.55 obj/s
```
