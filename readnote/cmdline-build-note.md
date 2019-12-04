# BUILD ENVIROMENT DPDK
## 1. Config & build DPDK

__Build default enviroment DPDK__
```
$ make config T=x86_64-native-linuxapp-gcc #configure Target to build

$ make DESTDIR=[path/to/folder DPDK] prefix=[prefix] #default value of prefix=/usr/local

$ make install DESTDIR=[path/to/DPDK/folder]
```

___Example___: Build an simple application "helloworld"
```python
# Build app helloworld

$ cd examples
$ ls
# Khai báo biến môi trường rte_sdk
$ export RTE_SDK=`pwd`
# khai báo biến lưu folder đích khi build
$ export RTE_TARGET=x86_64-native-linuxapp-gcc
# Set cấu hình
$ make config T=$RTE_TARGET
# Build cấu hình
$ make -j8
# Cài đặt
$ make install T=$RTE_TARGET DESTDIR=install -j8
# Build app
$ cd examples/helloworld/
$ make
$ ./build/app/helloworld --help
# Run app
$ sudo ./build/app/helloworld --file-prefix thuong -l 0-4 
```
## 2. Config & build app Testpmd


```python



```