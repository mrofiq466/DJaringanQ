## 1. Install Package
```
apt update && apt install -y
```

## 2. Configure
```
ssh-keygen
nano .ssh/config
'''
Host *
  StrictHostKeyChecking no
  GSSAPIAuthentication no
  UserKnownHostsFile /dev/null
  ServerAliveInterval 10
  TCPKeepAlive yes
  LogLevel ERROR

Host 10.10.0.*
  User root
  IdentityFile ~/.ssh/id_rsa
  StrictHostKeyChecking false
  ServerAliveInterval 240
  TCPKeepAlive yes

Host ctrl1
  HostName 10.10.10.10
  ServerAliveInterval 600
  TCPKeepAlive yes
'''
```

## 3. Verify
```
ssh ctrl1
```



[Back](../)
