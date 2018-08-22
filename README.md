# cilium-workshop
Welcome to the Cilium Workshop!

We'll explore some Cilium Network Policies and tools to help you better understand Cilium. This workshop will demonstrate Cilium's capabilities in a Kubernetes environment. We will assume minikube v0.28.0 with k8s v1.10 and Cilium v1.2 but if you're using a different version, you can retrieve other k8s and Cilium versions by following our [Getting Started Guides](https://cilium.readthedocs.io/en/v1.2/gettingstarted/minikube/).

## Prerequisites

1. Install a hypervisor [virtualbox](https://www.virtualbox.org/wiki/Downloads)

   If you are on Linux, make sure Intel Virtualization Technology (Intel VT) is enabled on BIOS.

2. Install [kubectl](https://kubernetes.io/docs/tasks/kubectl/install/)

3. Install [minikube](https://github.com/kubernetes/minikube/releases) (v0.28.0 recommended).

       % minikube version
       minikube version: v0.28.0

4. (Optional) Join our Slack channel to helping and collaborating with other Cilium users: [Cilium Slack](cilium.io/slack) #cilium-workshop

5. (Optional) Clone this repo to have quick access to the commands and scripts that you will use.

## Deploy minikube and set up Cilium

1. First, deploy minikube with the CNI option enabled:

         minikube start --network-plugin=cni --extra-config=kubelet.network-plugin=cni --memory=5120

2. Cilium requires a KV-store. For the purpose of this demo, launch etcd as its dependency:


         kubectl create -n kube-system -f https://raw.githubusercontent.com/cilium/cilium/v1.2/examples/kubernetes/addons/etcd/standalone-etcd.yaml

3. Finally deploy the Cilium DaemonSet on minikube:

      
         kubectl create -f https://raw.githubusercontent.com/cilium/cilium/v1.2/examples/kubernetes/1.10/cilium.yaml


Confirm that you're up and running:

         kubectl get pods --all-namespaces
