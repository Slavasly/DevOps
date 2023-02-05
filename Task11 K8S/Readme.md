## Setup Kubernetes

**Prepare 2 VMs (kubemaster and kubenode) with the same parametrs**

- Requirements:

 - 4 CPU
 
 - 8 GB RAM
 
 - Ubuntu 20.04 x86x64
 
 - Change disk size to 20Gb

**Run commands in two VMs (kubemaster and kubenode)**

>ssh to both instances and run command below.

**Commands:**
 
`sudo apt update`

`sudo apt upgrade -y`


>FYI:
>apt-get update downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.

>apt-get upgrade will fetch new versions of packages existing on the machine if APT knows about these new versions by way of apt-get update.

**Edit the hosts file with the command:**

`sudo nano /etc/hosts`

**Put your private IP address and hostname**

![image](https://user-images.githubusercontent.com/44306982/216848168-eed92b3f-f92c-45e4-9698-df6539030183.png)

**Save and exit**

**Install the first dependencies with:**

`sudo apt install curl apt-transport-https -y`

**Next, add the necessary GPG key with the command:**

`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`

**Add the Kubernetes repository with:**

`echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list`

**Update apt:**

`sudo apt update`

![image](https://user-images.githubusercontent.com/44306982/215218807-09423d8e-266f-468f-82ad-8f6006eb3cef.png)

**Install the required software with the command:**

`sudo apt -y install vim git curl wget kubelet kubeadm kubectl`

**Place kubelet, kubeadm, and kubectl on hold with:**

`sudo apt-mark hold kubelet kubeadm kubectl`

**Start and enable the kubelet service with:**

`sudo systemctl enable --now kubelet`

>Next, we need to disable swap on both kubemaster. 

**Open the fstab file for editing with:**

`sudo nano /etc/fstab`

**Save and close the file.**

>You can either reboot to disable swap or simply issue the following command to finish up the job:

`sudo swapoff -a`

![image](https://user-images.githubusercontent.com/44306982/215214697-6efd5fa0-3ba0-46ed-977a-0358c23f292b.png)

**Enable Kernel Modules and Change Settings in sysctl:**

`sudo modprobe overlay`
`sudo modprobe br_netfilter`

**Change the sysctl settings by opening the necessary file with the command:**

`sudo nano /etc/sysctl.d/kubernetes.conf`

![image](https://user-images.githubusercontent.com/44306982/215215173-ae997d45-433c-4cba-91b4-f58d3e202a6e.png)

**Look for the following lines and make sure they are set as you see below:**
```
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
```
**Save and close the file. Reload sysctl with:**

`sudo sysctl --system`

![image](https://user-images.githubusercontent.com/44306982/215218552-1da93736-3984-43e2-b737-f30cd6aebcc1.png)
---
## Install containerd

**Install the necessary dependencies with:**

`sudo apt install curl gnupg2 software-properties-common apt-transport-https ca-certificates -y`

**Add the GPG key with:**

`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

**Add the required repository with:**

`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`

![image](https://user-images.githubusercontent.com/44306982/215218253-4f6fb004-9330-4ceb-93e5-6e5088674097.png)

**Install containerd with the commands:**
```
sudo apt update
sudo apt install containerd.io -y
```
![image](https://user-images.githubusercontent.com/44306982/215218134-1f7b99f5-5b95-45d3-9f41-9c7b59d58136.png)

**Change to the root user with:**

`sudo su`

**Create a new directory for containerd with:**

`mkdir -p /etc/containerd`

**Generate the configuration file with:**

`containerd config default>/etc/containerd/config.toml`

**Exit from the root user with:**

`exit`

**Restart containerd with the command:**

`sudo systemctl restart containerd`

**Enable containerd to run at startup with:**

`sudo systemctl enable containerd`

## Initialize the Master Node

**Pull down the necessary container images with:**

`sudo kubeadm config images pull`

## Command only for kubemaster :

**Now, using the kubemaster IP address initialize the master node with:**

`sudo kubeadm init --pod-network-cidr=192.168.0.0/16`

**Result:**

![image](https://user-images.githubusercontent.com/44306982/215216745-02787324-eecd-4f9b-a57c-0df32eb0b2ef.png)

>Finally, you need to create a new directory to house a configuration file and give it the proper permissions which is done with the following commands:
```
mkdir -p $HOME/.kube
sudo cp -f /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
**List Kubernetes Nodes:**

`kubectl get nodes`

![image](https://user-images.githubusercontent.com/44306982/215216245-ac5dc440-6a31-4b37-a1e3-18980299366a.png)

## Command only for kubenode :

**Connect kubenode to kubemaster**

`sudo su`
```
kubeadm join 10.132.0.9:6443 --token ut36yh.qd0aeqwaciay05l6         
--discovery-token-ca-cert-hash sha256:11111111111111111111111111111111111111111111111111111111111111
```
>(copy from kubemaster output)

![image](https://user-images.githubusercontent.com/44306982/215216570-062996d4-0d42-4e3d-b1bc-41a3cb98339c.png)


## Comeback to kubemaster :

**Install network:**
```
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/tigera-operator.yaml

curl https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/custom-resources.yaml -O

kubectl create -f custom-resources.yaml
```
>Wait when all pods will be ready:

`kubectl get pods --all-namespaces -w`

![image](https://user-images.githubusercontent.com/44306982/215217440-335a1508-fbb7-4962-91c3-f9137a584a0f.png)

**Result:**

`kubectl get nodes -o wide`

![image](https://user-images.githubusercontent.com/44306982/215212512-ab6c6575-5585-4fdb-9823-e7c49407b6bb.png)

## Successfully deployed Kubernetes
