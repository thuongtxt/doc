sudo chown thuongtxt:thuongtxt thuongtxt

cp ~/../shared/vm-images/core-ubuntu.qcow 

sudo qemu-system-x86_64  -enable-kvm  -cpu host -smp 4 -m 4096 -hda core-ubuntu.qcow -device virtio-net-pci,netdev=net0 -netdev user,id=net0,hostfwd=tcp::5559-:22 -netdev user,id=n1 -device e1000,netdev=n1 -netdev user,id=n2 -device e1000,netdev=n2 -name vm1
user: linux
pass: 123456


ssh linux@127.0.0.1 -p 5559