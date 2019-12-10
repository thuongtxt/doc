# Setup model 4k flows, policer 2 levels. PHY-OVS-PHY
## Policer 2 levels
### Build OVS connect DPDK
tree folder
```
workspace
    |>dpdk-stable-11.18.5
    |>ovs_prj
        |>dpdk-sdk
        |>ovs-software
```
cd ovs
  564  ls
  565  ./configure --help
  566  ./configure --with-dpdk=/home/thuongtxt/dpdk/build --prefix=-software$ ls
  567  ./configure --with-dpdk=/home/thuongtxt/dpdk/build --prefix=/home/thuongtxt/ovs_prj/ovs-software CFLAGS=-DATVN_DEBUG
  568  ls
  569  make
  570  git branch
  571  history

## PHY-OVS-PHY model
## 4096 input flows (queues)