./x86_64-native-linuxapp-gcc/app/testpmd -l 23-27 -n 4 --master-lcore=23 --file-prefix p1  -w 0000:04:00.1 -- -i --nb-cores=4 --rxq=4 --txq=4 --rss-ip

sudo ./x86_64-native-linuxapp-gcc/app/testpmd -l 0-4 -n 4 --master-lcore=0 --file-prefix p1  -w 0000:00:04.0 -w 0000:00:05.0  -- -i --nb-cores=4 --rxq=1 --txq=0 --rss-ip

sudo ./x86_64-native-linuxapp-gcc/app/testpmd -l 0-3 -n 4 --master-lcore=0 --file-prefix p1  -w 0000:00:04.0 -- -i --nb-cores=3 --rxq=1 --txq=0 --rss-ip
port stop all
port config 0 dcb vt off 4 pfc off
port config 1 dcb vt off 4 pfc off
port start all
set fwd rxonly
set verbose 1
start


enable 0 vlan 
set 0 dst mac 3c:fd:fe:ad:84:14
set 0 src mac 00:02:00:00:00:00
set 0 type vlan
set 0 count 10
set 0 vlan 577
start 0



sudo ip tuntap add mode  tap tap00
sudo ip link set dev tap00 up
sudo ip tuntap add mode  tap tap01
sudo ip link set dev tap01 up

sudo ip tuntap add mode tap tap10
sudo ip tuntap add mode tap tap11
sudo ip link set dev tap10 up
sudo ip link set dev tap11 up

sudo ip link add br1-nctam type bridge
sudo ip link set dev br1-nctam  up
sudo ip link add br0-nctam type bridge
sudo ip link set dev br0-nctam up

sudo ip link set tap00 master br0-nctam 
sudo ip link set tap10 master br0-nctam

sudo ip link set tap01 master br1-nctam 
sudo ip link set tap11 master br1-nctam




sudo qemu-system-x86_64 -enable-kvm -nographic -cpu host -smp 4 -m 4096 -hda core-ubuntu.qcow \
-device virtio-net-pci,netdev=net0 -netdev user,id=net0,hostfwd=tcp::5559-:22 \
-netdev tap,id=n2,ifname=tap00,script=no,downscript=no -device e1000,netdev=n2,mac=52:55:00:d1:55:02 \
-netdev tap,id=n3,ifname=tap01,script=no,downscript=no -device virtio-net-pci,netdev=n3,mac=52:55:00:d1:55:03 \
-name vm1



sudo qemu-system-x86_64 -enable-kvm -nographic -cpu host -smp 4 -m 4096 -hda core-ubuntu1.qcow \
-device virtio-net-pci,netdev=net0 -netdev user,id=net0,hostfwd=tcp::5558-:22 \
-netdev tap,id=n2,ifname=tap10,script=no,downscript=no -device e1000,netdev=n2,mac=52:55:00:d1:55:04 \
-netdev tap,id=n3,ifname=tap11,script=no,downscript=no -device virtio-net-pci,netdev=n3,mac=52:55:00:d1:55:05 \
-name vm2


sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:00:04.0 0000:00:05.0

sudo -E ./app/x86_64-native-linuxapp-gcc/pktgen -l 0-3 -n 4 --proc-type auto --log-level 7 --file-prefix pg -w 0000:00:04.0 -- -T -P -m [1:2].0 


sudo ifconfig ens4 192.168.1.16 up
sudo ifconfig ens4 192.168.1.15 up
sudo ifconfig ens5 192.168.2.16 up
sudo ifconfig ens5 192.168.2.15 up


cd dpdk
export RTE_SDK=`pwd` 
export RTE_TARGET=x86_64-native-linuxapp-gcc 
cd $RTE_SDK 
sudo rmmod igb_uio 
sudo modprobe uio  
sudo insmod ${RTE_TARGET}/kmod/igb_uio.ko 
sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:00:04.0 0000:00:05.0

cd examples/qos_sched/
sudo ./build/qos_sched -l 0,1,2 -n 4 --file-prefix p1 -w 0000:00:04.0 -- --pfc "0,0,1,2" --mst 0 --cfg ./profile.cfg


sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:04:00.0 0000:04:00.1
pktgen-3.6.6

cd ../pktgen-dpdk/
sudo -E ./app/x86_64-native-linuxapp-gcc/pktgen -l 0-3 -n 4 --proc-type auto --log-level 7 --file-prefix pg -w 0000:00:04.0 -w 0000:00:05.0 -- -T -P -m [1:2].0  -m 3.1



set 0 dst mac 52:55:00:d1:55:04
set 0 dst mac 52:55:00:d1:55:02
enable 0 vlan
enable 0 vlan

vlan 0 on
seq 0 0 00:00:00:00:00:00 fa:16:3e:d2:a6:b1 192.168.20.3 192.168.30.3/24 2040 2040 ipv4 tcp 681 64

seq 1 0 00:00:00:00:00:00 fa:16:3e:d2:a6:b1 192.168.20.3 192.168.30.3/24 2041 2041 ipv4 udp 681 64



disable 0 vlan
enable 0 vlan
set 0 rate 50
start 0

set 0 vlan 65535
sleep 0.2
set 0 vlan 36863


set 0 dst ip 100.0.1.0

set 0 dst ip 100.0.4.0
di

e


set 0 size 1000
set 0 rate 100
enable 0 latency
page latency




sequence <seq#> <portlist> <dst-Mac> <src-Mac> <dst-IP> <src-IP> <sport> <dport> ipv4|ipv6 udp|tcp|icmp <vlanid> 
sequence <seq#> <portlist> cos <cos> tos <tos>


enable 0 vlan
set 0 seq_cnt 2
page seq
sequence 0 0 00:00:00:00:01:00 00:02:00:00:00:01 10.12.0.1 10.12.0.2/16 9 10 ipv4 tcp 3 64
sequence 0 0 cos 7 tos 6
sequence 1 0 00:00:00:00:01:00 00:02:00:00:00:01 10.12.0.1 10.12.0.2/16 9 10 ipv4 tcp 4 64
sequence 1 0 cos 5 tos 5

```
                 <Sequence Page>  Copyright (c) <2010-2019>, Intel Corporation
Port:  0, Sequence Count:  2 of 16                                                               GTP-u
Seq        Dst MAC        Src MAC          Dst IP            Src IP    Port S/D   Protocol:VLAN CoS/ToS TEID Flag/Group/vxlan  Size
 0: 0011:4455:6677 0011:1234:5678       10.12.0.1      10.12.0.2/16        9/10   IPv4/TCP:000d   7/  6    0 0000/    0/    0    64
 1: 0011:4455:6677 0011:1234:5678       10.12.0.1      10.12.0.2/16        9/10   IPv4/TCP:000f   5/  5    0 0000/    0/    0    64
```

page main
start 0
stop


sequence 2 0 0011:4455:6677 0011:1234:5678 10.12.0.1 10.12.0.2/16 9 10 ipv4 tcp 13 64
sequence 2 0 cos 7 tos 6




sudo ./build/qos_sched -l 9,10,11 -n 4 --file-prefix p1 -w 0000:03:00.0 -w 0000:03:00.1 -- --pfc "1,0,10,11" --mst 9 --cfg ./profile.cfg


sudo -E ./app/x86_64-native-linuxapp-gcc/pktgen -l 24-27 -n 4 --proc-type auto --log-level 7 --file-prefix pg -w 0000:04:00.0 -- -T -P -m [25:26].0


sudo timeout 10 trafgen --dev ens2f1 --cpp --conf ex.cfg --cpus 3
sudo timeout 10 trafgen --dev ens5 --cpp --conf ex.cfg --cpus 3

sudo ifconfig ens5 up
sudo timeout 10 trafgen --dev ens5 --cpp --conf test.cfg --cpus 3




sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:03:00.0 0000:04:00.0 0000:03:00.1


sudo ./build/qos_sched -l 9,10,11 -n 4 --file-prefix p1 -w 0000:03:00.0 -w 0000:04:00.0 -- --pfc "1,0,10,11" --mst 9 --cfg ./profile.cfg

sudo -E ./app/x86_64-native-linuxapp-gcc/pktgen -l 24-27 -n 4 --proc-type auto --log-level 7 --file-prefix pg -w 0000:03:00.1 -- -T -P -m [25:26].0


sudo timeout 10  ./trafgen --dev ens2f1 --conf ../../../ex.cfg --cpp --verbose --cpus 7


apt-get install build-essential git bison flex libxml2-dev libpcap-dev libtool libtool-bin rrdtool librrd-dev autoconf pkg-config automake autogen redis-server wget libsqlite3-dev libhiredis-dev libmaxminddb-dev libcurl4-openssl-dev libpango1.0-dev libcairo2-dev libnetfilter-queue-dev zlib1g-dev libssl-dev libcap-dev libnetfilter-conntrack-dev libreadline-dev libjson-c-dev


glasses?
    yes: 1/2
        long hair?
            yes: 1/3
                guess?
                truth: 1
            no: 2/3
                1/2
    no: 1/2
        long hair?
            yes: 1/3
                guess?
                truth: 1
            no: 2/3
                1/2

Long hair?
    Yes: 2/6
        glass?
            1

    No: 4/6
        glass?
            Yes: 1/2
                guess?
                    1/2
            No: 1/2
                1/2

Is Elliot:
    Yes: 1/6

    No: 5/6
        glass?
            yes: 2/5
                1/2
            No: 3/5
            1/3




1000
5   10  15  20  25  30  35

