# Using IBM Datapower Operator

This guide is intented to guide on how to create a development enviroment for IBM Datapower, and use the generated configuration to generate a Datapower Service in Red Hat Openshift using the IBM Datapower Operator

***
## Prerequisites
1. Red Hat Openshift Cluster 4.8.X
2. Computer with podman or docker
3. Internet Access
4. IBM Entitled Key to access IBM Container Registry (you can also use the IBM Datapower docker image available on docker hub)

### 1. Creating the IBM Datapower Docker Development Environment

* Create a directory structure to hold the configuration files
```
user@mypc:$ mkdir /home/fnaranjo/DEMOS/datapower
user@mypc:$ mkdir certs local config
user@mypc:$ cd /home/fnaranjo/DEMOS/datapower
```
* Login into the container registry
```
user@mypc:$ podman login cp.icr.io
Username: cp
Password: <IBM Entitled Key>
```
* Start the IBM Datapower Container
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

![dp1](https://github.com/fxnaranjo/datapower-operator/raw/main/images/11.png "dp1")
