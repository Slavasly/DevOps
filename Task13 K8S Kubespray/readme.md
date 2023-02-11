# Kubernetes Task K8S KubeSpray

**For deploying Kubernetes we used Kubespray. We need a new created instance, use ingress and certificate.**

Content:<br>
[Сreate Instance](#1Сreate-Instance) | [Setup Kubespray](#setup-kubespray) | [Git clone](#git-clone) | [Ingress-controller](#ingress-controller) | [Create domain name](#create-domain-name) | [Deployment and Ingress](#deployment-and-ingress) | [Certificate](#certificate) | [Result](#result)

# Pre-requisites:
Clear new-created instance for installing kubernetes through the Kubespray.

### <a name="1Сreate-Instance">1.Сreate-Instance</a>
Requirements:\
4 CPU\
8 GB RAM\
Ubuntu 20.04 x86x64\
Change disk size to 35Gb\

Configure VM:<br>
![image](https://user-images.githubusercontent.com/44306982/217662899-13e14a58-2235-41c9-957f-4b16d67759b9.png)\
![image](https://user-images.githubusercontent.com/44306982/217663192-f2392c76-b2d3-46b6-b21f-f93f9ccd448e.png)

Add your SSH public key:<br>
![image](https://user-images.githubusercontent.com/44306982/217662445-b962e6b5-45c5-48a0-855d-fc0919486498.png)

SSH to new created VM Instance from local machine:

![image](https://user-images.githubusercontent.com/44306982/217663717-c3138799-d4a4-4f34-abac-f00da63fe485.png)

### <a name="setup-kubespray">2.Setup Kubespray</a>
<b>All commands are executed on the local machine</b>

Clone Kubespray release  repository:
```
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray
git checkout release-2.20
```
![image](https://user-images.githubusercontent.com/44306982/217664981-f0e6c275-6fe5-4bf5-99ff-578b66fffd2a.png)

Copy and edit inventory file:
```
cp -rfp inventory/sample inventory/mycluster
```
![image](https://user-images.githubusercontent.com/44306982/217665257-c13092ad-a2f6-4e67-a19b-7df70b750a9d.png)

```
nano inventory/mycluster/inventory.ini
```
![image](https://user-images.githubusercontent.com/44306982/218258723-538ec716-14b6-45cb-b314-414104354eb8.png)
```
[all]
node1 ansible_host=35.187.38.64 #VM Public IP

[kube_control_plane]
node1

[etcd]
node1

[kube_node]
node1

[calico_rr]

[k8s_cluster:children]
kube_control_plane
kube_node
calico_rr
```
![image](https://user-images.githubusercontent.com/44306982/218258664-4d491271-76c0-47ec-a203-18be6a44426b.png)

Turn on MetalLB:
```
nano inventory/mycluster/group_vars/k8s_cluster/addons.yml
```
OR
```
vim inventory/mycluster/group_vars/k8s_cluster/addons.yml
```
```
metallb_enabled: true
metallb_speaker_enabled: true
metallb_avoid_buggy_ips: true
metallb_ip_range:
  - "VM_private_ip/32"
```
```
nano inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
```
OR
```
vim inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
```
```
kube_proxy_strict_arp: true
```
Run execute container (<b>if you don't have [Docker installed](https://docs.docker.com/engine/install/ubuntu/), you should install it</b>):
```
sudo docker run --rm -it -v ~/kubespray:/mnt/kubespray -v ~/.ssh:/pem quay.io/kubespray/kubespray:v2.20.0 bash
```
Go to kubespray folder and start ansible-playbook (command in container):
```
cd /mnt/kubespray
```
```
ansible-playbook -i inventory/mycluster/inventory.ini --private-key /pem/id_rsa -e ansible_user=remote_user -b cluster.yml
```
![image](https://user-images.githubusercontent.com/7732624/217251644-161e8609-b5e9-4c31-9fa6-16a8241176e4.png)

After successful installation connect to VM and copy kubectl configuration file:
```
ssh -i /pem/id_rsa remote_user@YOUR_VM_IP
```
```
mkdir ~/.kube
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chmod 777 ~/.kube/config
kubectl get nodes
```
As result, you will see installed node:<br>
```
kubectl get nodes
```
![image](https://user-images.githubusercontent.com/7732624/217257411-625e1c0c-6273-451c-9656-83592180fa93.png)

### <a name="git-clone">Git clone</a>

In order to start working with the project, you need:
```
git clone https://github.com/BohdanHavran/DevOps-Basecamp-HomeTask.git
```
```
cd DevOps-Basecamp-HomeTask/task13
```

### <a name="Ingress-controller">Ingress-controller</a> 
```
kubectl apply -f nginx-ctl.yaml
```
```
kubectl apply -f path_provisioner.yaml
```

With this command, you will get a list of pods:
```
kubectl get pods -n ingress-nginx -w
```
![image](https://user-images.githubusercontent.com/7732624/217257802-269abf4a-f5d8-4245-b2a3-049924fe32fe.png)

With this command, you will get a list of services:
```
kubectl get svc --all-namespaces
```
![image](https://user-images.githubusercontent.com/7732624/217273874-f3cb4021-9db1-497a-bf65-6670e20a65ee.png)

### <a name="create-domain-name">Create domain name</a>
You can use a free service to get a domain name https://dynv6.com/ (if you have your own domain name, you can use it)<br>
![image](https://user-images.githubusercontent.com/7732624/217261942-1b2a6821-9f37-47b0-9b23-78a5636eebb6.png)

### <a name="deployment-and-ingress">Deployment and Ingress</a>
With this command you will run deployment.yaml file:
```
kubectl apply -f deployment.yaml
```
With this command, you will get a list of pods:
```
kubectl get pods
```
![image](https://user-images.githubusercontent.com/7732624/217263171-7770b83e-e2e7-42d4-ac49-5e7a95b9bc5c.png)

<b>Note</b>
- Before using the following commands, you should change the data in the file to your own

With this command you will run ingress.yaml file:
```
kubectl apply -f ingress.yaml
```
With this command, you will get a list of ingress:
```
kubectl get ingress
```
![image](https://user-images.githubusercontent.com/7732624/217262886-b5e66089-34ab-4f66-9649-52c9a2536954.png)

### <a name="certificate">Certificate</a>
Installing cert-manager:
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
```
<b>Note</b>
- Before using the following commands, you should change the data in the files to your own

Run letsencrypt-staging:
```
kubectl apply -f staging-issuer.yaml
```
Run letsencrypt-prod:
```
kubectl apply -f production-issuer.yaml
```
Now you should uncomment everything in the [ingress.yaml](https://github.com/BohdanHavran/DevOps-Basecamp-HomeTask/blob/main/task13/ingress.yaml) file and run it again:
```
nano ingress.yaml
```
OR
```
vim ingress.yaml
```
```
kubectl apply -f ingress.yaml
```
Check on the status of the issuer after you create it:
```
kubectl describe issuer letsencrypt-staging
```
![image](https://user-images.githubusercontent.com/7732624/217269070-8e596090-2d61-42f9-bb1c-688627221ff4.png)

Cert-manager will read annotations and create a certificate, which you can request and see:
```
kubectl get certificate
```
![image](https://user-images.githubusercontent.com/7732624/217268081-f0cae2d3-e623-4f81-8dab-fd5063c36bd4.png)

Now you should replace "letsencrypt-staging" with "letsencrypt-prod" in the [ingress.yaml](https://github.com/BohdanHavran/DevOps-Basecamp-HomeTask/blob/main/task13/ingress.yaml) file and run it again:
```
nano ingress.yaml
```
OR
```
vim ingress.yaml
```
```
kubectl apply -f ingress.yaml
```

### <a name="result">Result</a>
URL: https://bohdanhavran.dynv6.net/
![image](https://user-images.githubusercontent.com/7732624/217270268-043949d7-3091-4af4-bfe4-e03872997508.png)<br>
![image](https://user-images.githubusercontent.com/7732624/217270293-78e8ab36-df2d-40aa-8bb9-8f8e797a5f69.png)<br>
![image](https://user-images.githubusercontent.com/7732624/217270323-60123375-b59f-4b9a-9d2c-863b23820c3e.png)
