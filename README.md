# Agregar disco duro linux
## Montar disco

El primer paso es buscar los discos instalados en el sistema a travez del comando lsblk. En este ejemplo el disco duro nuevo es el sdb, siendo sda nuestro disco principal donde esta instalado el SO. 

```sh
miguel@server:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0    8G  0 disk
├─sda1   8:1    0    7G  0 part /
├─sda2   8:2    0    1K  0 part
└─sda5   8:5    0  975M  0 part [SWAP]
sdb      8:16   0 68.4G  0 disk
sr0     11:0    1 1024M  0 rom
```

Procedemo a crear particiones para este disco duro, primero vamos a crear la particion principal que utilizara todo el tamaño del disco duro, y el sistema de archivos que planeamos utilizar.

```sh
miguel@server:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.33.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x6960b512.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-143525759, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-143525759, default 143525759):

Created a new partition 1 of type 'Linux' and of size 68.4 GiB.

Command (m for help): t
Selected partition 1
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
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs
 f  W95 Extd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep
1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT
Hex code (type L to list all codes): 83
Changed type of partition 'Linux' to 'Linux'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
Con lsblk o con fdisk -l podemos ver la nueva particion creada.

```sh
miguel@server:~$ sudo fdisk -l
Disk /dev/sda: 8 GiB, 8589934592 bytes, 16777216 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x4f3a45eb

Device     Boot    Start      End  Sectors  Size Id Type
/dev/sda1  *        2048 14776319 14774272    7G 83 Linux
/dev/sda2       14778366 16775167  1996802  975M  5 Extended
/dev/sda5       14778368 16775167  1996800  975M 82 Linux swap / Solaris


Disk /dev/sdb: 68.4 GiB, 73485189120 bytes, 143525760 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x6960b512

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdb1        2048 143525759 143523712 68.4G 83 Linux

```

Proseguimos a crear el sistema de archivos para la particion creada, y crearla en una carpeta existente

```sh
miguel@server:~$ sudo mkfs -t ext4 /dev/sdb1
miguel@server:~$ sudo mount -t ext4 /dev/sdb1 /var/lib/docker/
miguel@server:~$ df -hT
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  984M     0  984M   0% /dev
tmpfs          tmpfs     200M  5.5M  195M   3% /run
/dev/sda1      ext4      6.9G  6.2G  380M  95% /
tmpfs          tmpfs     998M     0  998M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     998M     0  998M   0% /sys/fs/cgroup
/dev/sdb1      ext4       67G  4.0G   60G   7% /var/lib/docker
tmpfs          tmpfs     200M     0  200M   0% /run/user/1000
```

En caso de querer desmontarlo seria con el comando

```sh
miguel@server:~$ sudo umount /dev/sdb1
```

Para poder realizar una particion que se monte cada vez que se reincia el equipo seria necesario editar el archivo /etc/fstab

```sh
miguel@server:~$ sudo blkid
[sudo] password for miguel:
/dev/sdb1: UUID="6ee500c3-7db0-4fe5-a058-c877ae71cde5" TYPE="ext4" PARTUUID="6960b512-01"
/dev/sda1: UUID="d54ed4cc-52f3-43bf-8111-fbf8d11e28aa" TYPE="ext4" PARTUUID="4f3a45eb-01"
/dev/sda5: UUID="cad17420-e59f-4750-bf9b-a90ba51c20fa" TYPE="swap" PARTUUID="4f3a45eb-05"


UUID=6ee500c3-7db0-4fe5-a058-c877ae71cde5 /var/lib/docker/               ext4    errors=remount-ro 0       1
```
