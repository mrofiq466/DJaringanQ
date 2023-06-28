## 1. Install Package
````bash
apt install python3-dev python3-pip python3 virtualenv
````
## 2. Create Virtual Enviroment
````bash
virtualenv opsclient
cd opsclient
source bin/activate
pip3 install python-openstackclient
pip3 install python-keystoneclient
````
## 3. Create RC File
````bash
nano openrc

''
#export OS_AUTH_URL=https://10.10.10.30:5000/v3
export OS_AUTH_URL=https://keystone.opsclient.com:5000/v3
export OS_CACERT=/root/opsclient/cacert.pem
#export OS_TENANT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_PROJECT_ID=bd3b936138a34030936e3a04763acf4c  # ID Project admin
export OS_PROJECT_DOMAIN_NAME=Default
export OS_AUTH_TYPE=password
#export OS_INTERFACE=public
export OS_USERNAME=admin
export OS_PASSWORD=Rahasia123
export OS_REGION_NAME=Blitar
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
export PS1='[(admin)\u@\h:$(pwd)]$ '
''

````
## 4. Mapping Domain
````bash
nano /etc/hosts

''
10.10.10.30 keystone.opsclient.com
''

````
## 5. Verify
````bash
source openrc
openstack project list
````

# Reference
- https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html
- https://docs.openstack.org/newton/user-guide/common/cli-set-environment-variables-using-openstack-rc.html
- https://www.youtube.com/watch?v=CPgP_m2dtm4

[Back](../)
