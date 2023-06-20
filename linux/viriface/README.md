
```
nano /etc/netplan/00-installer-config.yaml
'''
network:
  version: 2
  ethernets:
    enp0s10:
      addresses:
        - 192.168.56.101/24
        - 192.168.56.102/24:
            label: enp0s10:1
      dhcp4: false
      dhcp6: false
      routes:
          - to: 192.168.56.0/24
            via: 192.168.56.1
'''
netplan apply
```


[Back](../)
