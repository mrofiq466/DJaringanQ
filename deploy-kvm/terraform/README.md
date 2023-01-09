## 1. Install Terraform on Ubuntu Server
Prepare already installed Qemu-KVM on your ubuntu server. If not, you can open the following [link](../manual/).<br>
then,
```
sudo apt update && \
sudo apt -y install wget curl unzip
```
Download binnary code. [check release](https://github.com/hashicorp/terraform/releases)
```
TER_VER=`curl -s https://api.github.com/repos/hashicorp/terraform/releases/latest | grep tag_name | cut -d: -f2 | tr -d \"\,\v | awk '{$1=$1};1'`
wget https://releases.hashicorp.com/terraform/${TER_VER}/terraform_${TER_VER}_linux_amd64.zip


unzip terraform_${TER_VER}_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```
Verified it
```
terraform version
libvirtd --version
virsh -c qemu:///system version
```

## 2. Try Install VM with Terraform

## Reference
- https://github.com/dmacvicar/terraform-provider-libvirt/tree/main/examples/v0.13/ubuntu
- https://github.com/dmacvicar/terraform-provider-libvirt/
- https://registry.terraform.io/providers/dmacvicar/libvirt/latest
- https://computingforgeeks.com/how-to-provision-vms-on-kvm-with-terraform/
- https://computingforgeeks.com/how-to-install-terraform-on-linux/
- https://developer.hashicorp.com/terraform/downloads
