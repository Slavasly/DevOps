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
![image](https://user-images.githubusercontent.com/44306982/218314045-5650aaac-263f-4305-a7cf-4c93312c33b0.png)

Turn on MetalLB:
Open `addons.yml`
```
nano inventory/mycluster/group_vars/k8s_cluster/addons.yml
```
Change settings:
```
metallb_enabled: true
metallb_speaker_enabled: true
metallb_avoid_buggy_ips: true
metallb_ip_range:
  - "Your_VM_private_ip/32"
```
![image](https://user-images.githubusercontent.com/44306982/218308951-e156c71b-962c-4296-ba82-ee4709f61a23.png)

Open `k8s-cluster.yml`
```
nano inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
```
Change settings:
```
kube_proxy_strict_arp: true
```
![image](https://user-images.githubusercontent.com/44306982/218266234-ad6b1fb4-27c0-4b5d-9b21-2e4ca31839ed.png)

Run execute container (<b>if you don't have [Docker installed](https://docs.docker.com/engine/install/ubuntu/), you should install it in advance</b>):
```
sudo docker run --rm -it -v ~/kubespray:/mnt/kubespray -v ~/.ssh:/pem quay.io/kubespray/kubespray:v2.20.0 bash
```
Go to kubespray folder and start ansible-playbook (command in container):
```
cd /mnt/kubespray
```
```
ansible-playbook -i inventory/mycluster/inventory.ini --private-key /pem/id_rsa -e ansible_user=gcp -b cluster.yml
```
![image](https://user-images.githubusercontent.com/44306982/218325524-c120962e-331d-4f37-8b1c-c1321331807c.png)

After successful installation connect to K8S VM and copy kubectl configuration file:
```
ssh -i /pem/id_rsa gcpr@YOUR_K8S_VM_IP
```
```
mkdir ~/.kube
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chmod 777 ~/.kube/config
kubectl get nodes
```
Finally, you will see installed kubernetes:<br>

![image](https://user-images.githubusercontent.com/44306982/218325581-dde5262c-0e3b-4ba7-89e3-4a25ae94265b.png)

### <a name="Ingress-controller">Ingress-controller</a> 
Copy file https://github.com/Slavasly/DevOps/blob/main/Task13%20K8S%20Kubespray/nginx-ctl.yaml 
https://github.com/Slavasly/DevOps/blob/main/Task13%20K8S%20Kubespray/path_provisioner.yaml to VM:

Open folder and Run commands:
```
kubectl apply -f nginx-ctl.yaml
```
```
kubectl apply -f path_provisioner.yaml
```
![image](https://user-images.githubusercontent.com/44306982/218326066-5db23e86-2551-42c6-8418-de8ddd8470d2.png)

Get a list of pods:
```
kubectl get pods -n ingress-nginx -w
```
![image](https://user-images.githubusercontent.com/44306982/218326123-445be936-0779-442b-abe1-9021f4161eb4.png)

Get a list of services:
```
kubectl get svc --all-namespaces
```
![image](https://user-images.githubusercontent.com/44306982/218326176-fb9fa8c6-a528-41fd-a556-45b13de53c75.png)

### <a name="create-domain-name">Create domain name</a>
You can use a free service to get a domain name https://dynv6.com/ (if you have your own domain name, you can use it)<br>
![image](https://user-images.githubusercontent.com/44306982/218327436-a29b3ccf-48d9-4d71-9551-0a4c0682a109.png)

### <a name="deployment-and-ingress">Apply Deployment, Service, Ingress</a>
With this command you will run deployment.yaml file:
```
kubectl apply -f deployment.yaml
```
Get a list of pods:
```
kubectl get pods
```

![image](https://user-images.githubusercontent.com/44306982/218335405-825a2d3a-2fa7-4150-ab81-34cad81ad714.png)

<b>Note</b>
- You should change the data in the file to your parameters

![image](https://user-images.githubusercontent.com/44306982/218335459-f5be43c6-ec9a-498d-a017-7a734e16377d.png)

Run ingress.yaml file:

![image](https://user-images.githubusercontent.com/44306982/218335576-149f0f12-0883-4ebb-bea8-b314190bde5e.png)

```
kubectl apply -f ingress.yaml
```
Get a list of your ingress:
```
kubectl get ingress
```
![image](https://user-images.githubusercontent.com/44306982/218335621-4bb383c1-c625-44e6-8b01-dcc71a48152c.png)

### <a name="certificate">Get a certificate</a>
Installing cert-manager:
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
```
<b>Note</b>
- Before using the following commands, you should change the data in the files to your own

Run letsencrypt-prod:
```
kubectl apply -f clusterissuer.yaml
```
Run `ingress.yaml`
```
kubectl apply -f ingress.yaml
```
Get status of the issuerafter creation:
```
kubectl describe clusterissuer
```
![image](https://user-images.githubusercontent.com/44306982/218338146-ef05aaf6-74ca-4200-ad15-ac663d7f5883.png)

Cert-manager will read annotations and create a certificate, which you can request and see:
```
kubectl get certificate
```
![image](https://user-images.githubusercontent.com/44306982/218339431-126bb74d-5245-4034-8e70-10f18cdfbd2d.png)

### <a name="result">Result</a>
![image](https://user-images.githubusercontent.com/44306982/218339576-0d6e09fd-5731-4e46-b0d0-1bcc56971075.png)

[URL: https://vbruksha.dynv6.net/](https://vbruksha.dynv6.net/)
