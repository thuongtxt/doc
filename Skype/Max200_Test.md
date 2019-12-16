# PreInstall
```
mdkir <your-workspace>
cd <your-workspace>
export WD=`pwd`
echo ${WD}
```

# Install DPDK
```
cd ${WD}
git clone git@gitlab.atvn.com.vn:ovs/dpdk.git
# or clone with https
# git clone http://gitlab.atvn.com.vn/ovs/dpdk.git
export RTE_SDK=${WD}/dpdk
export RTE_TARGET=x86_64-native-linuxapp-gcc
cd $RTE_SDK
git checkout v18.11
make install T=x86_64-native-linuxapp-gcc DESTDIR=$RTE_SDK/sdk -j
```

# Install Pktgen
```
cd ${WD}
git clone http://dpdk.org/git/apps/pktgen-dpdk
cd ${WD}/pktgen-dpdk
git checkout pktgen-3.6.6

```

# Bind device into dpdk driver
* Move to dpdk dir
 > cd $RTE_SDK
* Load kernel module
```
sudo rmmod igb_uio
sudo modprobe uio 
sudo insmod ${RTE_TARGET}/kmod/igb_uio.ko
```
* List network device
 > ./usertools/dpdk-devbind.py -s
 
```bash
Network devices using kernel driver
===================================
0000:04:00.0 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.1 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.2 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.3 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.4 'T62100-CR Unified Wire Ethernet Controller 640d' if=ens3f4,ens3f4d1 drv=cxgb4 unused=igb_uio 
0000:07:00.0 'I210 Gigabit Network Connection 1533' if=enp7s0 drv=igb unused=igb_uio *Active*
0000:08:00.0 'I210 Gigabit Network Connection 1533' if=enp8s0 drv=igb unused=igb_uio *Active*

Other Network devices
=====================
0000:03:00.0 'Ethernet Controller XL710 for 40GbE backplane 1580' unused=i40e,igb_uio
0000:03:00.1 'Ethernet Controller XL710 for 40GbE backplane 1580' unused=i40e,igb_uio

No 'Crypto' devices detected
============================

No 'Eventdev' devices detected
==============================

No 'Mempool' devices detected
=============================

No 'Compress' devices detected
==============================
```

* Bind XL710 devices with command 
> sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:03:00.0  
> sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:03:00.1
  
* Recheck
> ./usertools/dpdk-devbind.py -s

```bash
Network devices using DPDK-compatible driver
============================================
0000:03:00.0 'Ethernet Controller XL710 for 40GbE backplane 1580' drv=igb_uio unused=i40e
0000:03:00.1 'Ethernet Controller XL710 for 40GbE backplane 1580' drv=igb_uio unused=i40e

Network devices using kernel driver
===================================
0000:04:00.0 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.1 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.2 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.3 'T62100-CR Unified Wire Ethernet Controller 600d' if= drv=cxgb4 unused=igb_uio 
0000:04:00.4 'T62100-CR Unified Wire Ethernet Controller 640d' if=ens3f4,ens3f4d1 drv=cxgb4 unused=igb_uio 
0000:07:00.0 'I210 Gigabit Network Connection 1533' if=enp7s0 drv=igb unused=igb_uio *Active*
0000:08:00.0 'I210 Gigabit Network Connection 1533' if=enp8s0 drv=igb unused=igb_uio *Active*

No 'Crypto' devices detected
============================

No 'Eventdev' devices detected
==============================

No 'Mempool' devices detected
=============================

No 'Compress' devices detected
==============================

```

# Run pktgen
* Move to pktgen dir
> cd ${WD}/pktgen-dpdk
* Start pktgen
> sudo -E ./app/x86_64-native-linuxapp-gcc/pktgen -l 0-4 -n 4 --proc-type auto --log-level 7 --file-prefix pg -- -T -P -m [1:2].0 -m [3:3].1 -f themes/black-yellow.theme 
* Inside pktgen promt run:
  * to start all ports: <kbd>start all</kbd>
  * to stop all ports:  <kbd>stop all </kbd>
  * clear statistic of all ports: <kbd>clr</kbd>

