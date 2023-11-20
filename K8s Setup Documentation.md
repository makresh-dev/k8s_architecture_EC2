# Kubernetes Setup Documentation

## Prerequisites

Before proceeding to installation, make sure you have updated the OS and installed the certificates:

```
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

## K8s Architecture
![](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)
From above image we can see that we need a Control Panel node (Master node) to control nodes 1 & 2 (Worker Node 1 & Worker Node 2). For that we need to install docker, kubeadm, kubectl and kubelet on all nodes.

We will create one master node and two worker nodes.


## Install docker on Master and on Worker nodes:
```
sudo apt install docker.io
```

## Add the GCP keys and repository of Kubernetes on Master and Worker nodes:
```bash
# Adding GPG keys.
curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg

# Add the repository to the sourcelist.
echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# After adding repos run this again
sudo apt update
```

## Install kubeadm, kubectl and kubelet on Master and Worker nodes:
```bash
sudo apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
```

## Initialize the Kubernetes on Master Node only by following command:
```bash
sudo kubeadm init
```
you will get output like this on master node if have correctly followed every step:
>Your Kubernetes control-plane has initialized successfully!
>
>To start using your cluster, you need to run the following as a regular user:
>
>mkdir -p $HOME/-kube
>
>sudo cp -i /etc/kubernetes/admin.conf $HOME/-kube/config sudo chown $(id -u):$(id -g) $HOME/-kube/config
>
>lternatively, if you are the root user, you can run:
>
>export KUBECONFIG=/etc/kubernetes/admin.conf
>
>You should now deploy a pod network to the cluster.
>
>Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at: https://kubernetes.io/docs/concepts/cluster->administration/addons/
>
>Then you can join any number of worker nodes by running the following on each as root:
>
>kubeadm join 172.31-39-226:6443 --token xobjop-odegonyuvr?piper --discovery-token-ca-cert-hash
>
>Set up local kubeconfig (both for root user and normal user):
>
>sha25b:d8aa75e7f8b953c42e10eb5b4bflb8c34f52b5c312da33577d4fbba772f202e2
>


## Set up local kubeconfig (both for root user and normal user):
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Apply Weave network on Master node:
```bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```
Expose port 6443 in the Security group for the Worker to connect to Master Node


## Run this command on worker nodes only to reset kubeadm:
```
sudo kubeadm reset pre-flight checks
```

## Paste the join command you got from the master node and append --v=5 at the end. Make sure either you are working as sudo user or use sudo before the command:
```bash
kubeadm join 172.31.40.6:6443 --token wdmf89.2yg038oqdmy6p88u \--discovery-token-ca-cert-hash sha256:9fdbaeedc8ee5c3991b71ae05896ed0f93d6386e7052cf2acc6fa7d6728d90f3 --v=5
```

## After successful join→ you will get this message on worker nodes:
>This node has joined the cluster:
>* Certificate signing request was sent to apiserver and a response was received. The Kubelet was informed of the new secure connection details.
>Run 'kubectl get nodes' on the control-plane to see this node join the cluster.


