 ```python
  $ mount -t hugetlbfs nodev /mnt/huge
  $ echo 1024 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
  $ grep "Hugepagesize:" /proc/meminfo
  $ modprobe uio
  $ insmod ./x86_64-native-linux-gcc/kmod/igb_uio.ko
  $ cd x86_64-native-linuxapp-gcc/
  $ ls
  $ insmod ./kmod/igb_uio.ko
  $ ./x86_64-native-linux-gcc/app/testpmd -l 0-3 -n 4 -- -i
  $ ./app/testpmd -l 0-3 -n 4 -- -i
  $ sudo lshw -c network -businfo
  $ vncserver -list
  $ ps aux|grep Xvnc
  $ sudo passwd thuongtxt
  $ sudo -i
  $ make config T=x86_64-native-linuxapp-gcc
  $ cd dpdk/
  $ make config T=x86_64-native-linuxapp-gcc
  $ make
  $ make -j8
  $ sudo modprobe uio
  $ cd x86_64-native-linuxapp-gcc/
  $ sudo insmod ./build/kmod/igb_uio.ko
  $ sudo ./usertools/dpdk-setup.sh 
  $ sudo ./build/app/testpmd -l 12,13,14 -n 4 -- -i
  $ sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:03:00.0 0000:03:00.1
  $ sudo ./build/app/testpmd \u2013l 12,13,14 \u2013n 4 -- -i
  $ sudo ./testpmd -l 0-3 -n 4 -- -i --portmask=0x1 --nb-cores=2
  $ ls x86_64-native-linuxapp-gcc/app/
  $ sudo ./x86_64-native-linuxapp-gcc/app/testpmd -l 0-3 -n 4 -- -i --portmask=0x1 --nb-cores=2
  $ sudo ./usertools/dpdk-devbind.py -b igb_uio 0000:03:00.0 0000:04:00.1
  $ sudo ./build/app/testpmd \u2013l 12,13,14 \u2013n 4 -- -i
  $ sudo ./testpmd -l 0-3 -n 4 -- -i --portmask=0x1 --nb-cores=2
  $ sudo ./x86_64-native-linuxapp-gcc/app/testpmd -l 0-3 -n 4 -- -i --portmask=0x1 --nb-cores=2
  $ cd x86_64-native-linuxapp-gcc/
  $ make config T=x86_64-native-linuxapp-gcc
  $ make
  $ make -j8
  $ sudo ./x86_64-native-linuxapp-gcc/app/testpmd -l 0-3 -n 4 -- -i --portmask=0x1 --nb-cores=2
  $ cd -
  $ sudo ./x86_64-native-linuxapp-gcc/app/testpmd -l 0-3 -n 4 -- -i --portmask=0x1 --nb-cores=2
  $ grep "Hugepagesize:" /proc/memingo
  $ grep "Hugepagesize:" /proc/meminfo
  $ modprobe uio
  $ insmod ./x86_64-native-linux-gcc/kmod/igb_uio.ko
  $ cat ./x86_64-native-linuxapp-gcc/kmod/igb_uio.ko 
  $ clear
  $ modprobe vfio-pci
  $ sudo modprobe vfio-pci
  $ sudo chmod a+x /dev/vfio
  $ sudo chmod 0666 /dev/vfio/*
  $ ./usertools/dpdk-devbind.py --bind vfio-pci 0000.03.00.0 000.
  $ ./usertools/dpdk-devbind.py --bind vfio-pci 0000.03.00.0 0000
  $ ./usertools/dpdk-devbind.py --bind vfio-pci 0000.03.00.0 0000.03.00.1
  $ ./x86_64-native-linux-gcc/app/testpmd -l 0-3 -n 4 -- -i
  $ sudo ./x86_64-native-linux-gcc/app/testpmd -l 0-3 -n 4 -- -i
  $ ls
  $ sudo ./x86_64-native-linuxapp-gcc/app/testpmd -l 0-3 -n 4 -- -i
  $ grep HugePages_ /proc/meminfo
  $ ipcs -m
  $ sudo ./build/app/testpmd \u2013l 12,13,14 \u2013n 4 -- -i
  $ htop
  $ cd
  $ cd dpdk/
  $ sudo ./b
  $ ps -aux | grep testpmd
  $ sudo kill -9 4253 4187 
  $ htop
  $ vncconfig
  $ mv dpdk dpdk-proc
  $ git clone https://github.com/dpdk/dpdk.git
  $ cd dpdk
  $ ls
  $ cat /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages
  $ cat /sys/devices/system/node/node1/hugepages/hugepages-2048kB/nr_hugepages
  $ ls
  $ cd ~/dpdk/
  $ ls
  $ sudo su
  $ ls
  ####################
  # Build app in DPDK
  $ cd examples/helloworld/ 
  $ ls
  $ make
  $ cd -
  $ export RTE_SDK=`pwd`
  $ export RTE_TARGET=x86_64-native-linuxapp-gcc
  $ make config T=x86_64-native-linuxapp-gcc
  $ make -j8
  $ cd examples/helloworld/
  $ ls
  $ make
  $ cd -
  $ ls
  $ make install T=$RTE_TARGET DESTDIR=install -j8
  $ cd examples/helloworld/
  $ make 
  $ ls
  $ cd -
  $ ll
  $ cd -
  $ ls
  $ ./build/app/helloworld --help
  $ ./build/app/helloworld --file-prefix thuong -l 0-4 
  $ sudo ./build/app/helloworld --file-prefix thuong -l 0-4 
  #######################

  #######################
  #Build testpmd
  $ cd -
  $ ./usertools/dpdk-devbind.py -s
  $ ./usertools/dpdk-devbind.py --bind vfio-pci 0000.03.00.0 0000.03.00.1
  $ ./usertools/dpdk-devbind.py --help 
  $ sudo ./usertools/dpdk-devbind.py --bind vfio-pci 0000.03.00.0 0000.03.00.1
  $ sudo ./usertools/dpdk-devbind.py --bind 
  $ ./usertools/dpdk-devbind.py -s
  $ sudo ./build/app/testpmd 
  $ sudo ./build/app/testpmd --file-prefix "akjk"
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0 l 12,13,14 n 4  -- -i
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0 -l 12,13,14 n 4  -- -i
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0 -l 12,13,14 -n 4  -- -i
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0 -l 15 -n 4  -- -i
  $ ./usertools/cpu_layout.py 
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0 -l 29 -n 4  -- -i
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0 -l 29,26 -n 4  -- -i
  $ cd examples/helloworld/
  $ ./build/app/helloworld  --file-prefix "akjk" -w 0000:03:00.0 -l 29 -n 4 
  $ sudo ./build/app/helloworld  --file-prefix "akjk" -w 0000:03:00.0 -l 29 -n 4 
  $ cd -
  $ show config fwd
  $ sudo ./build/app/testpmd --file-prefix "akjk" -w 0000:03:00.0 -l 29,26 -n 4  -- -i
  $ ./usertools/cpu_layout.py 
  $ ls
  $ ./usertools/dpdk-devbind.py -s
  #####################
  $ htop
```