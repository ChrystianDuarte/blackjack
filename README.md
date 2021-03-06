# Blackjack and OpenBanking
Black Jack Front End Demo for Open Banking at Scale Demo

Credits: 
Colaborartor: 
Tomas Gubelli - https://github.com/tgubeli/blackjack 
Francisco Meneses - https://github.com/fmenesesg
Paulo Seguel - https://github.com/pseguel/
Chrystian Duarte
Event: Tech Excahnge 2019
Openbank at Scale Team

Third party: OpenBankProject

## 1. Set Up Red Hat OpenShift Service Mesh on Cluster

Installing the Service Mesh involves :

1.  Installing Elasticsearch, Jaeger, Kiali

2.  Installing the Service Mesh Operator
    
3.  Creating and managing a  _ServiceMeshControlPlane_  resource to deploy the Service Mesh control plane
    
4.  Creating a  _ServiceMeshMemberRoll_  resource to specify the namespaces associated with the Service Mesh.
   
NOTE

The latest supported product installation instructions are located  [here](https://docs.openshift.com/container-platform/4.1/service_mesh/service_mesh_install/installing-ossm.html).

### 1.1. Install Service Mesh Operator Dependencies

The Red Hat OpenShift Service Mesh Operator has dependencies  _Elasticsearch_,  _Jaeger_  and  _Kiali_  operators.

In this section of the lab, you will install these operator dependencies from the  _Catalog_  of your OCP web console (which pulls operators from:  [OperatorHub](https://operatorhub.io/)).

#### 1.1.1. OCP Web Console  _Catalog_

1.  From the previous lab, you should already have a tab in your browser opened to the OCP Web Console.
    
    If you do not still have the OCP Web Console open, its URL can be identified by executing the following in the remote  _bastion_  node of your lab environment:
    
    ```
    $ echo -en "\n\nhttps://`oc get route console -o template --template {{.spec.host}} -n openshift-console`\n"
    ```
    
    Log in using credentials of:  `admin / r3dh4t1!`
    
2.  In the OCP Web Console, navigate to:  `Catalog -> Operator Hub`

#### 1.1.2. Install Elasticsearch Operator

1.  In the  _OperatorHub_  catalog of your OCP Web Console, type  **Elasticsearch**  into the filter box to locate the Elasticsearch Operator.
   
![system schema](img/operatorhub_es.png)

    
2.  Click the Elasticsearch Operator to display information about the Operator
    
3.  Click Install
    
4.  On the  _Create Operator Subscription_  page, specify the following:
    
    1.  Select  `All namespaces on the cluster (default)`.
        
        This installs the Operator in the default  _openshift-operators_  project and makes the Operator available to all projects in the cluster.
        
    2.  Select the preview Update Channel.
        
    3.  Select the Automatic Approval Strategy.
        
    4.  Click Subscribe
        
    
5.  The  _Subscription Overview_  page displays the Elasticsearch Operator’s installation progress.
    
6.  After about a minute, at the command line, view the new resource that represents the Elasticsearch Operator:
    
    ```
    $ oc get ClusterServiceVersion
    
    NAME                                         DISPLAY                  VERSION               REPLACES   PHASE
    elasticsearch-operator.4.1.14-201908291507   Elasticsearch Operator   4.1.14-201908291507              Succeeded
    ```
    
7.  After about a minute, view the status of the Elasticsearch operator pod in the  _openshift-operators_  namespace:
    
    ```
    $ oc get pod  -n openshift-operators | grep "^elasticsearch"
    
    elasticsearch-operator-6c4fdc5975-pcx88   1/1     Running   0          1d2h
    ```
#### 1.1.3. Install Jaeger Operator

1.  In the  _OperatorHub_  catalog of your OCP Web Console, type  **Jaeger**  into the filter box to locate the Elasticsearch Operator.
    
    ![system schema](img/operatorhub_jaeger.png)
    
2.  Click the  _Jaeger Operator provided by Red Hat_  to display information about the Operator.
    
3.  Click Install.
    
4.  On the  _Create Operator Subscription_  page, select :
    
    1.  All namespaces on the cluster (default).
        
    2.  Select the stable Update Channel.
        
    3.  Select the Automatic Approval Strategy.
        
    4.  Click Subscribe
        
    
5.  The  _Subscription Overview_  page displays the Jaeger Operator’s installation progress.
    
6.  After about a minute, at the command line, view the new resource that represents the Jaeger Operator:
    
    ```
    $ oc get ClusterServiceVersion | grep jaeger
    
    
    jaeger-operator.v1.13.1                      Jaeger Operator          1.13.1                           Succeeded
    ```
    
7.  View the status of the Jaeger operator pod in the  _openshift-operators_  namespace:
    
    ```
    $ oc get pod  -n openshift-operators | grep "^jaeger"
    
    
    jaeger-operator-98cc68944-rgmcc   1/1     Running   0          1d2h
    ```
    

#### 1.1.4. Install Kiali Operator

1.  In the  _OperatorHub_  catalog of your OCP Web Console, type  **Kiali Operator**  into the filter box to locate the Elasticsearch Operator.
    
    ![system schema](img/operatorhub_kiali.png)
    
2.  Click the  _Kiali Operator provided by Red Hat_  to display information about the Operator.
    
3.  Click Install.
    
4.  Populate the entries of the  _Create Operator Subscription_  page as follows :
    
    1.  Select  _All namespaces_  on the cluster (default)
        
    2.  Select the  _stable Update Channel_
        
    3.  Select the Automatic Approval Strategy
        
    4.  Click Subscribe
        
    
5.  The  _Subscription Overview_  page displays the Kiali Operator’s installation progress
    
6.  After about a minute, at the command line, view the new resource that represents the Jaeger Operator:
    
    ```
    $ oc get ClusterServiceVersion | grep kiali
    
    
    kiali-operator.v1.0.5                        Kiali Operator           1.0.5                            Installing
    ```
    
7.  View the status of the Kiali operator pod in the  _openshift-operators_  namespace:
    
    ```
    $ oc get pod  -n openshift-operators | grep "^kiali"
    
    kiali-operator-64c8487b6f-pp4k9   1/1     Running   0          1d2h
    ```
    
### 1.2. Set Up Service Mesh Operator Red Hat (Recomended)

1.  In the  _OperatorHub_  catalog of your OCP Web Console, type  **# Red Hat OpenShift Service Mesh**  into the filter box to locate the Elasticsearch Operator.
    
    ![system schema](img/operatorhub.png)
    
2.  Click the  _  Red Hat OpenShift Service Mesh provided by Red Hat_  to display information about the Operator.
    
3.  Click Install.
    
4.  Populate the entries of the  _Create Operator Subscription_  page as follows :
    
    1.  Select  _All namespaces_  on the cluster (default)
        
    2.  Select the  _stable Update Channel_
        
    3.  Select the Automatic Approval Strategy
        
    4.  Click Subscribe
        
    
5.  The  _Subscription Overview_  page displays the OpenShift Service Mesh Operator’s installation progress
    
6.  After about a minute, at the command line, view the new resource that represents the OpenShift Service Mesh:

```
 $ oc get ClusterServiceVersion
 
NAME                                         DISPLAY                          VERSION               REPLACES                     PHASE
elasticsearch-operator.4.2.15-202001171551   Elasticsearch Operator           4.2.15-202001171551                                Succeeded
jaeger-operator.v1.13.1                      Jaeger Operator                  1.13.1                                             Succeeded
kiali-operator.v1.0.9                        Kiali Operator                   1.0.9                 kiali-operator.v1.0.8        Succeeded
servicemeshoperator.v1.0.4                   Red Hat OpenShift Service Mesh   1.0.4                 servicemeshoperator.v1.0.3   Succeeded
```
 

### 1.2. Set Up Service Mesh Operator Maintra (optional)

Now that pre-req operators have been installed, the next step in installing the service mesh is to install the service mesh operator.

1.  Create an Istio operator namespace, then switch into the istio-operator project:
    
    oc adm new-project istio-operator --display-name="Service Mesh Operator"
    oc project istio-operator
    
2.  Create the Istio operator in the  `istio-operator`  project:
    
    oc apply -n istio-operator -f https://raw.githubusercontent.com/Maistra/istio-operator/maistra-1.0.0/deploy/servicemesh-operator.yaml
    ```
    Sample Output
    
     1: customresourcedefinition.apiextensions.k8s.io/controlplanes.istio.openshift.com created
     2: customresourcedefinition.apiextensions.k8s.io/servicemeshcontrolplanes.maistra.io created
     3: customresourcedefinition.apiextensions.k8s.io/servicemeshmemberrolls.maistra.io created
     4: clusterrole.rbac.authorization.k8s.io/istio-operator created
     5: serviceaccount/istio-operator created
     6: clusterrolebinding.rbac.authorization.k8s.io/istio-operator-account-istio-operator-cluster-role-binding created
     7: deployment.apps/istio-operator created
    
3.  Verify that the operator is running:
    ```
    oc get pod -n istio-operator
    
    Sample Output
    
    NAME                             READY   STATUS    RESTARTS   AGE
    istio-operator-6fc6c4466-rhkd7   1/1     Running   0          101s
    
4.  Verify that the operator launched successfully:
    ```
    oc logs -n istio-operator $(oc -n istio-operator get pods -l name=istio-operator --output=jsonpath={.items..metadata.name})
  
    Sample Output
    
     1: {"level":"info","ts":1560438471.4926245,"logger":"cmd","caller":"manager/main.go:38","msg":"Go Version: go1.11.6"}
     2: {"level":"info","ts":1560438471.4926615,"logger":"cmd","caller":"manager/main.go:39","msg":"Go OS/Arch: linux/amd64"}
     3: {"level":"info","ts":1560438471.4926672,"logger":"cmd","caller":"manager/main.go:40","msg":"operator-sdk Version: v0.2.1"}
     4: {"level":"info","ts":1560438471.4929588,"logger":"leader","caller":"leader/leader.go:55","msg":"Trying to become the leader."}
     5: {"level":"info","ts":1560438471.6304083,"logger":"leader","caller":"leader/leader.go:103","msg":"No pre-existing lock was found."}
     6: {"level":"info","ts":1560438471.6359944,"logger":"leader","caller":"leader/leader.go:127","msg":"Became the leader."}
     7: {"level":"info","ts":1560438471.6370773,"logger":"cmd","caller":"manager/main.go:89","msg":"Registering Components."}
     8:
     9: [...]
     10:
     11: {"level":"info","ts":1560438493.4802613,"logger":"cmd","caller":"manager/main.go:112","msg":"Starting the Cmd."}
     12: {"level":"info","ts":1560438493.580463,"logger":"kubebuilder.controller","caller":"controller/controller.go:134","msg":"Starting Controller","Controller":"servicemeshmemberroll-controller"}
     13: {"level":"info","ts":1560438493.5804665,"logger":"kubebuilder.controller","caller":"controller/controller.go:134","msg":"Starting Controller","Controller":"servicemeshcontrolplane-controller"}
     14: {"level":"info","ts":1560438493.5805254,"logger":"kubebuilder.controller","caller":"controller/controller.go:134","msg":"Starting Controller","Controller":"controlplane-controller"}
     15: {"level":"info","ts":1560438493.6806266,"logger":"kubebuilder.controller","caller":"controller/controller.go:153","msg":"Starting workers","Controller":"servicemeshcontrolplane-controller","WorkerCount":1}
     16: {"level":"info","ts":1560438493.6806233,"logger":"kubebuilder.controller","caller":"controller/controller.go:153","msg":"Starting workers","Controller":"servicemeshmemberroll-controller","WorkerCount":1}
     17: {"level":"info","ts":1560438493.6806598,"logger":"kubebuilder.controller","caller":"controller/controller.go:153","msg":"Starting workers","Controller":"controlplane-controller","WorkerCount":1}


### 1.3. Deploy Control Plane

Now that the Service Mesh Operator has been installed, you can now install a Service Mesh  _control plane_.

The Github repository at  [https://github.com/Maistra/istio-operator/tree/maistra-1.0.0/deploy/examples](https://github.com/Maistra/istio-operator/tree/maistra-1.0.0/deploy/examples)  includes a few custom resource examples that you can use to deploy this control plane.

#### 1.3.1. ServiceMeshControlPlane

The previously installed Service Mesh operator watches for a  _ServiceMeshControlPlane_  resource in all namespaces. Based on the configurations defined in that  _ServiceMeshControlPlane_, the operator creates the Service Mesh  _control plane_.

In this section of the lab, you define a  _ServiceMeshControlPlane_  and apply it to the  _istio-system_  namespace.

1.  Create a namespace called  _istio-system_  where the Service Mesh  _control plane_  will be installed.
    
    ```
    $ oc adm new-project istio-system --display-name="Service Mesh System"
    ```
    
2.  Create the custom resource file in your home directory:
    ```
    echo "apiVersion: maistra.io/v1
    kind: ServiceMeshControlPlane
    metadata:
     name: service-mesh-installation
    spec:
     threeScale:
     enabled: false
    
     istio:
     global:
     mtls: false
     disablePolicyChecks: false
     proxy:
     resources:
     requests:
     cpu: 100m
     memory: 128Mi
     limits:
     cpu: 500m
     memory: 128Mi
    
     gateways:
     istio-egressgateway:
     autoscaleEnabled: false
     istio-ingressgateway:
     autoscaleEnabled: false
     ior_enabled: false
    
     mixer:
     policy:
     autoscaleEnabled: false
    
     telemetry:
     autoscaleEnabled: false
     resources:
     requests:
     cpu: 100m
     memory: 1G
     limits:
     cpu: 500m
     memory: 4G
    
     pilot:
     autoscaleEnabled: false
     traceSampling: 100.0
    
     kiali:
     dashboard:
     user: admin
     passphrase: redhat
     tracing:
     enabled: true
    
    " > $HOME/service-mesh.yaml
    
3.  Note the following:
    
    -   Mutual TLS is disbled by setting  `mtls`  to false.
        
    -   Your Kiali username is set to  `admin`  and Kiali password to  `redhat`.
        
    -   You are setting the image prefix of the Istio images to  `registry.redhat.io/openshift-istio-tech-preview`. This means that you are using the Red Hat provided images rather than upstream images.
        
    
4.  Now create the service mesh  _control plane_  in the  `istio-system`  project:
    ```
    oc apply -f $HOME/service-mesh.yaml -n istio-system
    ```
    Sample Output
    ```
    servicemeshcontrolplane.maistra.io/service-mesh-installation created
    
5.  Watch the progress of the deployment:
    ```
    watch oc get pods -n istio-system
    ```
    -   It takes a minute or two before pods start appearing, and you may see some pods temporarily in  `Error`  and  `CrashLoopBackoff`  states that resolve themselves within a few seconds.
        
        NOTE
        
        The entire installation process can take approximately 10-15 minutes.
        
    
6.  Once the operator completes the installation successfully, confirm that you see the following pods all running successfully:
    ```
     1: NAME                                      READY   STATUS    RESTARTS   AGE
     2: grafana-86dc5978b8-m7wqf                  1/1     Running   0          80s
     3: istio-citadel-6656fc5b9b-dc8dr            1/1     Running   0          6m38s
     4: istio-egressgateway-66c8cdd978-qgkmr      1/1     Running   0          2m42s
     5: istio-galley-69d8bbb7c5-fx84w             1/1     Running   0          6m16s
     6: istio-ingressgateway-844848f59f-gklxr     1/1     Running   0          2m42s
     7: istio-pilot-798976867d-hc9mr              2/2     Running   0          3m44s
     8: istio-policy-54556f8b9c-drn66             2/2     Running   3          4m52s
     9: istio-sidecar-injector-694c49c4b7-8r28t   1/1     Running   0          111s
     10: istio-telemetry-8949d7ffd-95kzt           2/2     Running   3          4m52s
     11: jaeger-65f55f7bc6-7mcdx                   1/1     Running   0          8m17s
     12: kiali-d566b556c-l77lf                     1/1     Running   0          57s
     13: prometheus-5cb5d7549b-nvxv5               1/1     Running   0          9m42s
    
7.  Press  **Ctrl+C**  to exit the  _watch_.
    
8.  Examine the created routes in the  `istio-system`  project:
    
    ```
    oc get routes -n istio-system
    ```
    
    Sample Output
    ```
     1: NAME                   HOST/PORT                                                                         PATH   SERVICES               PORT    TERMINATION   WILDCARD
     2: grafana                grafana-istio-system.apps.cluster-<GUID>>.<GUID>>.<SANDBOX>.opentlc.com                  grafana                <all>                 None
     3: istio-ingressgateway   istio-ingressgateway-istio-system.apps.cluster-<GUID>>.<GUID>>.<SANDBOX>.opentlc.com     istio-ingressgateway   80                    None
     4: jaeger                 jaeger-query-istio-system.apps.cluster-<GUID>>.<GUID>>.<SANDBOX>.opentlc.com             jaeger-query           <all>   edge          None
     5: kiali                  kiali-istio-system.apps.cluster-<GUID>>.<GUID>>.<SANDBOX>.opentlc.com                    kiali                  20001   reencrypt     None
     6: prometheus             prometheus-istio-system.apps.cluster-<GUID>>.<GUID>>.<SANDBOX>.opentlc.com               prometheus             <all>                 None
    ```
    -   Expect to see routes for  `grafana`,  `prometheus`, and  `kiali`  among others.
        
    
9.  Enter this command to get the URL of the Kiali web console
    ```
    oc get route kiali -n istio-system -o jsonpath='{"https://"}{.spec.host}{"\n"}'
    ```
    Sample Output
    
    +
https://kiali-istio-system.apps.cluster-<GUID>.<GUID>.<SANDBOX>.opentlc.com

1.  Start a web browser on your computer and vist the Kiali URL.
    
2.  At the login screen, enter the credentials:
    
    1.  Username: admin
        
    2.  Password: r3dh4t1!
        
        -   Expect to see the Kiali user interface, which you use later in this course.




##  2. OpenBanking Setup

Clone git repo 
```
$ git clone [https://github.com/ChrystianDuarte/blackjack.git](https://github.com/ChrystianDuarte/blackjack.git#master)  
$ cd blackjack/scripts/obp
```

Deploy OBP Data Base

```
$ oc new-project obp-data
```
```
$ oc new-app --template=postgresql-persistent --param=NAMESPACE=openshift --param=DATABASE_SERVICE_NAME=postgresql --param=POSTGRESQL_USER=obpuser --param=POSTGRESQL_PASSWORD=obppassword --param=POSTGRESQL_DATABASE=obpuser --param=POSTGRESQL_VERSION=9.6
```
  
Deploy OBP API
```
$ oc new-project obp-api
```
```
$ oc create -f obp-template-demojam
```
```
$ oc process obp-api-example | oc create -f -
```
  

Restore data from Dump file

1. Port forward the Portgres Pod:

```
$ oc project obp-data
```
```
$ oc get pods

postgresql-1-pdsvc
```
```
$ oc port-forward postgresql-1-pdsvc 5432:5432
```
  
2. Do the data restore:
 - [1]  Open DBeaver desktop client
 - [2] Connect to DB (localhost:5432/obpuser) (Postgres 9.6 Driver)
 - [3 ] Right clic over DB > Tools > Restore
 - [4] Select dump file and restore

More info: [https://www.youtube.com/watch?v=S5Zx8Lf9-Aw](https://www.youtube.com/watch?v=S5Zx8Lf9-Aw)  

 
Deploy Blackjack APIs

```
$ oc new-project blackjack
```
```
$ oc apply -f https://raw.githubusercontent.com/jboss-fuse/application-templates/2.1.x.redhat-7-4-x/fis-image-streams.json -n openshift

  

$ oc new-app fuse7-java-openshift:1.4~https://github.com/ChrystianDuarte/blackjack.git#master --context-dir=blackjack-status --name=blackjack-status

$ oc new-app fuse7-java-openshift:1.4~https://github.com/ChrystianDuarte/blackjack.git#master --context-dir=blackjack-users --name=blackjack-users

$ oc new-app fuse7-java-openshift:1.4~https://github.com/ChrystianDuarte/blackjack.git#master --context-dir=blackjack-payment --name=blackjack-payment

$ oc new-app fuse7-java-openshift:1.4~https://github.com/ChrystianDuarte/blackjack.git#master --context-dir=blackjack-ranking --name=blackjack-ranking
```
 
Change port and targetPort from all services from 8778(8778) to 8080(8080)

(Do it manually)

```
Expose services

# oc expose svc/blackjack-users

# oc expose svc/blackjack-payment

# oc expose svc/blackjack-ranking

# oc expose svc/blackjack-status
```
  

Deploy Blackjack Frontend


Fork [https://github.com/ChrystianDuarte/blackjack.git](https://github.com/tgubeli/blackjack.git) (you will need an GitHub account)

Edit blackjack.js and dashboard.js (.../edit/master/public/blackjack.js and .../edit/master/public/dashboard.js)

Find and change "GUID" variable value to your RHPDS GUID.

Example: var guid = 'demojam-8b2e';

Commit and push
```
$ oc new-app nodejs:10~https://github.com/ChrystianDuarte/blackjack.git#master --context-dir=blackjack-frontend --name=blackjack-frontend

Example: oc new-app nodejs:10~https://github.com/ChrystianDuarte/blackjack.git#master --context-dir=blackjack-frontend --name=blackjack-frontend
```
```
$ oc expose svc/blackjack-frontend
```

Enjoy!

Go to your browser:

Game: [http://blackjack-frontend-blackjack.apps.cluster-<"GUID">.<"GUID">.example.opentlc.com/blackjack.html](http://blackjack-frontend-blackjack.apps.cluster-santiago-8980.santiago-8980.example.opentlc.com) (enter a redhat email)

Top Ten users: [http://blackjack-frontend-blackjack.apps.cluster-<"GUID">.<"GUID">.example.opentlc.com/dashboard.html](http://blackjack-frontend-blackjack.apps.cluster-santiago-8980.santiago-8980.example.opentlc.com/dashboard,html)


## Apply Service Mesh configurations


### Go to osm

```bash
$ cd osm
```

### Pre Req

(oc - OpenShift Command Line Interface)[https://mirror.openshift.com/pub/openshift-v4/clients/oc/latest/]
(ansible 2.9) [https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html]
Openshift Container Platform 4.2 with Openshift Service Mesh installed -> follow the instrucctions on the fundations (course)[https://learning.redhat.com/mod/scorm/view.php?id=5623]
Install Python dependencies

```bash
#Update ansible
pip install ansible==2.9.2
# install package
pip install requests-oauthlib --user
pip install openshift --user
```

### Demo Application

In order to test Openshift Service Mesh we are going to use the bookinfo app from the istio repository.
To deploy the application follow the following instructions:

```bash
$ oc new-project bookinfo

$ oc apply -f https://raw.githubusercontent.com/istio/istio/1.4.0/samples/bookinfo/platform/kube/bookinfo.yaml -n bookinfo

$ oc expose service productpage
```

### Apply Service Mesh for Namespace Blackjack

The goal of the homework is to enable service mesh capabilities on the bookinfo namespace.
The features that need to be enable are:

* The book info namespace should be on the scope of Openshift Service Mesh
* All the deployments should have an envoy proxy
* The trafic between the services should enable mTLS

In order to acomplish previus requirements and this homework principal goal, I have create an ansible playbook that automate all the task requested.


### Ansible Directory Structure

```bash
[francisco@fmeneses homework]$ tree .
.
├── osm
│   ├── group_vars
│   │   └── all
│   ├── roles
│   │   └── ServiceMeshMemberRoll
│   │       ├── files
│   │       ├── meta
│   │       │   └── main.yml
│   │       ├── tasks
│   │       │   └── main.yml
│   │       └── templates
│   │           ├── cert.cfg
│   │           ├── destinationrule.yml
│   │           ├── policy.yml
│   │           ├── smmr.yml
│   │           ├── virtualservice.yml
│   │           └── wildcard-gateway.yml
│   └── site.yml
```

* group_vars/all: Global playbook variables

* roles/ServiceMeshMemberRoll/files: Will contain tls.crt and tls.key files

* roles/ServiceMeshMemberRoll/meta/main.yml: Metadata file (author,company, etc)

* roles/ServiceMeshMemberRoll/tasks/main.yml: File that has all the tasks.

* roles/ServiceMeshMemberRoll/templates/*: Template files used by the task file

* site.yml: Main playbook file


### Variables setup

"All" file contains all the variables used by the playbook

```yaml
# API URL example of Openshift 4 Instance
openshift_api_url: "https://api.cluster-58fe.58fe.sandbox302.opentlc.com:6443"
# example Application domain
openshift_apps_domain: "apps.cluster-58fe.58fe.sandbox302.opentlc.com"
# Openshift User
openshift_username: axxxxx
# Openshift Password
openshift_password: "redhat password"
# Project used to deploy Service Mesh Control Plane
control_plane_project_name: "istio-system"
# Namespace that will be included into Service Mesh Scope
service_mesh_member_roll_namespaces: " bookinfo"
```
### Create files directory

Create a directory in 
```bash
$ mkdir files /osm/roles/ServiceMeshMemberRoll/
```
### Playbook execution

To execute the playbook use the following command

```bash
$ ansible-playbook site.yml
```

## Optional: Enable Autoscalling

In order to add autoscalling capabilities to a POD, for example a blackjack POD (payment or user services), we need to autoscale obp-api POD too and add some parameter to Postgres DB (obp-data).

  
1. Add Readiness Probe & Resource Limits:

-   obp-api: /obp/v4.0.0/rate-limiting, port: 8080, initial delay: 98, timeout: 5
    
-   obp-api: cpu-limit: 1core, min-mem:700mb, mem-limit: 2Gi
    
-   blackjack-payment: /blackjack/api, port: 8080, initial delay: 50, timeout: 4
    
-   blackjack-payment: cpu-limit: 700mi, mem-limit: 2Gi
    
```
$ oc project obp-api
```
```
$ oc set probe dc/obp-deployment --readiness --get-url=http://:8080/obp/v4.0.0/rate-limiting --initial-delay-seconds=98 --timeout-seconds=5
```

```
$ oc set resources dc/obp-deployment --limits=memory=2Gi,cpu=1 --requests=memory=700Mi
```
  
```
$ oc project blackjack
```
```
$ oc set probe dc/blackjack-payment --readiness --get-url=http://:8080/blackjack/api --initial-delay-seconds=50 --timeout-seconds=4
```
```
$ oc set resources dc/blackjack-payment --limits=memory=2Gi,cpu=1
```
  

2. Change obi-api pod replicas from 1 to 2
```
$ oc scale dc obp-deployment --replicas=2 -n obi-api
```
  
Add autoscaling to blackjack-payment dc from 2 pods to 10
```
$ oc autoscale dc/blackjack-payment --min 2 --max 10 --cpu-percent=80 -n blackjack
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NjUyMDUyMzgsMTMyNTU0NDgxNSwyND
Q0ODI2MzgsLTE4NTMyNjYwNjcsMjA1MTg2NzM0NywtMTg2NjU0
Nzc4MSwxOTYzMzkzODY4LDMyMzc2NjUwLC0xNTcwNjM5MjUwLD
ExMjI2MzIwNDIsMTA5Mzk1ODAsLTE5MDIyMTIxNzgsLTEzNDY3
MzM3MjQsLTE5MDc4OTU5OTUsMTAwMzg2Mjk4NSwtMzQyNjg2NT
AxLDE3Mzg5MDA4MjksMjA4MDIzOTAyMSwtMTkzNTQwODc1NSwx
MTQwNzkzODU3XX0=
-->