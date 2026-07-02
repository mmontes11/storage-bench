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
  --duration 5m \
  --tls
```

```
TODO
```

## Mixed - 1MiB object size - in-cluster

```bash
 warp mixed --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m
```

```
TODO
```

## Get - 1MiB object size - in-cluster

```bash
 warp get --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m
```

```
TODO
```

## Get - 128MiB object size - in-cluster

```bash
 warp get --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --objects 50 \
  --obj.size 128MiB \
  --duration 5m
```

```
TODO
```

## Put - 1MiB object size - in-cluster

```bash
 warp put --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 64 \
  --objects 1000 \
  --obj.size 1MiB \
  --duration 5m
```

```
TODO
```

## Put - 128MiB object size - in-cluster

```bash
 warp put --host minio.storage.svc.cluster.local:80 \
  --access-key $S3_ACCESS_KEY \
  --secret-key $S3_SECRET_KEY \
  --concurrent 8 \
  --objects 50 \
  --obj.size 128MiB \
  --duration 5m
```

```
TODO
```
