This feature for vm support kvm

## 1. Download Virtualbox Latest Version
On my case i use v7.0, and i research virtualbox supports kvm supports on v6.0 & latest.

## 2. Create VM & Setting 
### A. Set Paravirtualization Interface to KVM & enable Hardware Virtualization
![image](https://user-images.githubusercontent.com/59303583/218284404-43cb1352-94e5-4228-83ff-a3c0971506bd.png)
<p>

### B. Enable Extended Features
![image](https://user-images.githubusercontent.com/59303583/218284412-95d16108-cf08-4ab7-9cdc-b792c84a412a.png)

For enable nested
```
cd "Program Files\Oracle\VirtualBox"
VBoxManage modifyvm ubuntu --nested-hw-virt on
```

## Noted
If you vm still not support kvm you can try it<br>
On your Host open windows PowerShell run as administrator and execute
```
bcdedit /set hypervisorlaunchtype off
```
restart your host
  
## Reference<br>
- https://bhanuwriter.com/virtualized-amd-v-rvi-is-disabled/
- https://www.cyberithub.com/how-to-enable-nested-vt-x-amd-v-in-virtualbox-step-by-step/
- https://communities.vmware.com/t5/VMware-Workstation-Player/Virtualized-AMD-V-RVI-is-not-supported-on-this-platform/td-p/2900507

<br>


[Back](../)
