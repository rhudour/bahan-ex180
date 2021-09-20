# bahan-ex180

## [**install podman**]
	sudo yum install podman
	sudo podman -v
	konfig registries >> /etc/containers/registries.conf
	konfig cni >> /etc/cni/net.d/87-podman-bridge.conflist
#
## [**run container**]
	[root@workstation do180-practice]#  podman run --name mysql-basic -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e * MYSQL_ROOT_PASSWORD=r00tpa55 -d rhel8/mysql-80
	Trying to pull registry.do180.lab:5000/rhel8/mysql-80...
	manifest unknown: manifest unknown
	Trying to pull registry.access.redhat.com/rhel8/mysql-80...
	unsupported: This repo requires terms acceptance and is only available on registry.redhat.io
	Trying to pull registry.redhat.io/rhel8/mysql-80...
	unable to retrieve auth token: invalid username/password: unauthorized: Please login to the Red Hat Registry using your Customer Portal credentials. Further
	instructions can be found here: https://access.redhat.com/RegistryAuthentication
	Trying to pull docker.io/rhel8/mysql-80...
	denied: requested access to the resource is denied
	Trying to pull quay.io/rhel8/mysql-80...
	unauthorized: access to the requested resource is not authorized
	Error: unable to pull rhel8/mysql-80: 5 errors occurred:
	Error initializing source docker://registry.do180.lab:5000/rhel8/mysql-80:latest: Error reading manifest latest in registry.do180.lab:5000/rhel8/mysql-80: * manifest unknown: manifest unknown
	Error initializing source docker://registry.access.redhat.com/rhel8/mysql-80:latest: Error reading manifest latest in registry.access.redhat.com/rhel8/mysql-80: unsupported: This repo requires terms acceptance and is only available on registry.redhat.io
	Error initializing source docker://registry.redhat.io/rhel8/mysql-80:latest: unable to retrieve auth token: invalid username/password: unauthorized: Please  login to the Red Hat Registry using your Customer Portal credentials. Further instructions can be found here: https://access.redhat.com/RegistryAuthentication
	Error initializing source docker://rhel8/mysql-80:latest: Error reading manifest latest in docker.io/rhel8/mysql-80: errors:
	denied: requested access to the resource is denied 
	unauthorized: authentication required
	Error initializing source docker://quay.io/rhel8/mysql-80:latest: Error reading manifest latest in quay.io/rhel8/mysql-80: unauthorized: access to the requested resource is not authorized
	[root@workstation do180-practice]#
	[root@workstation do180-practice]#
	[root@workstation do180-practice]# podman login registry.redhat.io
	Username: feby.ichsan@datacomm.co.id
	Password:
	Login Succeeded!
	[root@workstation do180-practice]#
	[root@workstation do180-practice]#
	[root@workstation do180-practice]#  podman run --name mysql-basic -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 -d rhel8/mysql-80
	Trying to pull registry.do180.lab:5000/rhel8/mysql-80...
	manifest unknown: manifest unknown
	Trying to pull registry.access.redhat.com/rhel8/mysql-80...
	unsupported: This repo requires terms acceptance and is only available on registry.redhat.io
	Trying to pull registry.redhat.io/rhel8/mysql-80...
	Getting image source signatures
	Copying blob 1c176e0fbe37 done
	Copying blob 356f18f3a935 done
	Copying blob 359fed170a21 done
	Copying blob 296e14ee2414 done
	Copying config 7d3ef14d6a done
	Writing manifest to image destination
	Storing signatures
	087eefc6dd2e2df012587b8dab272982ead8027348bf90855c15cba2172064f8
	[root@workstation do180-practice]#

* [**build image and run as container-1**]
