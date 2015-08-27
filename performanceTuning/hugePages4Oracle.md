## Notes for Huge Pages


### Troubleshooting Commands


##### Memory Analysis

* Usage
```
sar -r -f /var/log/sa/sa18
...
08:40:01 PM    641396  32231032     98.05    839892  28669184  15349308     23.29
08:50:02 PM    635516  32236912     98.07    839908  28669276  15354324     23.29
09:00:01 PM    632892  32239536     98.07    839912  28669512  15359648     23.30
09:10:01 PM    625652  32246776     98.10    839948  28670136  15379224     23.33
09:20:01 PM    580712  32291716     98.23    839960  28670964  15392172     23.35
09:30:01 PM    592812  32279616     98.20    839972  28671296  15394176     23.35
09:40:01 PM    613420  32259008     98.13    840008  28671652  15372280     23.32
09:50:01 PM    563336  32309092     98.29    840016  28671760  15438272     23.42
10:00:01 PM    606368  32266060     98.16    840036  28672028  15391508     23.35
10:10:01 PM    462236  32410192     98.59    840068  28518416  15689740     23.80
```

* System Memory Usage/Configuration
```
cat /proc/meminfo
  ...
  MemTotal:       32872428 kB
  MemFree:        16468044 kB
  Buffers:          886644 kB
  Cached:         12643916 kB
  ...
  AnonHugePages:    995328 kB
  PageTables:       270436 kB
  ...
  HugePages_Total:       0
  HugePages_Free:        0
  HugePages_Rsvd:        0
  HugePages_Surp:        0
  Hugepagesize:       2048 kB
grep Anon /proc/meminfo
  AnonPages:       1834832 kB
  AnonHugePages:    997376 kB
```

* Kernel Settings
  * shmax = 67GB
  * shmall = 17179.9GB
  * shmmni = 4096
* Limits
  * memlock = 25165824 (24GB)
```
ipcs -lm
  ------ Shared Memory Limits --------
  max number of segments = 4096                       <--- this is SHMMNI
  max seg size (kbytes) = 67108864                    <--- this is SHMMAX
  max total shared memory (kbytes) = 17179869184      <--- this is SHMALL
  min seg size (bytes) = 1
sysctl -a | grep  shmmax
  kernel.shmmax = 68719476736
sysctl -a | grep shmall
  kernel.shmall = 4294967296
grep memlock /etc/security/limits.conf
  * hard memlock 25165824
  * soft memlock 25165824
```

* Database Configuration
```
SQL> SHOW SGA
Total System Global Area 1.2827E+10 bytes
Fixed Size                  2240344 bytes
Variable Size            5670699176 bytes
Database Buffers         7113539584 bytes
Redo Buffers               40890368 bytes
```

##### Configuring the system to Use Hugepages

* Kernel Settings
  * shmax = 30064771072 (28GB)
  * shmall =  7340032 (28GB)
  * shmmni = 4096
* Limits
  * memlock = 29360128 (28GB)
* Calculating HugePages
  * MAX_SGA: 24GB
  * HugePages: 2048 (2MB)
  * then you would have allocated {MAX_SGA}({HugePages}*2M – assumption Hugepagesize=2M) of Hugepages space.
  * vm.nr_hugepages = 12298

### Great Links

- https://events.linuxfoundation.org/slides/2011/lfcs/lfcs2011_hpc_arcangeli.pdf
- http://www.pythian.com/blog/performance-tuning-hugepages-in-linux/
- http://docs.oracle.com/cd/E37670_01/E37355/html/ol_config_hugepages.html
- https://tinky2jed.wordpress.com/technical-stuff/oracle-stuff/configuring-hugepages-for-oracle-database/
- https://dbakerber.wordpress.com/2015/03/11/update-on-hugepages-rewrite-to-fix-formatting-issues/
- https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Tuning_and_Optimizing_Red_Hat_Enterprise_Linux_for_Oracle_9i_and_10g_Databases/sect-Oracle_9i_and_10g_Tuning_Guide-Setting_Shared_Memory-Setting_SHMALL_Parameter.html
- http://seriousbirder.com/blogs/centos-6-setting-shmmax-and-shmall-kernel-paramaters/
- http://padmavyuha.blogspot.com/2010/12/configuring-shmmax-and-shmall-for.html
- http://www.thegeekstuff.com/2011/03/sar-examples/
