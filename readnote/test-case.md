### Add flow for bridge

* Add flow: server -> port 1 (in) -> bridge -> port 2 (out)
```
$ sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x11,in_port="ovs-br0-vport1",actions=set_queue:123,output:ovs-br0-vport2
```
* Add flow: server -> port 1 (in) -> bridge -> No switch (out)
```
$ sudo ovs-ofctl add-flow ovs-dpdk-br0 cookie=0x12,in_port="ovs-br0-vport2",actions=set_queue:234,normal
```

### Test case 1

sudo ovs-vsctl set port ovs-br0-vport2 qos=@newqos -- \
--id=@newqos create qos type=egress-hqos2l-policer other-config:cir=2048 other-config:cbs=2048 other-config:pir=31457280 other-config:pbs=2048 other-config:ydropt=true \
qos=@qospl11,@qospl12,@qospl13,@qospl14,@qospl15,@qospl16,@qospl17,@qospl18,@qospl19,@qospl1a -- \
--id=@qospl11 create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:1=@queue1 -- \
--id=@qospl12 create qos type=egress-policer-rfc2698 other-config:cir=2048 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:2=@queue2 -- \
--id=@qospl13 create qos type=egress-policer-rfc2698 other-config:cir=4096 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:4=@queue4 -- \
--id=@qospl14 create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:8=@queue8 -- \
--id=@qospl15 create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:4001=@queue1 -- \
--id=@qospl16 create qos type=egress-policer-rfc2698 other-config:cir=2048 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:4002=@queue2 -- \
--id=@qospl17 create qos type=egress-policer-rfc2698 other-config:cir=4096 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:4004=@queue4 -- \
--id=@qospl18 create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:4008=@queue8 -- \
--id=@qospl19 create qos type=egress-policer-rfc2698 other-config:cir=4096 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:4000=@queue4 -- \
--id=@qospl1a create qos type=egress-policer-rfc2698 other-config:cir=8192 other-config:cbs=2048 other-config:pir=10240 other-config:pbs=2048 other-config:ydropt=true \
queues:8000=@queue8 -- \
--id=@queue1 create queue other-config:desc=rfu -- \
--id=@queue2 create queue other-config:desc=rfu -- \
--id=@queue4 create queue other-config:desc=rfu -- \
--id=@queue8 create queue other-config:desc=rfu

Index       0   |   1   |   2   |   3   |   4   |   5   |   6   |   7   |   8   |   9   
Value      4008 |   2   |  4002 |   8   |   4   |  8000 |   1   | 4004  |  4000 |  4001

* Show list uuid of queue

sudo ovs-vsctl list qos

* Remove queue
sudo ovs-vsctl remove qos [uuid_queue] qos ???
* Add queue
sudo ovs-vsctl add qos [uuid_queue] qos ???


