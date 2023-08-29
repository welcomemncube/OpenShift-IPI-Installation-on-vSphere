# OpenShift IPI Installation on vSphere
## Objectives  

1. What is Openshift? 
2. Prerequistes  
3. Requirements
4. Setting up the environment on Bastion Host
5. Minimum Viable Architecture 

 ## 1. What is Openshift?
Containerized platform developed by Red Hat, a subsidiary of IBM.It simplies the process of deploying, managing and scaling contairized applications.

## 2. Prerequistes
* oc - For managing the cluster resources 
* Podman - For managing your containers local 
* openshift-install - For perfoming Openshift installation 
* Pull secret - For authenticating with image repositories
* ssh-key - For accessing nodes
* RedHat Subscription

## 3. Requirements
### Hardware requirements 

  |  Name  | Count | OS | IP Address  | CPU | Memory | Storage |
  | ---    | ---   | --- | ---        | --- | ---    | ---     |
  | Bastion host    | 1   | Linux | ip address      | 4 CPUs  | 32 GB    | 300 GB      |
  |  Master-0    | 1   | RHCOS | ip address        | 4 CPUs | 16 GB    | 120 GB     |
  |  Master-1    | 1   | RHCOS | ip address        | 4 CPUs | 16 GB    | 120 GB     |
  |  Master-2    | 1   | RHCOS | ip address        | 4 CPUs | 16 GB    | 120 GB     |
  | Worker-0    | 1   | RHCOS | ip address        | 8 CPUs | 32 GB    | 120 GB     |
  | Worker-1    | 1   | RHCOS | ip address        | 8 CPUs | 32 GB    | 120 GB     |

### Network requirements 

  |  Usage      |   Value    |
  | ---    | ---   |
  |     NTP   |   x.x.x.x   |
  |    DHCP    |  x.x.x.x - x.x.x.x     |
  |     Port   |   80, 443, 6443   |
  |     API   |   x.x.x.x (Whitelisted)  |
  |     Ingress   |   x.x.x.x (Whitelisted)  |
  |     clustername   |   your cluster name  |
  |     base domain   |   example.com  |
  |     DNS   |   api.cp4i-cluster.example.com;  *.apps.cp4i-cluster.example.com |
  
  

### VSphere Requirements 
* Need account with global administrative privileges.
  |  Usage      |   Value    |
  | ---    | ---   |
  |   vSphere     |     |
  |   Datastore     |     |
  |   vCenter     |     |
  |   vCenter username     |     |
  |   vCenter password     |     |
  |   datacenter     |     |
  |   vSphere folder     |     |
  |   vSphere ResourcePool     |     |
  

## 4. Setting up the environment on the Bastion Host
### Ensure CA certificates from vCenter are trusted 

` curl -kL https://vcenter-domain/certs/download.zip -o download.zip `  

` unzip download.zip `

` sudo cp certs/lin/* /etc/pki/ca-trust/source/anchors ` 

` sudo update-ca-trust extract `

### Extract oc and openshift-install

` tar –zxvf https://mirror.openshift.com/pub/openshift-v4/clients/ocp/<ocp-version>/openshift-client-linux.tar.gz `

` tar –zxvf https://mirror.openshift.com/pub/openshift-v4/clients/ocp/<ocp-version>/openshift-install-linux.tar.gz `

` sudo mv oc openshift-install /usr/local/bin `

` oc version `
 
` openshift-install version ` 


### Install Podman 

` sudo yum install podman –y `

### Create a directory for your Installation: 

` mkdir ocpinstall `

` cd ocpinstall ` 

### Generate a key pair for cluster node SSH access 

` ssh-keygen -t ed25519 -N '' -f ~/.ssh/id_core `

### Create the Install Config file (install-config.yaml-> yaml for defining installation parameters) 
` openshift-install create install-config `

### Copy install-config.yaml to the ocpinstall directory 

` sudo cp install-config.yaml ocpinstall `

### Make a backup of install-config.yaml 

` cp install-config.yaml install-config.yaml.bak `


### Create the cluster 

` openshift-install create cluster --dir ocpinstall --log-level debug `

### Accessing the cluster via CLI
` oc login -u kubeadmin -p kubeadmin-password https://api.ocpinstall.basedomain:6443 `

## 5. Minimum Viable Architecture
<img width="1079" alt="Drawing2 (2)" src="https://github.com/welcomemncube/OpenShift-IPI-Installation-on-vSphere/assets/39636648/f069b75d-1a63-40b3-a0f7-bf04187e95c6">


