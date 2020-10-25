# VMware 虚拟机扩展磁盘空间

## 场景

发现虚拟机创建的CentOS 8.2系统磁盘空间不足或者因为后续需要,急需扩充磁盘空间的大小.

```shell
# 查看当前系统信息和磁盘大小
[root@k8s-node2 ~]# cat /etc/redhat-release
CentOS Linux release 8.2.2004 (Core)
[root@k8s-node2 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             3.8G     0  3.8G   0% /dev
tmpfs                3.8G     0  3.8G   0% /dev/shm
tmpfs                3.8G   17M  3.8G   1% /run
tmpfs                3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/cl-root   38G  2.9G   35G   8% /
/dev/mapper/cl-home   19G  164M   19G   1% /home
/dev/sda1            976M  198M  711M  22% /boot
tmpfs                778M     0  778M   0% /run/user/0
```

## 虚拟机扩展磁盘和添加磁盘

VMware 虚拟机设置里有两种方法扩充磁盘:

- 添加磁盘
- 扩展磁盘

## 操作

### 1. 查看当前磁盘分布

原磁盘大小是60G, 目前已经扩展90G磁盘大小

```shell
# df 发现并没有扩充进去
[root@k8s-node2 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             3.8G     0  3.8G   0% /dev
tmpfs                3.8G     0  3.8G   0% /dev/shm
tmpfs                3.8G   17M  3.8G   1% /run
tmpfs                3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/cl-root   38G  2.9G   35G   8% /
/dev/mapper/cl-home   19G  164M   19G   1% /home
/dev/sda1            976M  198M  711M  22% /boot
tmpfs                778M     0  778M   0% /run/user/0
# fdisk 发现确实有150G 但实际上扩展的90G处于未分配的状态
[root@k8s-node2 ~]# fdisk -l
Disk /dev/sda: 150 GiB, 161061273600 bytes, 314572800 sectors
...

Device     Boot   Start       End   Sectors Size Id Type
/dev/sda1  *       2048   2099199   2097152   1G 83 Linux
/dev/sda2       2099200 125829119 123729920  59G 8e Linux LVM
...
```

### 2. 扩展分区

```shell
[root@k8s-node2 ~]# fdisk /dev/sda

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

# 输入命令n，建立一个新的分区
Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 2 free)
   e   extended (container for logical partitions)
# 输入 p，或者直接按回车
Select (default p): p
# 默认回车即可
Partition number (3,4, default 3):
# 默认回车即可
First sector (125829120-314572799, default 125829120):
# 默认回车即可
Last sector, +sectors or +size{K,M,G,T,P} (125829120-314572799, default 314572799):

Created a new partition 3 of type 'Linux' and of size 90 GiB.
# 输入命令 t，定义新分区的类型
Command (m for help): t
# 默认回车即可
Partition number (1-3, default 3):
# 查看所有分区类型的进制编码
Hex code (type L to list all codes): L

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
...
# 输入 8e，8e是Linux LVM类型的代码
Hex code (type L to list all codes): 8e

Changed type of partition 'Linux' to 'Linux LVM'.
# w 写入完成修改
Command (m for help): w
The partition table has been altered.
Syncing disks.
```

注: 如果

### 3. 分区确认

```shell
# 发现新增了/dev/sda3 90G磁盘
[root@k8s-node2 ~]# fdisk -l
Disk /dev/sda: 150 GiB, 161061273600 bytes, 314572800 sectors
...

Device     Boot     Start       End   Sectors Size Id Type
/dev/sda1  *         2048   2099199   2097152   1G 83 Linux
/dev/sda2         2099200 125829119 123729920  59G 8e Linux LVM
/dev/sda3       125829120 314572799 188743680  90G 8e Linux LVM
...
```

### 4. 创建PV和VG

```shell
# 创建PV
[root@k8s-node2 ~]# pvcreate /dev/sda3
  Physical volume "/dev/sda3" successfully created.

# 查看 LV,显示 LV Path 为 /dev/cl/root, VG Name 为 cl
[root@k8s-node2 ~]# lvdisplay
...
  --- Logical volume ---
  LV Path                /dev/cl/root
  LV Name                root
  VG Name                cl
...

# 扩展VG
[root@k8s-node2 ~]# vgextend cl /dev/sda3
  Volume group "cl" successfully extended

# 扩展LV
[root@k8s-node2 ~]# lvextend /dev/cl/root /dev/sda3
  Size of logical volume cl/root changed from <37.61 GiB (9628 extents) to <127.61 GiB (32667 extents).
  Logical volume cl/root successfully resized.

# 查看文件系统类型
[root@k8s-node2 ~]# blkid
/dev/mapper/cl-root: UUID="67702638-2db4-4676-aa41-583a827a6e27" TYPE="xfs"
...

# 如果系统是用的XFS文件系统，需要要运行以下命令
[root@k8s-node2 ~]# xfs_growfs /dev/cl/root
meta-data=/dev/mapper/cl-root    isize=512    agcount=4, agsize=2464768 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1
data     =                       bsize=4096   blocks=9859072, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=4814, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 9859072 to 33451008

# 如果系统不是使用XFS文件系统，需要运行以下命令
resize2fs /dev/cl/root
```

### 5. 查看结果

```shell
# 已经扩展成功
[root@k8s-node2 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             3.8G     0  3.8G   0% /dev
tmpfs                3.8G     0  3.8G   0% /dev/shm
tmpfs                3.8G   17M  3.8G   1% /run
tmpfs                3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/cl-root  128G  3.6G  125G   3% /
/dev/mapper/cl-home   19G  164M   19G   1% /home
/dev/sda1            976M  198M  711M  22% /boot
tmpfs                778M     0  778M   0% /run/user/0
```
