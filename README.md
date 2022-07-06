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
* Using the search bar look for "domain" and click **Application Domain** then Click Add to create the new domain
![dp5](https://github.com/fxnaranjo/datapower-operator/raw/main/images/5.png "dp5")
![dp6](https://github.com/fxnaranjo/datapower-operator/raw/main/images/6.png "dp6")
* Enter the name for the new domain and click apply, in the example the new domain is called **testing**
![dp7](https://github.com/fxnaranjo/datapower-operator/raw/main/images/7.png "dp7")
#### 2.2 Create a new Multi-Protocol Gateway
##### 2.2.1 Change to the new created domain, Click on **Control Panel** and then in **Multi-Protocol Gateway**, Click Add to create the new service
![dp8](https://github.com/fxnaranjo/datapower-operator/raw/main/images/8.png "dp8")
![dp9](https://github.com/fxnaranjo/datapower-operator/raw/main/images/9.png "dp9")
##### 2.2.2 Create the new Multi-Protocol Gateway with the following parameters
  - Name: mympg
  - Type: static-backend
  - Default Backend URL: https://petstore.swagger.io/
  - Processing Policy: Create a new one
  - Front Side Protocol: Create a new HTTP Handler
  - TLS client profile: Create a new one
![dp15](https://github.com/fxnaranjo/datapower-operator/raw/main/images/15.png "dp15")
##### 2.2.3 Configuration for Processing Policy
![dp12](https://github.com/fxnaranjo/datapower-operator/raw/main/images/12.png "dp12")
![dp11](https://github.com/fxnaranjo/datapower-operator/raw/main/images/11.png "dp11")
##### 2.2.4 Configuration for HTTP Handler
![dp13](https://github.com/fxnaranjo/datapower-operator/raw/main/images/13.png "dp13")
##### 2.2.5 Configuration for TLS client profile
![dp14](https://github.com/fxnaranjo/datapower-operator/raw/main/images/14.png "dp14")
#### 2.3 Test the new service
##### 2.3.1 Click apply and save the changes
![dp1x](https://github.com/fxnaranjo/datapower-operator/raw/main/images/1x.png "dp1x")
##### 2.3.2 Use you browser to test the following URL: [http://localhost:4444/v2/pet/findByStatus?status=available](http://localhost:4444/v2/pet/findByStatus?status=available)
![dp16](https://github.com/fxnaranjo/datapower-operator/raw/main/images/16.png "dp16")
