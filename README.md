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
**user@mypc:$** mkdir /home/fnaranjo/DEMOS/datapower
**user@mypc:$** mkdir certs local config
```
* Login into the container registry
```
**user@mypc:$** podman login cp.icr.io
**Username:** cp
**Password:** <IBM Entitled Key>
```
