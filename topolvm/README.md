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
        Random:        125,752 / 109,999
    Sequential:        128,879 / 112,052

Bandwidth in KiB/sec (Read/Write)
        Random:    2,392,895 / 5,309,766
    Sequential:    6,098,926 / 5,847,082


Latency in ns (Read/Write)
        Random:          63,072 / 30,455
    Sequential:          27,277 / 29,329
``` 

# dbench

```
Working dir: /data

Testing Read IOPS...
read_iops: (g=0): rw=randread, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process
read_iops: Laying out IO file(s) (1 file(s) / 2048MiB)

read_iops: (groupid=0, jobs=1): err= 0: pid=9: Sat Aug  2 14:34:01 2025
   read: IOPS=142k, BW=556MiB/s (583MB/s)(8338MiB/15001msec)
  cpu          : usr=22.49%, sys=77.45%, ctx=208, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.5%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=2134416,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=556MiB/s (583MB/s), 556MiB/s-556MiB/s (583MB/s-583MB/s), io=8338MiB (8743MB), run=15001-15001msec

Disk stats (read/write):
    dm-4: ios=2423332/0, merge=0/0, ticks=151396/0, in_queue=151396, util=99.58%, aggrios=2423358/0, aggrmerge=0/0, aggrticks=149380/0, aggrin_queue=149380, aggrutil=99.29%
    dm-2: ios=2423358/0, merge=0/0, ticks=149380/0, in_queue=149380, util=99.29%, aggrios=1211679/0, aggrmerge=0/0, aggrticks=74440/0, aggrin_queue=74440, aggrutil=99.29%
    dm-1: ios=2423358/0, merge=0/0, ticks=148880/0, in_queue=148880, util=99.29%, aggrios=2423358/0, aggrmerge=0/0, aggrticks=156027/0, aggrin_queue=156026, aggrutil=99.29%
  sdb: ios=2423358/0, merge=0/0, ticks=156027/0, in_queue=156026, util=99.29%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Write IOPS...
write_iops: (g=0): rw=randwrite, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

write_iops: (groupid=0, jobs=1): err= 0: pid=17: Sat Aug  2 14:34:18 2025
  write: IOPS=111k, BW=436MiB/s (457MB/s)(6534MiB/15001msec)
  cpu          : usr=16.27%, sys=67.79%, ctx=743713, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=114.3%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=0,1672611,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
  WRITE: bw=436MiB/s (457MB/s), 436MiB/s-436MiB/s (457MB/s-457MB/s), io=6534MiB (6851MB), run=15001-15001msec

Disk stats (read/write):
    dm-4: ios=0/1911623, merge=0/0, ticks=0/44556, in_queue=44556, util=98.54%, aggrios=0/1911629, aggrmerge=0/0, aggrticks=0/42536, aggrin_queue=42536, aggrutil=97.82%
    dm-2: ios=0/1911629, merge=0/0, ticks=0/42536, in_queue=42536, util=97.82%, aggrios=0/955814, aggrmerge=0/0, aggrticks=0/21058, aggrin_queue=21058, aggrutil=97.76%
    dm-1: ios=0/1911629, merge=0/0, ticks=0/42116, in_queue=42116, util=97.76%, aggrios=0/1911629, aggrmerge=0/0, aggrticks=0/48172, aggrin_queue=48175, aggrutil=97.58%
  sdb: ios=0/1911629, merge=0/0, ticks=0/48172, in_queue=48175, util=97.58%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Read Bandwidth...
read_bw: (g=0): rw=randread, bs=128KiB-128KiB,128KiB-128KiB,128KiB-128KiB, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

read_bw: (groupid=0, jobs=1): err= 0: pid=25: Sat Aug  2 14:34:36 2025
   read: IOPS=12.6k, BW=1573MiB/s (1650MB/s)(23.6GiB/15008msec)
  cpu          : usr=2.34%, sys=10.49%, ctx=178137, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.3%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=188839,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=1573MiB/s (1650MB/s), 1573MiB/s-1573MiB/s (1650MB/s-1650MB/s), io=23.6GiB (24.8GB), run=15008-15008msec

Disk stats (read/write):
    dm-4: ios=221307/0, merge=0/0, ticks=1124128/0, in_queue=1124128, util=99.61%, aggrios=221403/0, aggrmerge=0/0, aggrticks=1124560/0, aggrin_queue=1124560, aggrutil=99.36%
    dm-2: ios=221403/0, merge=0/0, ticks=1124560/0, in_queue=1124560, util=99.36%, aggrios=110701/0, aggrmerge=0/0, aggrticks=562274/0, aggrin_queue=562274, aggrutil=99.36%
    dm-1: ios=221403/0, merge=0/0, ticks=1124548/0, in_queue=1124548, util=99.36%, aggrios=214033/0, aggrmerge=7376/0, aggrticks=1087215/0, aggrin_queue=1087216, aggrutil=99.37%
  sdb: ios=214033/0, merge=7376/0, ticks=1087215/0, in_queue=1087216, util=99.37%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Write Bandwidth...
write_bw: (g=0): rw=randwrite, bs=128KiB-128KiB,128KiB-128KiB,128KiB-128KiB, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

write_bw: (groupid=0, jobs=1): err= 0: pid=33: Sat Aug  2 14:34:53 2025
  write: IOPS=44.6k, BW=5574MiB/s (5845MB/s)(81.7GiB/15003msec)
  cpu          : usr=12.39%, sys=35.07%, ctx=656517, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.4%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=0,668938,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
  WRITE: bw=5574MiB/s (5845MB/s), 5574MiB/s-5574MiB/s (5845MB/s-5845MB/s), io=81.7GiB (87.7GB), run=15003-15003msec

Disk stats (read/write):
    dm-4: ios=0/784543, merge=0/0, ticks=0/1113788, in_queue=1113788, util=99.57%, aggrios=0/784668, aggrmerge=0/0, aggrticks=0/1113560, aggrin_queue=1113560, aggrutil=99.30%
    dm-2: ios=0/784668, merge=0/0, ticks=0/1113560, in_queue=1113560, util=99.30%, aggrios=0/392334, aggrmerge=0/0, aggrticks=0/556708, aggrin_queue=556708, aggrutil=99.29%
    dm-1: ios=0/784668, merge=0/0, ticks=0/1113416, in_queue=1113416, util=99.29%, aggrios=0/758512, aggrmerge=0/26156, aggrticks=0/1076757, aggrin_queue=1076774, aggrutil=99.29%
  sdb: ios=0/758512, merge=0/26156, ticks=0/1076757, in_queue=1076774, util=99.29%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Read Latency...
read_latency: (g=0): rw=randread, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=4
fio-2.17-45-g06cb
Starting 1 process

read_latency: (groupid=0, jobs=1): err= 0: pid=41: Sat Aug  2 14:35:10 2025
   read: IOPS=60.6k, BW=236MiB/s (248MB/s)(3546MiB/15001msec)
    slat (usec): min=2, max=128, avg= 4.70, stdev= 1.51
    clat (usec): min=17, max=2031, avg=61.07, stdev=12.44
     lat (usec): min=26, max=2035, avg=65.79, stdev=12.58
    clat percentiles (usec):
     |  1.00th=[   50],  5.00th=[   52], 10.00th=[   54], 20.00th=[   56],
     | 30.00th=[   57], 40.00th=[   59], 50.00th=[   60], 60.00th=[   61],
     | 70.00th=[   62], 80.00th=[   64], 90.00th=[   69], 95.00th=[   74],
     | 99.00th=[   87], 99.50th=[   95], 99.90th=[  247], 99.95th=[  278],
     | 99.99th=[  338]
    lat (usec) : 20=0.01%, 50=0.98%, 100=98.62%, 250=0.30%, 500=0.09%
    lat (usec) : 750=0.01%
    lat (msec) : 2=0.01%, 4=0.01%
  cpu          : usr=7.56%, sys=36.70%, ctx=480872, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=113.4%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=907700,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
   READ: bw=236MiB/s (248MB/s), 236MiB/s-236MiB/s (248MB/s-248MB/s), io=3546MiB (3718MB), run=15001-15001msec

Disk stats (read/write):
    dm-4: ios=1015053/2, merge=0/0, ticks=60960/0, in_queue=60960, util=99.57%, aggrios=1029775/2, aggrmerge=0/0, aggrticks=61004/0, aggrin_queue=61004, aggrutil=99.19%
    dm-2: ios=1029775/2, merge=0/0, ticks=61004/0, in_queue=61004, util=99.19%, aggrios=514887/1, aggrmerge=0/0, aggrticks=30376/0, aggrin_queue=30376, aggrutil=99.19%
    dm-1: ios=1029775/2, merge=0/0, ticks=60752/0, in_queue=60752, util=99.19%, aggrios=1029775/2, aggrmerge=0/0, aggrticks=61522/1, aggrin_queue=61524, aggrutil=99.19%
  sdb: ios=1029775/2, merge=0/0, ticks=61522/1, in_queue=61524, util=99.19%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Write Latency...
write_latency: (g=0): rw=randwrite, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=4
fio-2.17-45-g06cb
Starting 1 process

write_latency: (groupid=0, jobs=1): err= 0: pid=49: Sat Aug  2 14:35:28 2025
  write: IOPS=105k, BW=411MiB/s (431MB/s)(6161MiB/15001msec)
    slat (usec): min=3, max=136, avg= 7.08, stdev= 2.78
    clat (usec): min=0, max=3931, avg=30.57, stdev=18.23
     lat (usec): min=20, max=3935, avg=37.65, stdev=18.32
    clat percentiles (usec):
     |  1.00th=[   20],  5.00th=[   23], 10.00th=[   25], 20.00th=[   27],
     | 30.00th=[   28], 40.00th=[   29], 50.00th=[   30], 60.00th=[   31],
     | 70.00th=[   32], 80.00th=[   33], 90.00th=[   35], 95.00th=[   38],
     | 99.00th=[   51], 99.50th=[   65], 99.90th=[  181], 99.95th=[  207],
     | 99.99th=[  270]
    lat (usec) : 2=0.01%, 4=0.01%, 10=0.01%, 20=0.50%, 50=98.40%
    lat (usec) : 100=0.89%, 250=0.20%, 500=0.01%, 750=0.01%
    lat (msec) : 2=0.01%, 4=0.01%
  cpu          : usr=14.13%, sys=61.67%, ctx=1056737, majf=0, minf=2
  IO depths    : 1=0.1%, 2=0.1%, 4=113.5%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,1577182,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
  WRITE: bw=411MiB/s (431MB/s), 411MiB/s-411MiB/s (431MB/s-431MB/s), io=6161MiB (6460MB), run=15001-15001msec

Disk stats (read/write):
    dm-4: ios=0/1763728, merge=0/0, ticks=0/37400, in_queue=37400, util=98.54%, aggrios=0/1790698, aggrmerge=0/0, aggrticks=0/36028, aggrin_queue=36028, aggrutil=97.81%
    dm-2: ios=0/1790698, merge=0/0, ticks=0/36028, in_queue=36028, util=97.81%, aggrios=0/895349, aggrmerge=0/0, aggrticks=0/17786, aggrin_queue=17786, aggrutil=97.67%
    dm-1: ios=0/1790698, merge=0/0, ticks=0/35572, in_queue=35572, util=97.67%, aggrios=0/1790698, aggrmerge=0/0, aggrticks=0/41798, aggrin_queue=41797, aggrutil=97.49%
  sdb: ios=0/1790698, merge=0/0, ticks=0/41798, in_queue=41797, util=97.49%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Read Sequential Speed...
read_seq: (g=0): rw=read, bs=1024KiB-1024KiB,1024KiB-1024KiB,1024KiB-1024KiB, ioengine=libaio, iodepth=16
...
fio-2.17-45-g06cb
Starting 4 threads
read_seq: Laying out IO file(s) (1 file(s) / 3548MiB)

read_seq: (groupid=0, jobs=1): err= 0: pid=57: Sat Aug  2 14:35:47 2025
   read: IOPS=1614, BW=1616MiB/s (1694MB/s)(23.8GiB/15028msec)
  cpu          : usr=0.15%, sys=3.40%, ctx=24083, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.1%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=24270,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
read_seq: (groupid=0, jobs=1): err= 0: pid=58: Sat Aug  2 14:35:47 2025
   read: IOPS=1622, BW=1623MiB/s (1702MB/s)(23.9GiB/15031msec)
  cpu          : usr=0.10%, sys=3.51%, ctx=24185, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=24387,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
read_seq: (groupid=0, jobs=1): err= 0: pid=59: Sat Aug  2 14:35:47 2025
   read: IOPS=1617, BW=1618MiB/s (1697MB/s)(23.8GiB/15029msec)
  cpu          : usr=0.21%, sys=3.39%, ctx=24097, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.2%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=24303,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
read_seq: (groupid=0, jobs=1): err= 0: pid=60: Sat Aug  2 14:35:47 2025
   read: IOPS=1562, BW=1564MiB/s (1640MB/s)(22.1GiB/15018msec)
  cpu          : usr=0.14%, sys=3.53%, ctx=23229, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=112.8%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=23473,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16

Run status group 0 (all jobs):
   READ: bw=6420MiB/s (6731MB/s), 1564MiB/s-1623MiB/s (1640MB/s-1702MB/s), io=94.3GiB (101GB), run=15018-15031msec

Disk stats (read/write):
    dm-4: ios=482212/18, merge=0/0, ticks=4760752/128, in_queue=4760880, util=99.58%, aggrios=482998/18, aggrmerge=0/0, aggrticks=4768784/128, aggrin_queue=4768912, aggrutil=99.21%
    dm-2: ios=482998/18, merge=0/0, ticks=4768784/128, in_queue=4768912, util=99.21%, aggrios=241499/9, aggrmerge=0/0, aggrticks=2384360/64, aggrin_queue=2384424, aggrutil=99.21%
    dm-1: ios=482998/18, merge=0/0, ticks=4768720/128, in_queue=4768848, util=99.21%, aggrios=121905/18, aggrmerge=361101/0, aggrticks=1199816/139, aggrin_queue=1199958, aggrutil=99.21%
  sdb: ios=121905/18, merge=361101/0, ticks=1199816/139, in_queue=1199958, util=99.21%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Write Sequential Speed...
write_seq: (g=0): rw=write, bs=1024KiB-1024KiB,1024KiB-1024KiB,1024KiB-1024KiB, ioengine=libaio, iodepth=16
...
fio-2.17-45-g06cb
Starting 4 threads

write_seq: (groupid=0, jobs=1): err= 0: pid=68: Sat Aug  2 14:36:04 2025
  write: IOPS=1318, BW=1320MiB/s (1384MB/s)(19.4GiB/15017msec)
  cpu          : usr=7.82%, sys=2.16%, ctx=19686, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.3%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,19804,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
write_seq: (groupid=0, jobs=1): err= 0: pid=69: Sat Aug  2 14:36:04 2025
  write: IOPS=1334, BW=1336MiB/s (1401MB/s)(19.7GiB/15026msec)
  cpu          : usr=7.73%, sys=2.03%, ctx=19881, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.5%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,20057,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
write_seq: (groupid=0, jobs=1): err= 0: pid=70: Sat Aug  2 14:36:04 2025
  write: IOPS=1348, BW=1350MiB/s (1415MB/s)(19.9GiB/15028msec)
  cpu          : usr=8.48%, sys=1.81%, ctx=20129, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.3%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,20271,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16
write_seq: (groupid=0, jobs=1): err= 0: pid=71: Sat Aug  2 14:36:04 2025
  write: IOPS=1352, BW=1354MiB/s (1419MB/s)(19.9GiB/15031msec)
  cpu          : usr=8.11%, sys=2.06%, ctx=20158, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=113.2%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwt: total=0,20330,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16

Run status group 0 (all jobs):
  WRITE: bw=5357MiB/s (5617MB/s), 1320MiB/s-1354MiB/s (1384MB/s-1419MB/s), io=78.7GiB (84.5GB), run=15017-15031msec

Disk stats (read/write):
    dm-4: ios=0/403529, merge=0/0, ticks=0/4727420, in_queue=4727420, util=99.58%, aggrios=0/404206, aggrmerge=0/0, aggrticks=0/4734672, aggrin_queue=4734672, aggrutil=99.27%
    dm-2: ios=0/404206, merge=0/0, ticks=0/4734672, in_queue=4734672, util=99.27%, aggrios=0/202103, aggrmerge=0/0, aggrticks=0/2367300, aggrin_queue=2367300, aggrutil=99.28%
    dm-1: ios=0/404206, merge=0/0, ticks=0/4734600, in_queue=4734600, util=99.28%, aggrios=0/109036, aggrmerge=0/295170, aggrticks=0/1276806, aggrin_queue=1276807, aggrutil=99.28%
  sdb: ios=0/109036, merge=0/295170, ticks=0/1276806, in_queue=1276807, util=99.28%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


Testing Read/Write Mixed...
rw_mix: (g=0): rw=randrw, bs=4096B-4096B,4096B-4096B,4096B-4096B, ioengine=libaio, iodepth=64
fio-2.17-45-g06cb
Starting 1 process

rw_mix: (groupid=0, jobs=1): err= 0: pid=79: Sat Aug  2 14:36:21 2025
   read: IOPS=94.5k, BW=369MiB/s (387MB/s)(5533MiB/15001msec)
  write: IOPS=31.5k, BW=123MiB/s (129MB/s)(1844MiB/15001msec)
  cpu          : usr=18.73%, sys=73.62%, ctx=370355, majf=0, minf=1
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=113.7%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=1416281,472135,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=369MiB/s (387MB/s), 369MiB/s-369MiB/s (387MB/s-387MB/s), io=5533MiB (5801MB), run=15001-15001msec
  WRITE: bw=123MiB/s (129MB/s), 123MiB/s-123MiB/s (129MB/s-129MB/s), io=1844MiB (1934MB), run=15001-15001msec

Disk stats (read/write):
    dm-4: ios=1610088/537002, merge=0/0, ticks=101016/11968, in_queue=112984, util=99.60%, aggrios=1610124/537014, aggrmerge=0/0, aggrticks=99156/11356, aggrin_queue=110512, aggrutil=99.28%
    dm-2: ios=1610124/537014, merge=0/0, ticks=99156/11356, in_queue=110512, util=99.28%, aggrios=805062/268507, aggrmerge=0/0, aggrticks=49346/5622, aggrin_queue=54968, aggrutil=99.28%
    dm-1: ios=1610124/537014, merge=0/0, ticks=98692/11244, in_queue=109936, util=99.28%, aggrios=1610124/537014, aggrmerge=0/0, aggrticks=104341/13174, aggrin_queue=117516, aggrutil=99.29%
  sdb: ios=1610124/537014, merge=0/0, ticks=104341/13174, in_queue=117516, util=99.29%
  dm-0: ios=0/0, merge=0/0, ticks=0/0, in_queue=0, util=0.00%


All tests complete.

==================
= Dbench Summary =
==================
Random Read/Write IOPS: 142k/111k. BW: 1573MiB/s / 5574MiB/s
Average Latency (usec) Read/Write: 65.79/37.65
Sequential Read/Write: 6420MiB/s / 5357MiB/s
Mixed Random Read/Write IOPS: 94.5k/31.5k
```
