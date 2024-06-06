# Cluster Architecture, Installation & Configuration

Docs: https://github.com/David-VTUK/CKA-StudyGuide/blob/master/RevisionTopics/01-Cluster%20Architcture%2C%20Installation%20and%20Configuration.md

## Manage role based access control (RBAC)
## Use Kubeadm to install a basic cluster
## Manage a highly-available Kubernetes cluster
## Provision underlying infrastructure to deploy a Kubernetes cluster
## Perform a version upgrade on a Kubernetes cluster using Kubeadm
## Implement etcd backup and restore


# Excerise 0 - setup

**Design and install a Kubernetes cluster**

First identify the requirements of the cluster:

Purpose
• Education
• Development & Testing
• Hosting Production Applications

Cloud or OnPrem?

Workloads
• How many?
• What kind?
    • Web
    • Big Data/Analytics

Application Resource Requirements
    • CPU Intensive
    • Memory Intensive

Traffic
    • Heavy traffic
    • Burst Traffic

# Exercise 1 - Use Kubeadm to install a basic cluster

Prerequisites:
- prepare cluster nodes
    - Prepare two vanilla VM's (No Kubernetes components installed) with the kubeadm binary installed that will be cluster nodes
    - install container runtime (e.g. [containerd](https://github.com/containerd/containerd/blob/main/docs/getting-started.md))
    - install kubeadm on all the nodes

- Use kubeadmin to install master server
- setup pod network on all the nodes (flannel)
- On second node, install kubeadm and join it to the cluster as a worker node

I will use a ready made environment for this exercise, as I don't have the resources to create a multi-node cluster.
[Kodekloud lab](https://uklabs.kodekloud.com/topic/practice-test-cluster-installation-using-kubeadm/)

<details><summary>Answer</summary>

## Node 1:

Prep kubeadm (as mentioned above, I doubt we will need to do this part in the exam)
Install kubeadm and stand up the control plane, using 10.244.0.0/16 as the pod network CIDR, and https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml as the CNI


```shell
# Install a container runtime, IE https://github.com/containerd/containerd/blob/main/docs/getting-started.md
apt-get update && apt-get install -y apt-transport-https curl
curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

Turn this node into a master

```shell
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
...
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
...
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
...
Note the join command, ie:
kubeadm join 172.16.10.210:6443 --token 9tjntl.10plpxqy85g8a0ui \
    --discovery-token-ca-cert-hash sha256:381165c9a9f19a123bd0fee36fe36d15e918062dcc94711ff5b286ee1f86b92b 
```
## Node 2

Run the join command taken from the previous step

```shell
kubeadm join 172.16.10.210:6443 --token 9tjntl.10plpxqy85g8a0ui \
    --discovery-token-ca-cert-hash sha256:381165c9a9f19a123bd0fee36fe36d15e918062dcc94711ff5b286ee1f86b92b 
```

Validate by running `kubectl get no` on the master node:

```shell
kubectl get no
NAME      STATUS   ROLES                  AGE     VERSION
ubuntu    Ready    control-plane,master   9m53s   v1.26.0
ubuntu2   Ready    <none>                 50s     v1.26.0
```
</details>
