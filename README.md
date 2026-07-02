# storage-bench
Storage benchmarks in Kubernetes


### Benchmarks

- [topolvm](./topolvm)
- [synology](./synology)
- [minio-synology](./minio-synology)
- [seaweedfs-synology](./seaweedfs-synology)

### Kubernetes cluster

Provisioned by [k8s-management](https://github.com/mmontes11/k8s-management) and [k8s-infrastructure](https://github.com/mmontes11/k8s-infrastructure)

### MinIO vs SeaweedFS on Synology

Both use the same Synology CSI-backed storage class and the same `warp` commands, run one after the other. [MinIO](./minio-synology) is a single process (control plane + S3 API + data) running entirely on `compute-large` nodes. [SeaweedFS](./seaweedfs-synology) splits master/filer/admin onto cheap `default` nodes and only volume+S3 onto `compute-large`, with a MariaDB-backed filer for metadata.

#### Mixed - 1MiB object size - external

Throughput:

| Operation | MinIO | SeaweedFS |
|-----------|-------|-----------|
| DELETE | 13.80 obj/s | 13.79 obj/s |
| GET | 61.54 MiB/s, 61.54 obj/s | 62.42 MiB/s, 62.42 obj/s |
| PUT | 20.81 MiB/s, 20.81 obj/s | 20.62 MiB/s, 20.62 obj/s |
| STAT | 41.20 obj/s | 41.54 obj/s |
| **Total** | **82.36 MiB/s, 137.35 obj/s** | **83.04 MiB/s, 138.37 obj/s** |

Latency (p50 / p99):

| Operation | MinIO | SeaweedFS |
|-----------|-------|-----------|
| DELETE | 60.8ms / 630.6ms | 39.1ms / 335.9ms |
| GET | 269.9ms / 1480.4ms | 390.2ms / 3882.7ms |
| PUT | 1944.2ms / 3234.1ms | 440.9ms / 3814.4ms |
| STAT | 16.3ms / 213.8ms | 30.6ms / 260.4ms |

#### Mixed - 1MiB object size - in-cluster

Throughput:

| Operation | MinIO | SeaweedFS |
|-----------|-------|-----------|
| DELETE | 29.96 obj/s | 42.05 obj/s |
| GET | 135.59 MiB/s, 135.59 obj/s | 186.01 MiB/s, 186.01 obj/s |
| PUT | 45.12 MiB/s, 45.12 obj/s | 61.01 MiB/s, 61.01 obj/s |
| STAT | 90.24 obj/s | 126.10 obj/s |
| **Total** | **180.71 MiB/s, 300.92 obj/s** | **244.00 MiB/s, 406.49 obj/s** |

Latency (p50 / p99):

| Operation | MinIO | SeaweedFS |
|-----------|-------|-----------|
| DELETE | 59.2ms / 848.8ms | 18.0ms / 217.4ms |
| GET | 71.5ms / 302.5ms | 214.8ms / 2292.7ms |
| PUT | 960.7ms / 2016.9ms | 447.8ms / 4059.8ms |
| STAT | 7.5ms / 121.3ms | 12.1ms / 66.3ms |

#### Get - 1MiB object size - in-cluster

| Metric | MinIO | SeaweedFS |
|--------|-------|-----------|
| Avg throughput | 854.24 MiB/s, 854.24 obj/s | 677.67 MiB/s, 677.67 obj/s |
| p50 latency | 73.0ms | 93.6ms |
| p99 latency | 110.2ms | 219.2ms |
| TTFB avg | 9ms | 61ms |

#### Get - 128MiB object size - in-cluster

| Metric | MinIO | SeaweedFS |
|--------|-------|-----------|
| Avg throughput | 175.20 MiB/s, 1.37 obj/s | 97.89 MiB/s, 0.76 obj/s |
| p50 latency | 6931.7ms | 10864.0ms |
| p99 latency | 8442.5ms | 13326.3ms |
| TTFB avg | 291ms | 958ms |

#### Put - 1MiB object size - in-cluster

| Metric | MinIO | SeaweedFS |
|--------|-------|-----------|
| Avg throughput | 98.86 MiB/s, 98.86 obj/s | 101.09 MiB/s, 101.09 obj/s |
| p50 latency | 703.2ms | 719.7ms |
| p99 latency | 1182.8ms | 6186.0ms |
| TTFB avg | 715ms | 1.107s |

#### Put - 128MiB object size - in-cluster

| Metric | MinIO | SeaweedFS |
|--------|-------|-----------|
| Avg throughput | 104.44 MiB/s, 0.82 obj/s | 111.59 MiB/s, 0.87 obj/s |
| p50 latency | 9432.1ms | 10747.6ms |
| p99 latency | 10286.2ms | 11547.3ms |
| TTFB avg | 4.066s | 4.721s |

#### Summary

The two setups embody opposite architectural bets, and the numbers track that:

- **External (1Gbps-bound) mixed workload:** near-identical, since the network link — not the storage backend — is the bottleneck. Architecture only shows up once you look inside the cluster.
- **In-cluster mixed workload:** SeaweedFS pulls ahead on raw throughput (~35% more MiB/s and obj/s than MinIO). SeaweedFS's volume+S3 pods get 1000m CPU/8Gi combined on `compute-large`, twice MinIO's single 1000m/4Gi process — splitting the data plane into dedicated pods lets it claim more of the large-node budget than MinIO's one-process pod. The cost is tail latency: SeaweedFS's p99s and StdDevs run 2-4x higher than MinIO's, from the extra S3-gateway → volume-server hop and filer round-trips to MariaDB.
- **Pure single-op GET (1MiB and 128MiB):** MinIO wins outright (~26% faster at 1MiB, ~79% faster at 128MiB) and has much lower TTFB (9ms vs 61ms; 291ms vs 958ms). With no separate gateway/volume hop, MinIO serves reads directly from the process holding the data — SeaweedFS pays a network hop and metadata lookup on every read.
- **Pure single-op PUT (1MiB and 128MiB):** average throughput converges (SeaweedFS is marginally ahead), since writes are bottlenecked by the same Synology CSI disk underneath either way. But SeaweedFS's tail is markedly worse — 1MiB PUT p99 is 6186ms vs MinIO's 1182ms — again the filer/volume coordination cost surfacing under load.

Net: SeaweedFS's distributed layout (control plane on cheap default nodes, data plane split across dedicated volume/S3 pods) buys higher aggregate throughput under concurrent mixed load and frees the large nodes' resources from control-plane work, at the cost of extra network/metadata hops that show up as higher and less predictable tail latency. MinIO's monolith has no internal hops to pay for, so it wins on raw single-stream throughput and latency predictability, but ties control and data plane to the same node class with no independent scaling. Which one wins depends on whether the workload cares more about sustained concurrent throughput (SeaweedFS) or single-request latency and predictability (MinIO).