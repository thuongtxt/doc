* Clean install
$ make clean
* Clean all configure
$ make distclean

* Show list info all queue
$ sudo ovs-appctl qos/show-queue-stats ovs-br0-vport2 
* Show info queue by queue id
$ sudo ovs-appctl qos/show-queue-stats ovs-br0-vport2 4000
* Add flow for port
sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x11,in_port="ovs-br0-vport1",actions=set_queue:8,output:ovs-br0-vport2

#HQoS command testing
# list out supported QoS type of port (already in use)
sudo ovs-appctl -t ovs-vswitchd qos/show-types ovs-br0-vport2
sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2
# clear qos of vport2 & all qos, queue table
sudo ovs-vsctl destroy QoS ovs-br0-vport2 -- clear Port ovs-br0-vport2 qos
sudo ovs-vsctl --all destroy qos -- --all destroy queue
# list out all log module information.
`$ sudo ovs-appctl coverage/show`   //show coverage counters
`$ sudo ovs-appctl vlog/reopen`  // reopen log file
`$ sudo ovs-appctl vlog/list`    // list out all information of vlog
#set a few module to DBG level for all: console, syslog, file
$ sudo ovs-appctl vlog/set dpif_netdev:ANY:DBG
$ sudo ovs-appctl vlog/set netdev_dpdk:ANY:DBG
$ sudo ovs-appctl vlog/set netdev_linux:ANY:DBG
$ sudo ovs-appctl vlog/set hqos:file:DBG

* Add a queue 
sudo ovs-vsctl create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:8004=@queue118 -- --id=@queue118 create queue other-config:desc=rfu

sudo ovs-vsctl create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:8005=@queue11 -- --id=@queue119 create queue other-config:desc=rfu

* Config queues
sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- \
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

* Add & Del flow
sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x11,in_port="ovs-br0-vport1",actions=set_queue:8,output:ovs-br0-vport2
sudo ovs-ofctl dump-flows ovs-dpdk-br0
sudo ovs-ofctl del-flows ovs-dpdk-br0 cookie=0x11/-1
sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x11,in_port="ovs-br0-vport1",actions=set_queue:8,output:normal


sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x14,in_port="ovs-br0-vport1",actions=set_queue:4,output:ovs-br0-vport2


sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- \
--id=@newqos create qos type=egress-hqos2l-policer other-config:cir=4096 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
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

sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x14,in_port="ovs-br0-vport1",actions=set_queue:4,output:ovs-br0-vport2
sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x15,in_port="ovs-br0-vport3",actions=set_queue:2,output:ovs-br0-vport2
sudo ovs-appctl -t ovs-vswitchd qos/show ovs-br0-vport2