# blackjack and OpenBanking
Black Jack Front End Demo for Open Banking at Scale Demo



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



## Download sources

```bash
# Clone Sources
git clone https://github.com/fmenesesg/osm-homework.git

# Go to homework-rhoar
cd osm-homework
```

## Pre Req

(oc - OpenShift Command Line Interface)[https://mirror.openshift.com/pub/openshift-v4/clients/oc/latest/]
(ansible 2.9) [https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html]
Openshift Container Platform 4.2 with Openshift Service Mesh installed -> follow the instrucctions on the fundations (course)[https://learning.redhat.com/mod/scorm/view.php?id=5623]
Install Python dependencies

```bash
pip install requests-oauthlib --user
pip install openshift --user
```

## Demo Application

In order to test Openshift Service Mesh we are going to use the bookinfo app from the istio repository.
To deploy the application follow the following instructions:

```bash
oc new-project bookinfo

oc apply -f https://raw.githubusercontent.com/istio/istio/1.4.0/samples/bookinfo/platform/kube/bookinfo.yaml -n bookinfo

oc expose service productpage
```

## Apply Service Mesh configurations

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
├── osm-homework
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
└── README.md

```

* group_vars/all: Global playbook variables

* roles/ServiceMeshMemberRoll/files: Will contain tls.crt and tls.key files

* roles/ServiceMeshMemberRoll/meta/main.yml: Metadata file (author,company, etc)

* roles/ServiceMeshMemberRoll/tasks/main.yml: File that has all the tasks.

* roles/ServiceMeshMemberRoll/templates/*: Template files used by the task file

* site.yml: Main playbook file


## Variables setup

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

## Playbook execution

To execute the playbook use the following command

```bash
ansible-playbook site.yml
```

##  2. OpenBanking Setup
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTU4Mjk2MzE4OSwtODUzNjg2NDUzXX0=
-->