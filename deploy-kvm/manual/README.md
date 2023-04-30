## 1. Verify Your Device Support Virtual Machine

```
sudo apt-get install cpu-checker -y
kvm-ok
```
## 2. Install KVM / QEMU on Ubuntu

```
sudo apt update && \
sudo apt -y install qemu-kvm libvirt-daemon bridge-utils virtinst libvirt-daemon-system \
virt-top libguestfs-tools libosinfo-bin  qemu-system virt-manager cloud-image-utils bridge-utils
```
After it run command on bellow for run apps & verif
```
sudo modprobe vhost_net 
lsmod | grep vhost
echo vhost_net | sudo tee -a /etc/modules
```
## 3. Create Bridge for Network VM

Edit netplan
```
network:
  version: 2
  renderer: networkd

  ethernets:
    enp1s0:
      dhcp4: false 
      dhcp6: false 
      #addresses: [192.168.1.239/24]
      #gateway4: 192.168.1.1
      #mtu: 1500
      #nameservers:
      #  addresses: [8.8.8.8]

  bridges:
    br0:
      interfaces: [enp1s0]
      addresses: [192.168.1.239/24]
      gateway4: 192.168.1.1
      mtu: 1500
      nameservers:
        addresses: [8.8.8.8]
      parameters:
        stp: true
        forward-delay: 4
      dhcp4: no
      dhcp6: no
```

## 4. Create VM

### Prepare
Download Image Cloud version and costume

```
wget https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img

qemu-img info focal-server-cloudimg-amd64.img
qemu-img resize focal-server-cloudimg-amd64.img 10G  ## Change virtual disk/ root
qemu-img info focal-server-cloudimg-amd64.img

qemu-img convert -f qcow2 focal-server-cloudimg-amd64.img ubuntu20.img
```

Create Cloud-init
```
$ nano init.txt

#cloud-config
ssh_pwauth: true
disable_root: false

chpasswd:
  list: |
    root:password
    tshoot:recovery
  expire: false

users:
  - name: root
    shell: /bin/bash
    ssh-authorized-keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDShGceCPUf9+LeqJk3XYojukXx0rDmJWfuV1/5x/hicY04a9gzNj5X+2UtiPTXh9O214NyoEEoj8FIAKOubWYdovBJotfwF9T1AqTnujEITz69wiQYYwl2y0Fs86v3FJt3wX/6nDrTFpW98/s4oRYYu0J207fK4G0GQOlgfCbH3CWoBWkCP8RHp68VBccrrZsv67PRwWzD0mtvBXpNbCDvL2blsa35TJ/PiE43BJ/wd6sCHWW+UAm+3gZ7/cqjJANANEpWA9r6p6P3Ly3//MrLX1QpQGQNsookmh8HWI60bTYhPZXLnnRbxByKXfsndCc4gn/XulvL2XTUyffaJ/KwN5uFeFfD8KVnSC7K6JbH75db7tUKU16g9kVewFwqlwpl/fSnid+gy+wftfCbV53jOMLlbpQUgWRRjoDtgWbybvLsY60ZI3VwsG0cn7esbMRM2TbPBCkT5sm6hsjIJFQTqQR88pFx5GTuW+XcJtaHoMHKODBo8nJDz0U3IebwEk0= root@pc-lab-2

  - name: tshoot
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    lock_passwd: false
    groups: users, wheel, sudo
    ssh-authorized-keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDShGceCPUf9+LeqJk3XYojukXx0rDmJWfuV1/5x/hicY04a9gzNj5X+2UtiPTXh9O214NyoEEoj8FIAKOubWYdovBJotfwF9T1AqTnujEITz69wiQYYwl2y0Fs86v3FJt3wX/6nDrTFpW98/s4oRYYu0J207fK4G0GQOlgfCbH3CWoBWkCP8RHp68VBccrrZsv67PRwWzD0mtvBXpNbCDvL2blsa35TJ/PiE43BJ/wd6sCHWW+UAm+3gZ7/cqjJANANEpWA9r6p6P3Ly3//MrLX1QpQGQNsookmh8HWI60bTYhPZXLnnRbxByKXfsndCc4gn/XulvL2XTUyffaJ/KwN5uFeFfD8KVnSC7K6JbH75db7tUKU16g9kVewFwqlwpl/fSnid+gy+wftfCbV53jOMLlbpQUgWRRjoDtgWbybvLsY60ZI3VwsG0cn7esbMRM2TbPBCkT5sm6hsjIJFQTqQR88pFx5GTuW+XcJtaHoMHKODBo8nJDz0U3IebwEk0= root@pc-lab-2

```

Create Common-init
```
sudo cloud-localds common-init.iso init.txt
```

#### Qemu ver
```
sudo virt-install \
--name ubuntu-test \
--memory 2048 \
--disk ubuntu20.img,device=disk,bus=virtio \
--disk common-init.iso,device=cdrom \
--os-type linux \
--os-variant ubuntu20.04 \
--virt-type qemu \
--graphics none \
--network network=default,model=virtio \
--import
```

#### KVM ver
```
sudo virt-install \
--name ubuntu-test \
--memory 2048 \
--disk ubuntu20.img,device=disk,bus=virtio \
--disk common-init.iso,device=cdrom \
--os-type linux \
--os-variant ubuntu20.04 \
--virt-type kvm \
--graphics none \
--network bridge=br0,model=virtio \
--import
```

## Noted
parameter " --network network=default"
got from default network virsh
```
virsh net-list
```

## Reference<br>
- https://fabianlee.org/2019/04/01/kvm-creating-a-bridged-network-with-netplan-on-ubuntu-bionic/
- https://computingforgeeks.com/install-kvm-hypervisor-on-ubuntu-linux/
- https://www.theurbanpenguin.com/using-cloud-images-in-kvm/
- https://zhimin-wen.medium.com/provision-a-vm-with-cloud-image-and-cloud-init-36f356a33b90

<br>

[Back](../)


