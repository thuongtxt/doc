
cd ~/dpdk
export RTE_SDK=`pwd`
export RTE_TARGET=x86_64-native-linuxapp-gcc
make config T=$RTE_TARGET
make -j4
make install T=$RTE_TARGET DESTDIR=install -j4

mkdir /mnt/huge
mount -t hugetlbfs nodev /mnt/huge
echo 1024 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
grep "Hugepagesize:" /proc/meminfo

modprobe uio
insmod ./x86_64-native-linux-gcc/kmod/igb_uio.ko

./usertools/dpdk-devbind.py --bind igb_uio 0000:00:04.0 0000:00:05.0
sudo ./build/app/testpmd --file-prefix "thuongtxt" -w 0000:00:04.0 -w 0000:00:05.0 -l 1,2,3 -n 4  -- -i



show config fwd
start
show port stats all

sudo insmod /home/linux/source/dpdk/x86_64-native-linuxapp-gcc/kmod/igb_uio.ko
./usertools/dpdk-devbind.py -s

Network devices using kernel driver
===================================
0000:00:03.0 'Virtio network device 1000' if=ens3 drv=virtio-pci unused=igb_uio *Active*
0000:00:04.0 '82540EM Gigabit Ethernet Controller 100e' if=ens4 drv=e1000 unused=igb_uio 
0000:00:05.0 '82540EM Gigabit Ethernet Controller 100e' if=ens5 drv=e1000 unused=igb_uio 

No 'Baseband' devices detected
==============================

No 'Crypto' devices detected
============================

No 'Eventdev' devices detected
==============================

No 'Mempool' devices detected
=============================

No 'Compress' devices detected
==============================

No 'Misc (rawdev)' devices detected
===================================


sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:00:04.0
sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:00:05.0
./usertools/dpdk-devbind.py -s

Network devices using DPDK-compatible driver
============================================
0000:00:04.0 '82540EM Gigabit Ethernet Controller 100e' drv=igb_uio unused=e1000
0000:00:05.0 '82540EM Gigabit Ethernet Controller 100e' drv=igb_uio unused=e1000

Network devices using kernel driver
===================================
0000:00:03.0 'Virtio network device 1000' if=ens3 drv=virtio-pci unused=igb_uio *Active*

No 'Baseband' devices detected
==============================

No 'Crypto' devices detected
============================

No 'Eventdev' devices detected
==============================

No 'Mempool' devices detected
=============================

No 'Compress' devices detected
==============================

No 'Misc (rawdev)' devices detected
===================================


cd ${WD}/pktgen-dpdk
make




```
Copyright (c) <2010-2019>, Intel Corporation. All rights reserved. Powered by DPDK
EAL: Detected 4 lcore(s)
EAL: Detected 1 NUMA nodes
EAL: Auto-detected process type: PRIMARY
EAL: Multi-process socket /var/run/dpdk/pg/mp_socket
EAL: No free hugepages reported in hugepages-1048576kB
EAL: Probing VFIO support...
EAL: WARNING: cpu flags constant_tsc=yes nonstop_tsc=no -> using unreliable clock cycles !
EAL: PCI device 0000:00:03.0 on NUMA socket -1
EAL:   Invalid NUMA socket, default to 0
EAL:   probe driver: 1af4:1000 net_virtio
EAL: Error: Invalid memory
EAL: PCI device 0000:00:04.0 on NUMA socket -1
EAL:   Invalid NUMA socket, default to 0
EAL:   probe driver: 8086:100e net_e1000_em
EAL: PCI device 0000:00:05.0 on NUMA socket -1
EAL:   Invalid NUMA socket, default to 0
EAL:   probe driver: 8086:100e net_e1000_em
Lua 5.3.3  Copyright (C) 1994-2016 Lua.org, PUC-Rio

*** Copyright (c) <2010-2019>, Intel Corporation. All rights reserved.
*** Pktgen created by: Keith Wiles -- >>> Powered by DPDK <<<

Initialize Port 0 -- TxQ 1, RxQ 1,  Src MAC 52:54:00:12:34:57
Initialize Port 1 -- TxQ 1, RxQ 1,  Src MAC 52:54:00:12:34:58

Port  0: Link Up - speed 1000 Mbps - full-duplex <Enable promiscuous mode>
Port  1: Link Down <Enable promiscuous mode>



```