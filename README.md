# kubernetes-example


## Kargo
Kargo walkthrough example:

Create file kargoconf/kargo.conf:
```
# Common options
# ---------------
# Path where the kargo ansible playbooks will be installed
# Defaults to current user's home directory if not set
# kargo_path: "/tmp"
# Default inventory path
kargo_git_repo: "https://github.com/kubespray/kargo.git"
# Logging options
loglevel: "info"

# OpenStack options
# ---
os_auth_url: "http://nebula.ncsa.illinois.edu:5000/v2.0"
os_username: ""
os_password: ""
os_project_name: ""
masters_flavor: "m1.medium"
nodes_flavor: "m1.medium"
etcds_flavor: "m1.medium"
image: "CentOS7"
network: "yournetwork"
sshkey: "yourkey"
```


Start the kargo-cli container:
```
docker run -it -v `pwd`/kargoconf:/etc/kargo quay.io/smana/k8s-kargocli:latest /bin/bash
```

Install shade:
```
# Required to get shade library
easy_install -U pip
pip install "ansible==2.2.1.0" --force-reinstall
pip install "setuptools==11.3"
pip install shade
apt-get update -y 
apt-get install -y vim
```

Provision a 3-node cluster in openstack:
```
kargo openstack --nodes 3 
```

Deploy Kubernetes cluster with flannel network:
```
kargo deploy -k your.pem -u centos -n flannel
```

Add a public IP address to one of your instances via openstack CLI or horizon


### Kubernetes example

SSH into your cluster -- use the first created instance.

Label the node with the public IP:
```
kubectl get nodes
kubectl  label node <node name> my-node-role=publicip
```

Create the nginx ingress controller:
```
kubectl create -f yaml/ingress.yaml
```

Wait for it to be ready:
```
kubectl get pods
```

Create your nginx toy service and replication controller:
```
kubectl create -f yaml/nginx.yaml
```

