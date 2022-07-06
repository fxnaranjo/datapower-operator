# Using IBM Datapower Operator

This guide is intented to show how to create a development enviroment for IBM Datapower, and use the generated configuration to create a Datapower Service in Red Hat Openshift using the IBM Datapower Operator

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

#### 1.4 Once the container finish up loading, access the admin console with admin/admin
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

### 2. Creating a Simple Multiprotocol Gateway on IBM Datapower
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

### 3. Creating an IBM Datapower Gateway in Red Hat Openshift
#### 3.1 Install the IBM Datapower Operator
* Add the online catalog sources to the cluster according to this [link](https://www.ibm.com/docs/en/cloud-paks/cp-integration/2021.4?topic=installing-adding-catalog-sources-online-openshift-cluster)
* Create a new Openshit project called datapower (must be logged-in into the cluster)
```
user@mypc:$ oc new-project datapower
```
* Create a secret to hold the IBM Entlitement Registry Key (must be logged-in into the cluster)
```
user@mypc:$ oc create secret docker-registry ibm-entitlement-key --docker-username=cp --docker-password=<your-key> --docker-server=cp.icr.io -n datapower
```
* Install the IBM Datapower Operator from the Openshift Web Console, install the lastest version and choose the datapower project created earlier
![dp17](https://github.com/fxnaranjo/datapower-operator/raw/main/images/17.png "dp17")
#### 3.2 Create the config maps and secrets to hold the configuration
* Create a secret to hold the IBM datapower admin password
```
user@mypc:$ oc create secret generic datapower-admin-credentials --from-literal=password=<password> -n datapower
```
* Create the config maps that holds the configuration of your development environment (the --from-file path should match your folder structure created earlier)
```
user@mypc:$ oc create configmap default-config --from-file=/home/fnaranjo/DEMOS/datapower/config/default.cfg
user@mypc:$ oc create configmap testing-config --from-file=/home/fnaranjo/DEMOS/datapower/config/testing/testing.cfg
```
* Click the plus icon in the Openshift console and add the configuration for the Datapower Web Console (make sure to be in the datapower project)
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: console-config
data:
  console.cfg: |
    web-mgmt
        admin-state "enabled"
        local-address "0.0.0.0" "9090"
        save-config-overwrite 
        idle-timeout 600
        ssl-config-type server
        no disable-csrf 
        enable-sts 
    exit
```
* Click the plus icon in the Openshift console and add the configuration for the new Datapower Service (make sure to be in the datapower project)
![dp21](https://github.com/fxnaranjo/datapower-operator/raw/main/images/21.png "dp21")
```
apiVersion: datapower.ibm.com/v1beta3
kind: DataPowerService
metadata:
  name: mydp
spec:
  version: 10.0-lts
  license:
    accept: true
    use: production
    license: L-RJON-CCCPAN
  replicas: 1
  users:
  - name: admin
    passwordSecret: datapower-admin-credentials
    accessLevel: privileged
  domains:
  - name: "default"
    dpApp:
      config:
      - "default-config"
      - "console-config"
  - name: "testing"
    dpApp:
      config:
      - "testing-config"
```
* Click the plus icon in the Openshift console and add the configuration for the new Openshift Service needed to access the Datapower (make sure to be in the datapower project)
![dp22](https://github.com/fxnaranjo/datapower-operator/raw/main/images/22.png "dp22")
```
apiVersion: v1
kind: Service
metadata:
  name: mydatapower-svc
  namespace: datapower
spec:
  selector:
    app.kubernetes.io/component: datapower
    app.kubernetes.io/instance: datapower-mydp
  ports:
  - protocol: TCP
    port: 9090
    targetPort: 9090
    name: console
  - protocol: TCP
    port: 4444
    targetPort: 4444
    name: service
```
* Go to the pods view to verify if the new Datapower container is running
![dp23](https://github.com/fxnaranjo/datapower-operator/raw/main/images/23.png "dp23")
* Create the IBM Datapower Console route
![dp27](https://github.com/fxnaranjo/datapower-operator/raw/main/images/27.png "dp27")
* Create the IBM Datapower Service route
![dp28](https://github.com/fxnaranjo/datapower-operator/raw/main/images/28.png "dp28")
* Access the console from the created route and using the credentials created earlier
![dp24](https://github.com/fxnaranjo/datapower-operator/raw/main/images/24.png "dp24")
* In the console verify that that all the configuration required is present
![dp25](https://github.com/fxnaranjo/datapower-operator/raw/main/images/25.png "dp25")
![dp26](https://github.com/fxnaranjo/datapower-operator/raw/main/images/26.png "dp26")
* Test the service using the created route and adding the following context **/v2/pet/findByStatus?status=available**
![dp2x](https://github.com/fxnaranjo/datapower-operator/raw/main/images/2x.png "dp2x")
![dp29](https://github.com/fxnaranjo/datapower-operator/raw/main/images/29.png "dp29")

## CONGRATULATIONS!! You have successfully deployed an IBM Datapower Service with custom configuration















