# Setup model 4k flows, policer 2 levels. PHY-OVS-PHY
## Policer 2 levels
### Build OVS connect DPDK
* Tree folder
```
workspace
    |>dpdk-stable-11.18.5
    |>ovs_prj
        |>dpdk-sdk
        |>ovs-software
```
* Steps: Build OVS with DPDK
```
$ cd ~/../ovs/
$ ./boot.sh 
$ ./configure --help
$ ls
$ ./configure --with-dpdk=/home/thuongtxt/ovs_prj/src/$ dpdk-stable-18.11.5/build --prefix=/home/thuongtxt/ovs_prj/$ ovs-software CFLAGS=-DATVN_DEBUG
$ make -j8
$ make install -j8
```
* Show infomation of NICs
```
$ ./usertools/dpdk-devbind.py -s
```
* Start OVS with DPDK
```
$ ovs_mini_tools.py start_ovs_dpdk
```
* Show ovs architecture
```
$ sudo ovs-vsctl show

[ATVN]Parsed command (i:1,argc:1,start:0): show (null)...(1 arguments)
[ATVN]ovs-vsctl (show (null) ...) is processed now!
134fd616-b877-402a-81a9-696af8c2978d
```
* Add bridge for ovs architecture
```
$ sudo ovs-vsctl add-br ovs-dpdk-br0 -- set bridge ovs-dpdk-br0 datapath_type=netdev

$ sudo ovs-vsctl show
[ATVN]Parsed command (i:1,argc:1,start:0): show (null)...(1 arguments)
[ATVN]ovs-vsctl (show (null) ...) is processed now!
134fd616-b877-402a-81a9-696af8c2978d
    Bridge "ovs-dpdk-br0"
        Port "ovs-dpdk-br0"
            Interface "ovs-dpdk-br0"
                type: internal
```
* Add ports architecture
```
$ ovs_mini_tools.py init_ovs_dpdk_bridge --bridge-vports=ovs-br0-vport1,ovs-br0-vport2,ovs-br0-vport3,ovs-br0-vport4 --bridge-name=ovs-dpdk-br0

$ sudo ovs-vsctl show

[ATVN]Parsed command (i:1,argc:1,start:0): show (null)...(1 arguments)
[ATVN]ovs-vsctl (show (null) ...) is processed now!
134fd616-b877-402a-81a9-696af8c2978d
    Bridge "ovs-dpdk-br0"
        Port "ovs-br0-vport3"
            Interface "ovs-br0-vport3"
                type: dpdkvhostuser
        Port "ovs-br0-vport4"
            Interface "ovs-br0-vport4"
                type: dpdkvhostuser
        Port "ovs-dpdk-br0"
            Interface "ovs-dpdk-br0"
                type: internal
        Port "ovs-br0-vport1"
            Interface "ovs-br0-vport1"
                type: dpdkvhostuser
        Port "ovs-br0-vport2"
            Interface "ovs-br0-vport2"
                type: dpdkvhostuser

```

* Show type ovs

```
$ sudo ovs-appctl -t ovs-vswitchd qos/show-types ovs-br0-vport2

QoS type: egress-hqos2l-policer
QoS type: egress-policer
QoS type: egress-policer-rfc2698
```


```
$ sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2
QoS not configured on ovs-br0-vport2
```
* Show interface port 1
```
$ sudo ovs-vsctl list interface ovs-br0-vport1
```

```
$ iperf -s -u -p 8080
```

```
$ iperf -c 192.168.2.12 -u -p 8080 -b 100m
```

```
$ iperf -s -u -p 8080
```

```
$ iperf -c 192.168.2.12 -u -p 8080 -b 100m
```
* Show configure port 2
```
$ sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2
QoS: ovs-br0-vport2 egress-policer
cir: 1250000
cbs: 2048
```
* Set config port 2
```
$ sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- --id=@newqos create qos type=egresspolicer-rfc2698 other-config:cir=46000000 other-config:cbs=2048 other-config:pir=46000000 other-config:pbs=2048 other-config:ydropt=false other-config:rdropt=true
```
* Set config port 2
```
$ sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- --id=@newqos create qos type=egress-policer-rfc2698 other-config:cir=46000000 other-config:cbs=2048 other-config:pir=46000000 other-config:pbs=2048 other-config:ydropt=false other-config:rdropt=true
```
* Show config port 2
```
$ sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2

QoS: ovs-br0-vport2 egress-policer-rfc2698
cir: 46000000
pbs: 2048
rdropt: true
cbs: 2048
ydropt: false
pir: 46000000
```
* Set interface port 1
```
$ sudo ovs-vsctl set interface ovs-br0-vport1 ingress_policing_rate=1024 ingress_policing_burst=128
```
* Show interface port 1
```
$ sudo ovs-vsctl list interface ovs-br0-vport1
```

```
$ iperf -s -u -p 8080

------------------------------------------------------------
Server listening on UDP port 8080
Receiving 1470 byte datagrams
UDP buffer size:  208 KByte (default)
------------------------------------------------------------
[  3] local 192.168.2.12 port 8080 connected with 192.168.2.13 port 53493
[ ID] Interval       Transfer     Bandwidth        Jitter   Lost/Total Datagrams
[  3]  0.0-10.3 sec  1.21 MBytes   990 Kbits/sec  15.669 ms 84171/85034 (99%)
```

```
$ iperf -c 192.168.2.12 -u -p 8080 -b 100m

------------------------------------------------------------
Client connecting to 192.168.2.12, UDP port 8080
Sending 1470 byte datagrams, IPG target: 117.60 us (kalman adjust)
UDP buffer size:  208 KByte (default)
------------------------------------------------------------
[  3] local 192.168.2.13 port 53493 connected with 192.168.2.12 port 8080
[ ID] Interval       Transfer     Bandwidth
[  3]  0.0-10.0 sec   119 MBytes   100 Mbits/sec
[  3] Sent 85035 datagrams
[  3] Server Report:
[  3]  0.0-10.3 sec  1.21 MBytes   990 Kbits/sec   0.000 ms 84171/85034 (0%)
```
* Set port with options
```
$ sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- \
--id=@newqos create qos type=egress-hqos2l-policer other-config:cir=0 other-config:cbs=2048 other-config:pir=31457280 other-config:pbs=2048 other-config:ydropt=true \
qos=@qospl11,@qospl12,@qospl13,@qospl14 -- \
--id=@qospl11 create qos type=egress-policer-rfc2698 other-config:cir=1024 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:1=@queue1 -- \
--id=@qospl12 create qos type=egress-policer-rfc2698 other-config:cir=2048 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:2=@queue2 -- \
--id=@qospl13 create qos type=egress-policer-rfc2698 other-config:cir=4096 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:4=@queue4 -- \
--id=@qospl14 create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:8=@queue8 -- \
--id=@queue1 create queue other-config:desc=rfu -- \
--id=@queue2 create queue other-config:desc=rfu -- \
--id=@queue4 create queue other-config:desc=rfu -- \
--id=@queue8 create queue other-config:desc=rfu
```


* Dump port 2
```
$ sudo ovs-ofctl dump-ports ovs-dpdk-br0 ovs-br0-vport2
```
* Dump port 2 with 
```
$ sudo ovs-ofctl --protocols=OpenFlow14 dump-ports ovs-dpdk-br0 ovs-br0-vport2
```

```
$ sudo ovs-ofctl dump-ports-desc ovs-dpdk-br0

[ATVN]dump_transaction()/if case()
2019-12-10T03:44:21Z|00001|ofctl|INFO|[ATVN]Print the result now
OFPST_PORT_DESC reply (xid=0x2):
 1(ovs-br0-vport1): addr:00:00:00:00:00:00
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
 2(ovs-br0-vport2): addr:00:00:00:00:00:00
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
 3(ovs-br0-vport3): addr:00:00:00:00:00:00
     config:     0
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
 4(ovs-br0-vport4): addr:00:00:00:00:00:00
     config:     0
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
 LOCAL(ovs-dpdk-br0): addr:ba:6e:67:3f:6d:44
     config:     0
     state:      0
     current:    10MB-FD COPPER
     speed: 10 Mbps now, 0 Mbps max
2019-12-10T03:44:21Z|00002|ofctl|INFO|[ATVN]Done
```

```
$ sudo ovs-appctl ofproto/trace ovs-dpdk-br0 in_port=ovs-br0-vport1

Flow: in_port=1,vlan_tci=0x0000,dl_src=00:00:00:00:00:00,dl_dst=00:00:00:00:00:00,dl_type=0x0000

bridge("ovs-dpdk-br0")
----------------------
 0. priority 0
    NORMAL
     -> no learned MAC for destination, flooding

Final flow: unchanged
Megaflow: recirc_id=0,eth,in_port=1,vlan_tci=0x0000/0x1fff,dl_src=00:00:00:00:00:00,dl_dst=00:00:00:00:00:00,dl_type=0x0000
Datapath actions: 1,3,4,5
```

* Show dump flow of ovs
```
$ sudo ovs-ofctl dump-flows ovs-dpdk-br0
```

sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x11,in_port="ovs-br0-vport1",actions=set_queue:8,output:ovs-br0-vport2
sudo ovs-ofctl dump-flows ovs-dpdk-br0
sudo ovs-ofctl del-flows ovs-dpdk-br0 cookie=0x11/-1
sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x11,in_port="ovs-br0-vport1",actions=set_queue:8,output:normal

## PHY-OVS-PHY model
## 4096 input flows (queues)