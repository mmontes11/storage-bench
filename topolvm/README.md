# kbench

```
TEST_FILE: /volume/test
TEST_OUTPUT_PREFIX: test_device
TEST_SIZE: 30G
Benchmarking iops.fio into test_device-iops.json
Benchmarking bandwidth.fio into test_device-bandwidth.json
Benchmarking latency.fio into test_device-latency.json

=========================
FIO Benchmark Summary
For: test_device
CPU Idleness Profiling: disabled
Size: 30G
Quick Mode: disabled
=========================
IOPS (Read/Write)
        Random:        204,584 / 164,652
    Sequential:        193,306 / 168,166

Bandwidth in KiB/sec (Read/Write)
        Random:    2,508,280 / 5,228,858
    Sequential:    5,923,913 / 5,738,163
                                        

Latency in ns (Read/Write)
        Random:          62,190 / 27,418
    Sequential:          25,266 / 28,275
``` 

# dbench

```
Working dir: /data

Testing Read IOPS...
read_iops: (g=0): rw=randread, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process
read_iops: Laying out IO file(s) (1 file(s) / 2048MiB)

read_iops: (groupid=0, jobs=1): err= 0: pid=9: Mon Jul 28 19:25:57 2025
   read: IOPS=203k, BW=794MiB/s (833MB/s)(11.7GiB/15001msec)
  cpu          : usr=30.24%, sys=69.53%, ctx=669, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.6%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=3050899,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=794MiB/s (833MB/s), 794MiB/s-794MiB/s (833MB/s-833MB/s), io=11.7GiB (12.5GB), run=15001-15001msec

Disk stats (read/write):
    dm-1: ios=3465877/0, merge=0/0, ticks=220184/0, in_queue=220184, util=99.64%, aggrios=3465918/0, aggrmerge=0/0, aggrticks=228366/0, aggrin_queue=228367, aggrutil=99.33%
  sdb: ios=3465918/0, merge=0/0, ticks=228366/0, in_queue=228367, util=99.33%


Testing Write IOPS...
write_iops: (g=0): rw=randwrite, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

write_iops: (groupid=0, jobs=1): err= 0: pid=17: Mon Jul 28 19:26:14 2025
  write: IOPS=167k, BW=653MiB/s (685MB/s)(9797MiB/15001msec)
  cpu          : usr=22.85%, sys=62.47%, ctx=1028618, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=112.7%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=0,2507973,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
  WRITE: bw=653MiB/s (685MB/s), 653MiB/s-653MiB/s (685MB/s-685MB/s), io=9797MiB (10.3GB), run=15001-15001msec

Disk stats (read/write):
    dm-1: ios=0/2827676, merge=0/0, ticks=0/61780, in_queue=61780, util=99.32%, aggrios=0/2827717, aggrmerge=0/5, aggrticks=0/67068, aggrin_queue=67070, aggrutil=99.09%
  sdb: ios=0/2827717, merge=0/5, ticks=0/67068, in_queue=67070, util=99.09%


Testing Read Bandwidth...
read_bw: (g=0): rw=randread, bs=128KiB-128KiB,128KiB-128KiB,128KiB-128KiB, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

read_bw: (groupid=0, jobs=1): err= 0: pid=25: Mon Jul 28 19:26:32 2025
   read: IOPS=9755, BW=1220MiB/s (1279MB/s)(17.1GiB/15010msec)
  cpu          : usr=1.69%, sys=6.34%, ctx=139658, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=146427,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=1220MiB/s (1279MB/s), 1220MiB/s-1220MiB/s (1279MB/s-1279MB/s), io=17.1GiB (19.2GB), run=15010-15010msec

Disk stats (read/write):
    dm-1: ios=165408/2, merge=0/0, ticks=1087252/12, in_queue=1087264, util=99.65%, aggrios=165502/2, aggrmerge=0/0, aggrticks=1087621/12, aggrin_queue=1087646, aggrutil=99.36%
  sdb: ios=165502/2, merge=0/0, ticks=1087621/12, in_queue=1087646, util=99.36%


Testing Write Bandwidth...
write_bw: (g=0): rw=randwrite, bs=128KiB-128KiB,128KiB-128KiB,128KiB-128KiB, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

write_bw: (groupid=0, jobs=1): err= 0: pid=33: Mon Jul 28 19:26:49 2025
  write: IOPS=44.7k, BW=5579MiB/s (5850MB/s)(81.8GiB/15002msec)
  cpu          : usr=10.75%, sys=27.45%, ctx=664112, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.1%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=0,669541,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
  WRITE: bw=5579MiB/s (5850MB/s), 5579MiB/s-5579MiB/s (5850MB/s-5850MB/s), io=81.8GiB (87.8GB), run=15002-15002msec

Disk stats (read/write):
    dm-1: ios=0/757480, merge=0/0, ticks=0/1083268, in_queue=1083268, util=99.60%, aggrios=4/757566, aggrmerge=0/0, aggrticks=20/1080898, aggrin_queue=1080917, aggrutil=99.27%
  sdb: ios=4/757566, merge=0/0, ticks=20/1080898, in_queue=1080917, util=99.27%


Testing Read Latency...
read_latency: (g=0): rw=randread, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=4
fio-2.17-45-g06cb
Starting 1 process

read_latency: (groupid=0, jobs=1): err= 0: pid=41: Mon Jul 28 19:27:06 2025
   read: IOPS=60.9k, BW=238MiB/s (249MB/s)(3566MiB/15001msec)
    slat (usec): min=1, max=248, avg= 2.83, stdev= 1.16
    clat (usec): min=21, max=1455, avg=62.64, stdev=12.39
     lat (usec): min=33, max=1468, avg=65.47, stdev=12.44
    clat percentiles (usec):
     |  1.00th=[   50],  5.00th=[   53], 10.00th=[   55], 20.00th=[   57],
     | 30.00th=[   58], 40.00th=[   60], 50.00th=[   61], 60.00th=[   63],
     | 70.00th=[   64], 80.00th=[   67], 90.00th=[   71], 95.00th=[   76],
     | 99.00th=[   89], 99.50th=[   97], 99.90th=[  278], 99.95th=[  302],
     | 99.99th=[  350]
    lat (usec) : 50=0.75%, 100=98.82%, 250=0.28%, 500=0.14%, 750=0.01%
    lat (msec) : 2=0.01%
  cpu          : usr=7.02%, sys=23.87%, ctx=482138, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=113.3%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=912893,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
   READ: bw=238MiB/s (249MB/s), 238MiB/s-238MiB/s (249MB/s-249MB/s), io=3566MiB (3739MB), run=15001-15001msec

Disk stats (read/write):
    dm-1: ios=1034377/2, merge=0/0, ticks=64292/0, in_queue=64292, util=99.26%, aggrios=1034377/2, aggrmerge=0/0, aggrticks=62253/1, aggrin_queue=62255, aggrutil=98.97%
  sdb: ios=1034377/2, merge=0/0, ticks=62253/1, in_queue=62255, util=98.97%


Testing Write Latency...
write_latency: (g=0): rw=randwrite, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=4
fio-2.17-45-g06cb
Starting 1 process

write_latency: (groupid=0, jobs=1): err= 0: pid=49: Mon Jul 28 19:27:24 2025
  write: IOPS=127k, BW=496MiB/s (520MB/s)(7442MiB/15001msec)
    slat (usec): min=1, max=135, avg= 3.64, stdev= 1.82
    clat (usec): min=0, max=4239, avg=27.58, stdev=20.04
     lat (usec): min=15, max=4242, avg=31.19, stdev=20.05
    clat percentiles (usec):
     |  1.00th=[   17],  5.00th=[   20], 10.00th=[   21], 20.00th=[   23],
     | 30.00th=[   25], 40.00th=[   26], 50.00th=[   27], 60.00th=[   28],
     | 70.00th=[   29], 80.00th=[   31], 90.00th=[   33], 95.00th=[   36],
     | 99.00th=[   43], 99.50th=[   49], 99.90th=[  171], 99.95th=[  207],
     | 99.99th=[  282]
    lat (usec) : 2=0.01%, 4=0.01%, 10=0.01%, 20=4.40%, 50=95.13%
    lat (usec) : 100=0.31%, 250=0.15%, 500=0.01%, 750=0.01%, 1000=0.01%
    lat (msec) : 2=0.01%, 4=0.01%, 10=0.01%
  cpu          : usr=18.02%, sys=39.86%, ctx=975120, majf=0, minf=2
  IO depths    : 1=0.1%, 2=0.1%, 4=112.8%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,1905080,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
  WRITE: bw=496MiB/s (520MB/s), 496MiB/s-496MiB/s (520MB/s-520MB/s), io=7442MiB (7803MB), run=15001-15001msec

Disk stats (read/write):
    dm-1: ios=0/2118919, merge=0/0, ticks=0/42808, in_queue=42808, util=97.59%, aggrios=0/2149648, aggrmerge=0/0, aggrticks=0/45700, aggrin_queue=45700, aggrutil=97.13%
  sdb: ios=0/2149648, merge=0/0, ticks=0/45700, in_queue=45700, util=97.13%


Testing Read Sequential Speed...
read_seq: (g=0): rw=read, bs=1024KiB-1024KiB,1024KiB-1024KiB,1024KiB-1024KiB, ioengine=libaio, iodepth=16
...
fio-2.17-45-g06cb
Starting 4 threads
read_seq: Laying out IO file(s) (1 file(s) / 3548MiB)

read_seq: (groupid=0, jobs=1): err= 0: pid=57: Mon Jul 28 19:27:42 2025
   read: IOPS=1481, BW=1483MiB/s (1555MB/s)(21.8GiB/15038msec)
  cpu          : usr=0.13%, sys=2.51%, ctx=22065, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=111.6%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=22286,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
read_seq: (groupid=0, jobs=1): err= 0: pid=58: Mon Jul 28 19:27:42 2025
   read: IOPS=1488, BW=1490MiB/s (1562MB/s)(21.9GiB/15037msec)
  cpu          : usr=0.03%, sys=2.68%, ctx=22167, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=111.7%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=22389,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
read_seq: (groupid=0, jobs=1): err= 0: pid=59: Mon Jul 28 19:27:42 2025
   read: IOPS=1487, BW=1488MiB/s (1561MB/s)(21.9GiB/15037msec)
  cpu          : usr=0.15%, sys=2.77%, ctx=22117, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=111.5%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=22364,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
read_seq: (groupid=0, jobs=1): err= 0: pid=60: Mon Jul 28 19:27:42 2025
   read: IOPS=1486, BW=1487MiB/s (1560MB/s)(21.9GiB/15038msec)
  cpu          : usr=0.13%, sys=2.59%, ctx=22149, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=111.6%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=22352,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16

Run status group 0 (all jobs):
   READ: bw=5948MiB/s (6237MB/s), 1483MiB/s-1490MiB/s (1555MB/s-1562MB/s), io=87.4GiB (93.8GB), run=15037-15038msec

Disk stats (read/write):
    dm-1: ios=99615/9, merge=0/0, ticks=1086048/48, in_queue=1086096, util=99.62%, aggrios=99836/5, aggrmerge=0/4, aggrticks=1088702/34, aggrin_queue=1088739, aggrutil=99.21%
  sdb: ios=99836/5, merge=0/4, ticks=1088702/34, in_queue=1088739, util=99.21%


Testing Write Sequential Speed...
write_seq: (g=0): rw=write, bs=1024KiB-1024KiB,1024KiB-1024KiB,1024KiB-1024KiB, ioengine=libaio, iodepth=16
...
fio-2.17-45-g06cb
Starting 4 threads

write_seq: (groupid=0, jobs=1): err= 0: pid=68: Mon Jul 28 19:28:00 2025
  write: IOPS=1462, BW=1464MiB/s (1535MB/s)(21.5GiB/15024msec)
  cpu          : usr=8.85%, sys=1.45%, ctx=21639, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.2%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,21979,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
write_seq: (groupid=0, jobs=1): err= 0: pid=69: Mon Jul 28 19:28:00 2025
  write: IOPS=1361, BW=1363MiB/s (1429MB/s)(20.2GiB/15036msec)
  cpu          : usr=8.43%, sys=1.66%, ctx=19697, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.4%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,20479,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
write_seq: (groupid=0, jobs=1): err= 0: pid=70: Mon Jul 28 19:28:00 2025
  write: IOPS=1472, BW=1473MiB/s (1545MB/s)(21.7GiB/15036msec)
  cpu          : usr=8.81%, sys=1.49%, ctx=21796, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.2%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,22138,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
write_seq: (groupid=0, jobs=1): err= 0: pid=71: Mon Jul 28 19:28:00 2025
  write: IOPS=1467, BW=1468MiB/s (1540MB/s)(21.6GiB/15024msec)
  cpu          : usr=8.96%, sys=1.50%, ctx=21703, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.4%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,22045,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16

Run status group 0 (all jobs):
  WRITE: bw=5766MiB/s (6046MB/s), 1363MiB/s-1473MiB/s (1429MB/s-1545MB/s), io=84.7GiB (90.1GB), run=15024-15036msec

Disk stats (read/write):
    dm-1: ios=0/98022, merge=0/0, ticks=0/1079756, in_queue=1079756, util=99.57%, aggrios=0/119917, aggrmerge=0/0, aggrticks=0/1293008, aggrin_queue=1293008, aggrutil=99.18%
  sdb: ios=0/119917, merge=0/0, ticks=0/1293008, in_queue=1293008, util=99.18%


Testing Read/Write Mixed...
rw_mix: (g=0): rw=randrw, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

rw_mix: (groupid=0, jobs=1): err= 0: pid=79: Mon Jul 28 19:28:17 2025
   read: IOPS=139k, BW=544MiB/s (570MB/s)(8157MiB/15001msec)
  write: IOPS=46.6k, BW=182MiB/s (191MB/s)(2725MiB/15001msec)
  cpu          : usr=24.93%, sys=66.48%, ctx=517864, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.3%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=2088043,697704,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=544MiB/s (570MB/s), 544MiB/s-544MiB/s (570MB/s-570MB/s), io=8157MiB (8553MB), run=15001-15001msec
  WRITE: bw=182MiB/s (191MB/s), 182MiB/s-182MiB/s (191MB/s-191MB/s), io=2725MiB (2858MB), run=15001-15001msec

Disk stats (read/write):
    dm-1: ios=2365383/790139, merge=0/0, ticks=152668/16548, in_queue=169216, util=99.61%, aggrios=2365383/790139, aggrmerge=0/0, aggrticks=156769/17681, aggrin_queue=174452, aggrutil=99.28%
  sdb: ios=2365383/790139, merge=0/0, ticks=156769/17681, in_queue=174452, util=99.28%


All tests complete.

==================
= Dbench Summary =
==================
Random Read/Write IOPS: 203k/167k. BW: 1220MiB/s / 5579MiB/s
Average Latency (usec) Read/Write: 65.47/31.19
Sequential Read/Write: 5948MiB/s / 5766MiB/s
Mixed Random Read/Write IOPS: 139k/46.6k
```
