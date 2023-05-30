## 1. Verify Your Device already follow step on
- [Deploy Manual VM (KVM/ Qemu ) on Ubuntu Server](../manual)
- [Deploy VM With Terraform on Ubuntu Server](../terraform)

## 2. Create Pool 
### Store Network
```
nano net-10.10.10.xml
'''
<network>
  <name>net-10.10.10</name>
  <forward mode='route'/>
  <bridge name='virbr10' stp='on' delay='0'/>
  <ip address='10.10.10.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.10.10.2' end='10.10.10.254' />
    </dhcp>
  </ip>
</network>
'''
virsh net-create net-10.10.10.xml
virsh net-destroy net-10.10.10
virsh net-undefine net-10.10.10
virsh net-define net-10.10.10.xml
virsh net-start net-10.10.10
virsh net-autostart net-10.10.10
```
### Store Images
```
mkdir /images
virsh pool-define-as images dir - - - - "/images"
virsh pool-build images
virsh pool-start images
virsh pool-autostart images
```
### Store VM disk
```
mkdir /vms
virsh pool-define-as vms dir - - - - "/vms"
virsh pool-build vms
virsh pool-start vms
virsh pool-autostart vms
```
### Verify it
```
virsh net-list --all
virsh pool-list --all
```

## 3. TVGen Binary
```
git clone https://github.com/mrofiq466/TVGen-Master.git
chmod +x TVGen-Master/tvgen
mv TVGen-Master/tvgen /usr/local/bin/
tvgen -h
```

## 4. Prepare Enviroment
### Set AppArmor
```
nano  /etc/apparmor.d/libvirt/TEMPLATE.qemu
'''
#include <tunables/global>
profile LIBVIRT_TEMPLATE flags=(attach_disconnected) {
  #include <abstractions/libvirt-qemu>
/**.qcow{,2} rwk,
/**.img rwk,
}
'''
apparmor_parser -r /etc/apparmor.d/libvirt/TEMPLATE.qemu
systemctl reload apparmor
```
### Image
```
wget https://cloud-images.ubuntu.com/releases/focal/release/ubuntu-20.04-server-cloudimg-amd64.img
cp ubuntu-20.04-server-cloudimg-amd64.img /images/ubuntu2004.img
virsh pool-refresh --pool images
```
### Create file variable
You can edit/ copy format from test1 and test2, [Read detail](https://github.com/mrofiq466/TVGen-Master).
```
nano test1.txt
'''
[KEY]
PUBKEY1: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDUT+QC2+vIH/zpGqi9cJMyaOlaVE7HrLrAjpsJtkVP6HTv+pBeM0ebU8qU5X7dLOm2/3Xiid8KCnpxJU12FjbpJq2z/w/thhqXNTqWlghhplb2d3vgh7AT5l7JPpeEkwVAQ73YElFK78CvYGiAN02QuGNw8/t0ICM7iatGqxamdbWMhsl5x1cJniU077ykxez8eJvee3NjuC4YSX/LBvWdPZ2O4pNtrALTFhGl8OeyjXlI1mq9J5KMCexlqufr2QDEGocHKbBRw0lxVIb5u0GMF+/YQYfXoWKTpeaj967k1+KHocTzVqsA53gb22/ogPiTShORAEuBbiNXRIbwtF2W7PKAEsSj7AKpcPOboK26ZzK3l8e0Bo/tQRHt5l1XJX+dnHvh2RsugDBN6erOfc35Yls/9Lq3QdNBjcUMMkLU6FOely63+1VpARRWpFGtj9MFEh7SzYXTpqtu5PkE8/ZsdyzCAapypYXChK/yl4nYY4W4yKs+AGiZrE3vzFAnPhE= root@mrofiq466

[VM]
NAME: demo-tvgen1
OS: ubuntu2004.img
VCPUS: 2
MEMORY: 4
DISK1: 10
DISK2: 10
DHCP1: true
IFACE_IP1: default
DHCP2: false
IFACE_IP2: 10.10.10.3
CONSOLE: vnc
'''
tvgen demo test1.txt
cd demo
terraform init
terraform apply -auto-approve
```

## 5. Access Instance
```
virsh net-dhcp-leases default
virsh list
ssh 10.10.10.3 -l tshoot    # password is "help" this user have sudo access
virsh console demo-tvgen1   # use user "root" and psw "password" 
ssh 10.10.10.3 -i ~/id_rsa  # ssh password less 
```

## Noted
File variable it most case-sensitive.<br>
Make sure AppArmor config.<br>
You can use images again from /images to another instance. check on `virsh vol-list vms`<br>
When you create another instance make sure the domain name is different.<p>

## Reference
- [Redhat Doc Pool Virsh](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/virtualization_administration_guide/sect-virtualization-storage_pools-creating-local_directories-virsh)
- [Redhat Doc Net Virsh](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-managing_guest_virtual_machines_with_virsh-managing_virtual_networks)
- https://computingforgeeks.com/managing-kvm-network-interfaces-in-linux/
- https://kashyapc.fedorapeople.org/virt/create-a-new-libvirt-bridge.txt
- https://download.libvirt.org/virshcmdref/html/sect-net-create.html
- https://computingforgeeks.com/virsh-commands-cheatsheet/
- https://sysguides.com/create-and-manage-storage-pools-and-volumes-in-kvm/
- https://registry.terraform.io/providers/dmacvicar/libvirt/latest/docs/resources/volume


<br>

[Back](../)
