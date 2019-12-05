
__Revision history__

| Revision      | Date          | Author        | Description    |
|---------------|---------------|---------------|----------------|
| 1.00          | Aug 21, 2019  | HungTM        | Initial Version|
| 2.00          | sept 18, 2019 | HungTM        | update model test trTCM rfc2698 |


__Abbreviation and Terminologies__

__LAG__ Link Aggregation Group

__LACP__ Link Aggregation Control Protocol

__NIC__ A network interface controller (NIC, also known as a network interface card, network adapter, LAN adapter or physical network interface). Refer the [wiki](https://en.wikipedia.org/wiki/Network_interface_controller)


## Overview Of Open vSwitch with DPDK

![OVS + DPDK Architecture](images/OpenvSwitchAndDPDK.png)

## Build & Install Open vSwitch with DPDK in UBUNTU (18.02 LTS X86_64)
In this note, we built openvswitch-2.11.1 and dpdk-stable-18.11.2 in ubuntu 18.02 LTS x86_64, gcc 7.4.0

### Build & Install DPDK
Goto DPDK source directory

```
$ make config T=x86_64-native-linuxapp-gcc #configure Target to build

$ make DESTDIR=[path of DPDK SDK folder] prefix=[prefix] #default value of prefix=/usr/local

$ make install DESTDIR=[path/of/DPDK-SDK/folder]
```
Note:

	For more options of make file, please use `make help` command, The DPDK SDK will be allocated at [path of DPDK SDK folder]/[prefix]

### Build & Install OVS with DPDK
Goto OVS source directory

```

$ ./boot.sh
$ ./configure --prefix=[path/to/install/lib,binary,../of/ovs] --with-dpdk=[path/of/DPDK-SDK]/share/dpdk/x86_64-native-linuxapp-gcc
$ make
$ make install

```
Note: 

	+ The ovs software will be stored in [path/to/install/lib,binary,../of/ovs]
	+ For more information about configuration of OVS, please use `configure --help`

### Build & Install igb_uio driver (provided by DPDK) (TBU)

## Setup NIC, Using igb_uio driver (compatible with dpdk)
### Setup NIC with with `dpdk-devbind.py` script.
dpdk-devbind.py is provided by DPDK SDK at `dpdk-stable-18.11.2/usertools/` folder. 

	To display current device status:
		`dpdk-devbind.py --status`

	To display current network device status:
		`dpdk-devbind.py --status-dev net`

	To bind eth1 from the current driver and move to use igb_uio
		`dpdk-devbind.py --bind=igb_uio eth1`

	To unbind 0000:01:00.0 from using any driver
		`dpdk-devbind.py -u 0000:01:00.0`

	To bind 0000:02:00.0 and 0000:02:00.1 to the ixgbe kernel driver
		`dpdk-devbind.py -b ixgbe 02:00.0 02:00.1`

For more information of this script, we can show help message by `dpdk-devbind.py --help`. Below is example to setup NIC in Server 5: 82574L Gigabit Network (pci address: 0000:02:00.0)

using `dpdk-devbind.py --status` to list out status of NIC in server 5

```

$ dpdk-devbind.py --status

Network devices using kernel driver
===================================
0000:00:19.0 '82579LM Gigabit Network Connection (Lewisville) 1502' if=eno1 drv=e1000e unused=igb_uio,uio_pci_generic *Active*

Other Network devices
=====================
0000:02:00.0 '82574L Gigabit Network Connection 10d3' unused=e1000e,igb_uio,uio_pci_generic

No 'Crypto' devices detected
============================

```

In the result, don't have any NIC compatible with DPDK, use the script to bind `82574L Gigabit Network` NIC

```

$ sudo dpdk-devbind.py --bind=igb_uio 0000:02:00.0
$ dpdk-devbind.py --status

Network devices using DPDK-compatible driver
============================================
0000:02:00.0 '82574L Gigabit Network Connection 10d3' drv=igb_uio unused=e1000e,uio_pci_generic

Network devices using kernel driver
===================================
0000:00:19.0 '82579LM Gigabit Network Connection (Lewisville) 1502' if=eno1 drv=e1000e unused=igb_uio,uio_pci_generic *Active*

```

Note: 

	igb_uio also provided by DPDK SDK, please check it in {dpdk_source_code_path}/x86_64-native-linuxapp-gcc/kmod/igb_uio.ko

### Setup NIC with `dpdk-setup.sh` script (TBU)

## Setup hugepage in UBUNTU (18.02 LTS X86_64)
### Setup manually (TBU)
### Setup using `dpdk-setup.sh` script (TBU)

## Configure & launch OVS+DPDK
### Configure & launch OVS+DPDK manually
In this session, we tested OVS+DPDK on Server 9 (2 NUMA Node: 0 & 1)

+ with huge_page, HugePages_Free: 7737 (using `$ cat /proc/meminfo` to check)
+ pid file, log file will be used default.

Below is step by step to launch OVS+DPDK

Step 1: check & create database `conf.db` if it is not exist. The command to create the database

	$ sudo ovsdb-tool create /etc/openvswitch/conf.db /share/openvswitch/vswitch.ovsschema

Step 2: start ovsdb-server

	$ sudo ovsdb-server --remote=punix:/var/run/openvswitch/db.sock  --remote=db:Open_vSwitch,Open_vSwitch,manager_options --pidfile --detach --log-file

Step 3: Configure DPDK related parameter

	#Initialize & support DPDK port in OVS (default is false)
	$ sudo ovs-vsctl --no-wait set Open_vSwitch . other_config:dpdk-init=true

	#Specifies the CPU cores on which dpdk lcore threads should be spawned
	$ sudo ovs-vsctl --no-wait set Open_vSwitch . other_config:dpdk-lcore-mask="0x03"

	#if we have more than one numa node, should initialize both
	$ sudo ovs-vsctl --no-wait set Open_vSwitch . other_config:dpdk-socket-mem="2048,2048"

	#multiple PMD threads can be created and pinned to CPU cores by explicitly specifying pmd-cpu-masks
	$ sudo ovs-vsctl --no-wait set Open_vSwitch . other_config:pmd-cpu-mask=0x3

Step 4: start ovs_vswitchd

	$ sudo ovs-vswitchd --pidfile --detach --log-file

### Configure & launch OVS+DPDK automatically by `ovs_mini_tools.py` script (TBU)

## Initialize OvS+DPDK Bridge
### Initialize OvS+DPDK Bridge manually (TBU)
In this session, we focus on how to add OvS+DPDK bridge (`ovsdb-server` & `ovs_vswitchd` have to started)

+ NIC is Chelsio with PCI address 0000:03:00.4 (already bind with igb_uio drvier - compatible with DPDK)
+ bridge with `datapath_type` is `netdev` (support DPDK ports)
+ physical port with `type` is `dpdk` (connect to NIC)
+ vhost user port with `type` is `dpdkvhostuser` (connect to virtual machine)

To check current bridge in system, we use command `$ sudo ovs-vsctl show`

	$ sudo ovs-vsctl show
	ec3086f5-67ac-4c14-9b47-ea7520235faf

For the first time, database is empty so we don't have any bridge as above. Below is step by step to add a OvS+DPDK bridge

Step 1: Add bridge (example: bridge name is `ovs-dpdk-br0`)

	$ sudo ovs-vsctl add-br ovs-dpdk-br0 -- set bridge ovs-dpdk-br0 datapath_type=netdev

	Note: if we already had bridge `ovs-dpdk-br0` with another datapath_type (check by command `$ sudo ovs-vsctl get bridge ovs-dpdk-br0 datapath_type`), we can set datapath_type by command
	$ sudo ovs-vsctl set bridge ovs-dpdk-br0 datapath_type=netdev

Step 2: Add physical port (physical port name `ovs-br0-pport1`)

	$ sudo ovs-vsctl add-port ovs-dpdk-br0 ovs-br0-pport1 --set Interface ovs-br0-pport1 type=dpdk options:dpdk-devargs=0000:03:00.4

	Note: loop for others physical ports

Step 3: Add vhost user port (port name is `ovs-br0-vport1`)

	#Add interface into bridge `ovs-dpdk-br0`
	$sudo ovs-vsctl add-port ovs-dpdk-br0 ovs-br0-vport1 -- set Interface ovs-br0-vport1  type=dpdkvhostuser

	Note: loop for others ports

After configure sucessfully, we can check by command `ovs-vsctl show`

	$ sudo ovs-vsctl show
	ec3086f5-67ac-4c14-9b47-ea7520235faf
    Bridge "ovs-dpdk-br0"
        Port "ovs-br0-vport3"
            Interface "ovs-br0-vport3"
                type: dpdkvhostuser
        Port "ovs-br0-vport2"
            Interface "ovs-br0-vport2"
                type: dpdkvhostuser
        Port "ovs-br0-pport1"
            Interface "ovs-br0-pport1"
                type: dpdk
                options: {dpdk-devargs="0000:03:00.4"}
        Port "ovs-br0-vport1"
            Interface "ovs-br0-vport1"
                type: dpdkvhostuser
        Port "ovs-dpdk-br0"
            Interface "ovs-dpdk-br0"
                type: internal
        Port "ovs-br0-vport4"
            Interface "ovs-br0-vport4"
                type: dpdkvhostuser

Note: 

+ If we got a error : "could not open network device ovs-br0-vport* (Unknown error -1)" when show bridge information by `$sudo ovs-vsctl show` as below

        Port "ovs-br0-vport3"
            Interface "ovs-br0-vport3"
                type: dpdkvhostuser
				error: "could not open network device ovs-br0-vport3 (Unknown error -1)"

	Try fixing it by remove "port file" in  ${ovs-software}/var/run/openvswitch that will be created when we add dpdkvhostuser port (just workaround).

+ In the result, we have a `internal port` that has `type: internal`, it is used for host. without it, the host will lose connection. Different with other ports, it is L3 port, we have to setup a IP address (example: 192.168.2.1) for it by command

	$sudo ifconfig ovs-dpdk-br0 192.168.2.1 up

For more detail about `internal port`, please refer [link](https://arthurchiao.github.io/blog/ovs-deep-dive-6-internal-port/)


### Initialize OvS+DPDK bridge automatically by `ovs_mini_tools.py` script (TBU)

## Add virtual machine into OvS+DPDK Bridge (Using QEMU)
###  Install and Configure KVM on Ubuntu 18.04 LTS
Verify Whether our system support hardware virtualization by `kvm-ok` command, if the system support hardware virtualization, the result should like below:

	$sudo kvm-ok
	INFO: /dev/kvm exists
	KVM acceleration can be used


Run the below commands to install KVM and its dependencies

	$sudo apt install qemu qemu-kvm libvirt-bin  bridge-utils  virt-manager

### Start virtual machine  (VM) & connect it with our bridge
Start VM that is connected with our bridge with below informations

+ Ram 1024M (-m 1024), CPU core 1 (-smp 1), image file lubuntu01.qcow2, mac address `A0:01:00:00:00:00`,....
+ Connected with our bridge by port `ovs-br0-vport1`
+ For other information of command, please refer munual of `qemu-system-x86_64`.

The command to start virtual machine as below:

	$ sudo qemu-system-x86_64 -m 1024 -smp 1 -cpu host -hda ${HOME}/qemu/lubuntu01.qcow2 -boot c -enable-kvm -no-reboot -net none -chardev socket,id=char1,path=${HOME}/ovs_prj/ovs-software/var/run/openvswitch/ovs-br0-vport1 -netdev type=vhost-user,id=ovs-br0-vport1,chardev=char1,vhostforce -device virtio-net-pci,mac=A0:01:00:00:00:00,netdev=ovs-br0-vport1 -object memory-backend-file,id=mem,size=1G,mem-path=/mnt/huge,share=on -numa node,memdev=mem -mem-prealloc -virtfs local,path=${HOME}/iperf_debs,mount_tag=host0,security_model=none,id=vm1_dev

	Note: 
	+ After the VM fully start up, check its IP address. if it doesn't have IP address, please setup it manually by `ifconfig` command. The subnet Ip has to same with `internal port`
	+ We can add more VM in other ports, example below is add VM2 into our bridge via `ovs-br0-vport2` port
	$ sudo qemu-system-x86_64 -m 1024 -smp 1 -cpu host -hda ${HOME}/qemu/lubuntuuu.qcow2 -boot c -enable-kvm -no-reboot -net none -chardev socket,id=char2,path=${HOME}/ovs_prj/ovs-software/var/run/openvswitch/ovs-br0-vport2 -netdev type=vhost-user,id=ovs-br0-vport2,chardev=char2,vhostforce -device virtio-net-pci,mac=A0:02:00:00:00:00,netdev=ovs-br0-vport2 -object memory-backend-file,id=mem,size=1G,mem-path=/mnt/huge,share=on -numa node,memdev=mem -mem-prealloc -virtfs local,path=${HOME}/iperf_debs,mount_tag=host0,security_model=none,id=vm2_dev



For more information about `DPDK vhost user port` & how to add `DPDK vhost user port` to the guest (qemu), please refer [link](http://docs.openvswitch.org/en/latest/topics/dpdk/vhost-user/)


## Testing tools
### intel parallel studio
This tool is used to analysis source code,... & it has already installed in SERVER 9 at `/opt/intel`. To start the tool, use below commands

	$ source /opt/intel/parallel_studio_xe_2019.4.070/bin/psxevars.sh
	$ amplxe-gui

How to configure & monitor a program? (TBU)

### gprof2dot
This tool is create call graph of program

+ Install gprof2dot, please refer [link](https://pypi.org/project/gprof2dot/)
+ How to use it with result of `intel parallel studio`, please refer [link](https://software.intel.com/en-us/articles/making-visual-call-graphs-from-intel-vtune-amplifier-output)

### sFlowTrend
This tool is used to monitor VM traffic using sFlow. 

+ To install it, refer [link](https://inmon.com/products/sFlowTrend.php)
+ Configure & use it, refer [link](http://docs.openvswitch.org/en/latest/howto/sflow/)

Example:

	#export a few valua
	export COLLECTOR_IP=172.33.41.8
	export COLLECTOR_PORT=6343
	export AGENT_IP=eno1
	export HEADER_BYTES=128
	export SAMPLING_N=64
	export POLLING_SECS=10

	#Create sFlow on OVS host
	$ sudo ovs-vsctl -- --id=@sflow create sflow agent=${AGENT_IP} target="\"${COLLECTOR_IP}:${COLLECTOR_PORT}\"" header=${HEADER_BYTES} sampling=${SAMPLING_N} polling=${POLLING_SECS} -- set bridge ovs-atvn-br0 sflow=@sflow

	# Start sFlowTren on Monitor host, one sFlowTren fully start up, it could be received data from OVS host

	Note:
	+ To see all current sets of sFlow configuration: `$ sudo ovs-vsctl list sflow`
	+ Troubleshooting: `sudo tcpdump -ni eno1 udp port 6343` (eno1: network interface)


### iperf
This tool is used to perform network throughput tests.

+ Install iperf in ubuntu by command `apt-get install iperf`
+ For more information, use command `iperf --help`

Example using iperf for testing

	#At Server side, deploy an iPerf server in UDP mode on port 8080
	$ iperf –s –u –p 8080

	#At client side, deploy an iPerf client in UDP mode on port 8080 with a transmission bandwidth of 100Mbps
	$ iperf -c 192.168.2.11 -u -p 8080 -b 100m
	
### trafgen
This tool is a fast, multithreaded network packet generator

	+ Install trafgen (in netsniff-ng toolkit) by command `$sudo apt-get install netsniff-ng`
	+ For more information about using it, please refer its manual by command `man trafgen`

Example:

	# Generate & send out 1000 packages
	$ sudo trafgen --dev enp7s0 --cpp --conf trafgen.cfg -n1000

	Note: 
	--cpp: pass  the  packet configuration to the C preprocessor before reading it
    into trafgen

	trafgen.cfg example

	/* Note: dynamic elements make trafgen slower! */
	#include <stddef.h>

	{
	/* MAC Destination */
	fill(0xff, ETH_ALEN),	/*Update destination mac address here, example: 0xA0,0x01,drnd(4),*/
	/* MAC Source */
	0x00, 0x02, 0xb3, drnd(3), /*Update source mac address here*/
	/* IPv4 Protocol */
	c16(ETH_P_IP),
	/* IPv4 Version, IHL, TOS */
	0b01000101, 0,
	/* IPv4 Total Len */
	c16(59),
	/* IPv4 Ident */
	drnd(2),
	/* IPv4 Flags, Frag Off */
	0b01000000, 0,
	/* IPv4 TTL */
	64,
	/* Proto TCP */
	0x06,
	/* IPv4 Checksum (IP header from, to) */
	csumip(14, 33),
	/* Source IP */
	drnd(4),		/*Update source IP address here: 192,168,2,10,*/
	/* Dest IP */
	drnd(4),		/*Update destination IP address here: 192,168,2,11,*/
	/* TCP Source Port */
	drnd(2),
	/* TCP Dest Port */
	c16(80),
	/* TCP Sequence Number */
	drnd(4),
	/* TCP Ackn. Number */
	c32(0),
	/* TCP Header length + TCP SYN/ECN Flag */
	c16((8 << 12) | TCP_FLAG_SYN | TCP_FLAG_ECE)
	/* Window Size */
	c16(16),
	/* TCP Checksum (offset IP, offset TCP) */
	csumtcp(14, 34),
	/* TCP Options */
	0x00, 0x00, 0x01, 0x01, 0x08, 0x0a, 0x06,
	0x91, 0x68, 0x7d, 0x06, 0x91, 0x68, 0x6f,
	/* Data blob */
	"gotcha!",	/*Update data here*/
	}

### tshark
This tool is dump and analyze network traffic. 

	+ Install `wireshark network analyzer` (TBU)
	+ More information, pleaser refer manual by command `man tshark`
	
	Note: for simple using, just use command `$ sudo tshark`

## Automation script
### dpdk-setup.sh - provided by DPDK (TBU)
### ovs_mini_tools.py - provided by ATVN (TBU)

## OvS deep dive note
### OvS without DPDK datapath (TBU)
### OvS with DPDK datapath (TBU)
### QoS feature of OvS without DPDK (TBU)
Open vSwitch does not implement QoS itself. Instead, it can configure some, but not all, of the QoS features built into the __Linux kernel__. 

For more information about configure & use it, please refer [link](http://docs.openvswitch.org/en/latest/faq/qos/#)

### QoS feature of OvS with DPDK
At writing time, OvS+DPDK just support `egress policing` and `rate limiting (ingress policing)` only.

We will focus on setting & use it in session __Model Testing/OvS with DPDK QoS Testing__ of this note.

For more information, please refer [link](http://docs.openvswitch.org/en/latest/topics/dpdk/qos/).

### OVS DPDK pmd deep dive
![](images/ovs_thread.jpg)

![](images/ovs_data_from_rx_to_tx.jpg)

Every `pmd_thead` contain 2 list:

+ *poll_list*: list of rx queues to poll.
+ *tx_ports*: Map of 'tx_port's used for transmission.

Default queue config of a port:

+ rx: 1 rx queue.
+ tx: number of tx queue equal to number of pmd thread.

#### Port rx queue:
Overview:

+ n_rxq default: 1
+ configure via `option` `n_rxq` in table `interface`. Example command: `ovs-vsctl set interface <interface> option:n_rxq=<value>`.
+ each rx queue of a port will be add to each pmd thead's poll_list one by one.
+ command show ` ovs-appctl dpif-netdev/pmd-rxq-show <bridge_name>`.

Example: set n_rxq của port `phy_port0` to 4:

> ovs-vsctl set interface phy_port0 option:n_rxq=4

```bash
$  ovs-appctl dpif-netdev/pmd-rxq-show dpdk-br0
pmd thread numa_id 0 core_id 6:
  isolated : false
  port: br0-vport2  queue-id:  0  pmd usage:  0 %
  port: phy_port0   queue-id:  0  pmd usage:  0 %
  port: phy_port0   queue-id:  1  pmd usage:  0 %
pmd thread numa_id 0 core_id 7:
  isolated : false
  port: phy_port0   queue-id:  2  pmd usage:  0 %
  port: phy_port0   queue-id:  3  pmd usage:  0 %
  port: phy_port1   queue-id:  0  pmd usage:  0 %
pmd thread numa_id 1 core_id 12:
  isolated : false
  port: br0-vport1  queue-id:  0  pmd usage:  0 %
```


#### Port tx queue
Overview:

+ Value: number of pmd thread or maximum number tx queue support.
+ A pmd_thread has rx queue infomation of all ports.
+ Each thread has a `statis queue id` which use to send packet out.

Command show rx, tx queue info: 

```bash
$ ovs-appctl dpif/show
netdev@ovs-netdev: hit:0 missed:0
  dpdk-br0:
    br0-vport1 1/5: (dpdkvhostuserclient: configured_rx_queues=1, configured_tx_queues=1, mtu=1500, requested_rx_queues=1, requested_tx_queues=1)
    br0-vport2 4/4: (dpdkvhostuser: configured_rx_queues=1, configured_tx_queues=1, mtu=1500, requested_rx_queues=1, requested_tx_queues=1)
    dpdk-br0 65534/1: (tap)
    phy_port0 2/3: (dpdk: configured_rx_queues=1, configured_rxq_descriptors=2048, configured_tx_queues=4, configured_txq_descriptors=2048, lsc_interrupt_mode=false, mtu=1500, requested_rx_queues=1, requested_rxq_descriptors=2048, requested_tx_queues=4, requested_txq_descriptors=2048, rx_csum_offload=true)
    phy_port1 3/2: (dpdk: configured_rx_queues=1, configured_rxq_descriptors=2048, configured_tx_queues=4, configured_txq_descriptors=2048, lsc_interrupt_mode=false, mtu=1500, requested_rx_queues=1, requested_rxq_descriptors=2048, requested_tx_queues=4, requested_txq_descriptors=2048, rx_csum_offload=true)
``` 

## Model Testing
### Model I - 2VM connect with OvS bridge (without DPDK)

![Model I](images/sFlowMonitoring.png)

#### Target

	+ 2VM connect to bridge.
	+ use trafgen to send out package.
	+ use tshark to trace the received package
	+ use sFlowTrend to monitor
	+ Add more VM to bridge & checking

#### Setup (TBU)

#### Testing (TBU)

### Model II - 2VM connect with OvS+DPDK bridge

![Model II](images/sFlowMonitoringWithDPDK.png)

#### Target:

	+ 2VM connect to bridge.
	+ use trafgen to send out package.
	+ use tshark to trace the received package
	+ use sFlowTrend to monitor
	+ Add more VM to bridge & checking
#### Setup:

__Step 1__: Build & install OVS+DPDK (refer session __Build & Install Open vSwitch With DPDK in UBUNTU (18.02 LTS X86_64)__)

__Step 2__: Add OVS+DPDK bridge with 1 physical port `ovs-br0-pport1` and 2 vhost user ports `ovs-br0-vport1 & ovs-br0-vport2` (refer session __Initialize OvS+DPDK Bridge__)

__Step 3__: Start VM1 connect with bridge via `ovs-br0-vport1` & Start VM2 connect with bridge via `ovs-br0-vport2` (refer __Start virtual machine  (VM) & connect it with our bridge__)

__Step 4__: Check connection between them by `ping` command.

Note: 

+ if every thing is correct, we will have a model with 2 VM connect with our bridge, if we want to have more than 2 VM, in the `Step 3`, add more VM then check connection between them by `ping` command.
+ Other later steps are optional, dependent on your purpose.

__Step 5__: configure `trafgen` in our VMs (refer __trafgen__)

__Step 6__: Configure sFlowTrend to monitor (refer __sFlowTrend__)

#### Testing:
	# Start tshark on VM1 & VM2 by command
	$ sudo tshark

	#On VM1, open terminal & start trafgen to send 10000 pakages to VM2
	$ sudo trafgen --dev ens3 --cpp --conf trafgen.cfg -n10000

	#On VM2, open terminal & start trafgen to send 10000 pakages to VM1
	$ sudo trafgen --dev ens3 --cpp --conf trafgen.cfg -n10000

	#Check the result on `tshark` terminal & sFlowTrend on Monitor host


### OvS with DPDK QoS testing
#### Target:

	+ Use Model II - 2VM connect with OvS+DPDK bridge
	+ Use iperf to check the QoS features of OvS+DPDK
	+ Find the way to show data rate in OvS+DPDK bridge (perspective of the switch)
	+ Add more VM & check again

#### Setup
Base on __Model II__, Setup 2 VM connect with our bridge. No need setup for trafgen & sFlowTrend (refer __Model II - 2VM connect with OvS+DPDK bridge/Setup__). Below is example configuration of testing

Internal port IP address: 192.168.2.1

VM1:

	+ vhost user port: ovs-br0-vport1
	+ Ip address: 192.168.2.10

VM2: 

	+ vhost user port: ovs-br0-vport2
	+ Ip address: 192.168.2.11

![Model II OVS+DPDK QoS Tesing](images/ovs_dpdk.png)

#### Testing
A list of supported QoS types for a given port (for example, ovs-br0-vport2) can be obtained with the following command

	$ sudo ovs-appctl -t ovs-vswitchd qos/show-types ovs-br0-vport2
	QoS type: egress-policer
	QoS type: egress-policer-rfc4115(TODO)
	QoS type: egress-policer-rfc2698

	Note:

	+ At writing time, the OVS+DPDK just support egress-policer (srTCM rfc2697).

	+ The result as above, egress-policer-rfc2698 is added by ATVN. The OVS source code that support this one can be checkout at [ovs feature/qos](http://gitlab.atvn.com.vn/ovs/ovs) branch __feature/qos__

##### Testing without QoS
	
	#Check & make sure the egress policing is not configured for `ovs-br0-vport2` port (we use VM2 as __iperf server__ so we will test egress policing in  `ovs-br0-vport2`)
	$ sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2
	QoS not configured on ovs-br0-vport2

	#Note
	+ if QoS already configured on ovs-br0-vport2, disable it by command `$ sudo ovs-vsctl -- destroy QoS ovs-br0-vport2 -- clear Port ovs-br0-vport2 qos`

	#Check & make sure the __Rate limiting (QoS ingress policing)__ is not configured for `ovs-br0-vport1` (we use VM1 as __iperf client__ so we will test ingress policing in `ovs-br0-vport1`)
	$ sudo ovs-vsctl list interface ovs-br0-vport1
	_uuid               : c6a57b70-6df2-491b-830d-f15c7a0686a6
	admin_state         : up
	bfd                 : {}
	bfd_status          : {}
	cfm_fault           : []
	cfm_fault_status    : []
	cfm_flap_count      : []
	cfm_health          : []
	cfm_mpid            : []
	cfm_remote_mpids    : []
	cfm_remote_opstate  : []
	duplex              : []
	error               : []
	external_ids        : {}
	ifindex             : 3159192
	ingress_policing_burst: 0
	ingress_policing_rate: 0
	lacp_current        : []
	link_resets         : 0
	link_speed          : []
	link_state          : up
	lldp                : {}
	mac                 : []
	mac_in_use          : "00:00:00:00:00:00"
	mtu                 : 1500
	mtu_request         : []
	name                : "ovs-br0-vport1"
	ofport              : 2
	ofport_request      : []
	options             : {}
	other_config        : {}
	statistics          : {"rx_1024_to_1522_packets"=0, "rx_128_to_255_packets"=46, "rx_1523_to_max_packets"=0, "rx_1_to_64_packets"=19, "rx_256_to_511_packets"=27, "rx_512_to_1023_packets"=0, "rx_65_to_127_packets"=33, rx_bytes=22060, rx_dropped=0, rx_errors=0, rx_packets=125, tx_bytes=219289, tx_dropped=14326, tx_packets=1047}
	status              : {features="0x000000017020e782", mode=server, num_of_vrings="2", numa="0", socket="/home/hungtm/ovs_prj/ovs-software/var/run/openvswitch/ovs-br0-vport1", status=connected, "vring_0_size"="256", "vring_1_size"="256"}
	type                : dpdkvhostuser

	#Note: 
	+ In the result, `ingress_policing_rate: 0` is mean `Rate limiting` is disable.
	+ if ingress_policing_rate already configured, disable it by command `$ sudo ovs-vsctl set interface ovs-br0-vport2  ingress_policing_rate=0`

	#Start __iperf server__ on VM2
	$ iperf –s –u –p 8080
	
	#Start __iperf client__ on VM1
	$ iperf -c 192.168.2.11 -u -p 8080 -b 100m

Waiting for `iperf` on VM1 finish, Bandwidth is 100Mbits/sec on both of side as below:

On VM1 (iperf client side): 

![Iperf Result On VM1](images/Iperf_client_wo_qos_result.png)

On VM2 (iperf server side):

![Iperf Result on VM2](images/Iperf_server_wo_qos_result.png)


##### Testing with egress policing (srTCM rfc2697)

![Egress Policing Testing Molde](images/egress_policing_testing_model.png)

	#Set limitation for `ovs-br0-vport2` port.
	$ sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- --id=@newqos create qos type=egress-policer other-config:cir=1250000 other-config:cbs=2048

	#Check the qos configuration
	$ sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2
	QoS: ovs-br0-vport2 egress-policer
	cir: 1250000
	cbs: 2048

Start `iperf on VM1 & VM2 again to check the qos egress policing. the result in VM1 (client side) should like below:

![QoS Egress Policing Result](images/Iperf_client_qos_egress_policing_result.png)

Fore more information about this one, please refer [link](https://software.intel.com/en-us/articles/qos-configuration-and-usage-for-open-vswitch-with-dpdk)

##### Testing with egress policing (trTCM rfc2698)

Almost is the same with srTCM rfc2697, just configure `ovs-br0-vport2` port with QoS type: egress-policer-rfc2698 as below:

	# Configure QoS egress policing trTCM (rfc2698)
    `$ sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- --id=@newqos create qos type=egress-policer-rfc2698 other-config:cir=46000000 other-config:cbs=2048 other-config:pir=46000000 other-config:pbs=2048  other-config:ydropt=false other-config:rdropt=true`

	# Edit parameter of QoS
	`$ sudo ovs-vsctl set qos __uuid__ other-config:...

	Note: 
    + PIR have to equal or greater CIR
    + Default actions of yellow & red color is dropt (ydropt=true & rdropt=true)

	# Check the qos configuration
	`$ sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2`
	QoS: ovs-br0-vport2 egress-policer-rfc2698
	cir: 46000000
	pbs: 2048
	cbs: 2048
	pir: 46000000

Then start iperf on VM1 & VM2 to testing, change cir,cbs,pir & pbs for more result.

##### Testing with ingress policing (Rate limiting)

![Ingress Policing Testing Molde](images/igress_policing_testing_model.png)

	#Set limitation for `ovs-br0-vport1` port.  (should remove `egress policing` to testing this one)
	$ sudo ovs-vsctl set interface ovs-br0-vport1 ingress_policing_rate=1024 ingress_policing_burst=128

	$ sudo ovs-vsctl list interface ovs-br0-vport1
	_uuid               : c6a57b70-6df2-491b-830d-f15c7a0686a6
	admin_state         : up
	bfd                 : {}
	bfd_status          : {}
	cfm_fault           : []
	cfm_fault_status    : []
	cfm_flap_count      : []
	cfm_health          : []
	cfm_mpid            : []
	cfm_remote_mpids    : []
	cfm_remote_opstate  : []
	duplex              : []
	error               : []
	external_ids        : {}
	ifindex             : 3159192
	ingress_policing_burst: 128
	ingress_policing_rate: 1024
	lacp_current        : []
	link_resets         : 0
	link_speed          : []
	link_state          : up
	lldp                : {}
	mac                 : []
	mac_in_use          : "00:00:00:00:00:00"
	mtu                 : 1500
	mtu_request         : []
	name                : "ovs-br0-vport1"
	ofport              : 2
	ofport_request      : []
	options             : {}
	other_config        : {}
	statistics          : {"rx_1024_to_1522_packets"=340146, "rx_128_to_255_packets"=46, "rx_1523_to_max_packets"=0, "rx_1_to_64_packets"=27, "rx_256_to_511_packets"=27, "rx_512_to_1023_packets"=0, "rx_65_to_127_packets"=33, rx_bytes=514323148, rx_dropped=0, rx_errors=0, rx_packets=340279, tx_bytes=354648, tx_dropped=14326, tx_packets=1663}
	status              : {features="0x000000017020e782", mode=server, num_of_vrings="2", numa="0", socket="/home/hungtm/ovs_prj/ovs-software/var/run/openvswitch/ovs-br0-vport1", status=connected, "vring_0_size"="256", "vring_1_size"="256"}
	type                : dpdkvhostuser

Start `iperf on VM1 & VM2 again to check the qos ingress policing. the result in VM1 (client side) should like below:

![QoS igress Policing Result](images/Iperf_client_qos_igress_policing_result.png)

For more information about this one, please refer [link](https://software.intel.com/en-us/articles/rate-limiting-configuration-and-usage-for-open-vswitch-with-dpdk) & fast refer configure command, refer [link](http://docs.openvswitch.org/en/latest/topics/dpdk/qos/)

#### Testing with more than 2VM
Base on __Model II__, add more VM then testing again on all VM. The step is the same with 2 VM.

#### Show data rate in OVS (TBU)

