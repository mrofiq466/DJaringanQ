## 1. Prepare
- Turn off all your virtual machine
- Enable Hypervisor & WSL
````bash
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestar
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
````
- Reload your host

## 2. Install WSL1
Open your PowerShell at administrator user
````bash
wsl --update
wsl --set-default-version 1
wsl -l -o
wsl --install -d Ubuntu  --enable-wsl1    # waiting to install complate and setup upto you can run command `pwd`
wsl --set-version Ubuntu 1
wsl -l -v
````

## 3. Turn off Hypervisor
`bcdedit /set hypervisorlaunchtype off` <br>
Reload

## 4. Verify
Check your vm and wsl1

# Noted
Only support on WSL1 if WSL2 you can choose one of them WSL or Hypervisor

[Back](../)
