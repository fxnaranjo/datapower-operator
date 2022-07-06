# Using IBM Datapower Operator

This guide is intented to guide on how to create a development enviroment for IBM Datapower, and use the generated configuration to generate a Datapower Service in Red Hat Openshift using the IBM Datapower Operator

***
## Prerequisites
1. Red Hat Openshift Cluster 4.8.X
2. Computer with podman or docker
3. Internet Access
4. IBM Entitled Key to access IBM Container Registry (you can also use the IBM Datapower docker image available on docker hub)

### 1. Creating the IBM Datapower Docker Development Environment

#### 1.1 Create a directory structure to hold the configuration files
```
user@mypc:$ mkdir /home/fnaranjo/DEMOS/datapower
user@mypc:$ mkdir certs local config
user@mypc:$ cd /home/fnaranjo/DEMOS/datapower
```
#### 1.2 Login into the container registry
```
user@mypc:$ podman login cp.icr.io
Username: cp
Password: <IBM Entitled Key>
```
#### 1.3 Start the IBM Datapower Container
```
user@mypc:$ docker run -it --name mydp \
-v $(pwd)/config:/opt/ibm/datapower/drouter/config \
-v $(pwd)/local:/opt/ibm/datapower/drouter/local \
-v $(pwd)/certs:/opt/ibm/datapower/root/secure/usrcerts \
-e DATAPOWER_ACCEPT_LICENSE="true" \
-e DATAPOWER_INTERACTIVE="true" \
-p 9090:9090 \
-p 4444:4444 \
cp.icr.io/cp/datapower/datapower-cp4i:10.0.1.8


------------DOCKER HUB VERSION-------------------

user@mypc:$ docker run -it --name mydp \
-v $(pwd)/config:/opt/ibm/datapower/drouter/config \
-v $(pwd)/local:/opt/ibm/datapower/drouter/local \
-v $(pwd)/certs:/opt/ibm/datapower/root/secure/usrcerts \
-e DATAPOWER_ACCEPT_LICENSE="true" \
-e DATAPOWER_INTERACTIVE="true" \
-p 9090:9090 \
-p 4444:4444 \
docker.io/ibmcom/datapower:latest
```

![dp1](https://github.com/fxnaranjo/datapower-operator/raw/main/images/1.png "dp1")

#### 1.4 Once the container finish up loading access the admin console with admin/admin
#### 1.5 Configure the web console using the following procedure
```
idg# config
Global mode
idg(config)# web-mgmt
idg(config web-mgmt)# admin-state enabled
idg(config web-mgmt)# exit
idg(config)# write mem
```
![dp2](https://github.com/fxnaranjo/datapower-operator/raw/main/images/2.png "dp2")

#### 1.6 Access IBM Datapower Admin Console using the following URL: https://localhost:9090
![dp3](https://github.com/fxnaranjo/datapower-operator/raw/main/images/3.png "dp3")
![dp4](https://github.com/fxnaranjo/datapower-operator/raw/main/images/4.png "dp4")

### 2. Creating a simple Multiprotocol Gateway on IBM Datapower
#### 2.1 Create a new application domain
* Using the search bar look for "domain" and click **Application Domain**
![dp5](https://github.com/fxnaranjo/datapower-operator/raw/main/images/5.png "dp5")
* Click Add to create the new domain
![dp6](https://github.com/fxnaranjo/datapower-operator/raw/main/images/6.png "dp6")
* Enter the name for the new domain and click apply, in the example the new domain is called **testing**
![dp7](https://github.com/fxnaranjo/datapower-operator/raw/main/images/7.png "dp7")
