# Kubernetes Helm Home Task
Content:<br>
 [Deploy Nginx](#1Deploy-nginx) | [Deploy Pacman](#2Deploy-pacman) | [Deploy MERN stack](#3Deploy-mern-stack)

<b>Note:</b>
- You must edit the values.yaml file before starting (depends on the project you are running)
- You can also use `--set` when starting the project and replace the required data with your own to avoid editing the file

### Prerequisite
Before start to deploy you need to install K8S:

[Instruction](https://github.com/Slavasly/DevOps_BaseCamp/tree/main/11.K8S%20(Install%2C%20Deploy)) - how to create K8S Cluster

or

[Instruction](https://github.com/Slavasly/DevOps_BaseCamp/tree/main/13.K8S%20(Kubespray%2C%20Ingress%2C%20Letsencrypt)) - how to create K8S Cluster with KubeSpray)

In order to start working with the project, you need to clone git and open Helm folder:
```
git clone https://github.com/Slavasly/DevOps_BaseCamp.git
cd DevOps_BaseCamp/14.K8S\ \(Helm\)/
```
We want to use Helm Chart as simple solution for deploying objects to K8S.

Prepairing a config files tree for deployment:
- Chart.yaml  --> info about the chart
- values.yaml --> values file for insertion while templating
- templates/  --> directory with manifest templates:
    - ingress.yaml
    - service.yaml
    - nginx.yaml
    - _helpers.yaml (with the function to use while templating)

![image](https://user-images.githubusercontent.com/44306982/233852540-2e16a097-002e-4169-a230-432dc99ee8e5.png)

Create Helm `nginx/Chart.yaml` with capital letter:
```
apiVersion: v1
name: nginx
description: A Helm chart for Kubernetes nginx deployment
type: application
version: 0.1.0
appVersion: "1.0"
```
Create `nginx/values.yaml` for values info:
```
replicaCount: 3
host: vbruksha.dynv6.net
image:
  repository: nginx
  tag: stable
  pullPolicy: IfNotPresent

port: 80

protocol: TCP
```
Create `nginx/templates/deployment.yaml` for deployment:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
  labels:
    app: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        ports:
        - name: {{ .Release.Name }}-http
          containerPort: {{ .Values.port }}
```
Create `nginx/templates/ingress.yaml` for ingress controller:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ .Release.Name }}-service
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
  - hosts:
    - {{ .Values.host }}
    secretName: quickstart-example-tls
  rules:
  - host: {{ .Values.host }}
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: {{ .Release.Name }}-service
            port:
              number: {{ .Values.port }}
```
Create `nginx/templates/service.yaml` for service:
```
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-service
spec:
  selector:
    app: {{ .Release.Name }}
  ports:
  - name: {{ .Release.Name }}-service-port
    protocol: {{ .Values.protocol }}
    port: {{ .Values.port }}
    targetPort: {{ .Release.Name }}-http
```
Installing Helm Chart:
```
sudo snap install helm --classic
```
![image](https://user-images.githubusercontent.com/44306982/233852697-777ba57c-bce0-43d8-b524-285812e408dd.png)

In case of issue with installation need to delete prev. ingress and uninstall Helm Chart:

`kubectl get ingress --all-namespaces`

`kubectl delete ingress nginx-service -n default`

`helm ls`

`helm uninstall nginx-helm`

![image](https://user-images.githubusercontent.com/44306982/233855233-80090dc3-e6cc-427f-981c-0d703014082b.png)


### <a name="1Deploy-nginx">1.Deploy Nginx</a>

 Deploy Nginx via helm with Ingress configuration 

In the task we used ingress and the certificate that we created in the last task.<br> 

We can run the Helm chart:

`helm install nginx-helm nginx/`

`kubectl get all`

`helm ls`

![image](https://user-images.githubusercontent.com/44306982/233855100-94d64f19-e01b-4542-b533-38b6021cbb5e.png)

Use can use command `helm upgrade --install --atomic …` to change some parameters `--set host=<your_domain_name>`

`helm upgrade --install --atomic nginx-helm nginx/ --set replicaCount=4 --set host=vbruksha.v6.rocks`

`kubectl get all`

![image](https://user-images.githubusercontent.com/44306982/233855760-6bceedef-2065-4eee-9d67-b86273e305c9.png)

<b>Result:</b><br>
URL: https://vbruksha.dynv6.net/nginx-helm

URL: https://vbruksha.v6.rocks/nginx-helm

### <a name="2Deploy-pacman">2.Deploy Pacman</a>

## Task 2
Create and deploy your own chart with the [Pacman](https://hub.docker.com/r/golucky5/pacman) game. 
[Helm get start](https://helm.sh/docs/chart_template_guide/getting_started/)

Expected result:

![image](https://user-images.githubusercontent.com/44306982/233730510-c3be808a-8d48-4c9c-a8e0-cb0af794cfb2.png)
With this command you will run the helm chart:

```
helm install pacman pacman/
```
![image](https://user-images.githubusercontent.com/44306982/233857941-2dd06d78-dbda-4d4b-8a41-2fa2e9047d96.png)

<b>Result:</b><br>
URL: https://vbruksha.dynv6.net

<b>Note:</b>
- host can be changed in the `values.yaml` file or when starting helm chart change its value using `--set host=<your_domain_name>`


### <a name="3Deploy-mern-stack">3.Deploy MERN stack</a>

## Task 3. Deploy MERN stack (MongoDB, Express.js, React.js, Node.js) via Helm

Create a Mern bitname repository:
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
![image](https://user-images.githubusercontent.com/44306982/233860675-f3537e8b-05ea-4566-aa18-b00efb8c99ab.png)

Run the Helm Chart with Bitnami node installation:
```
helm install mern-stack bitnami/node
```
![image](https://user-images.githubusercontent.com/44306982/233860804-c70a9c25-c2fc-4fd2-964f-f57c6e940a2e.png)

With the help of this command, you can view all the resources that have been created:
```
kubectl get all
```
![image](https://user-images.githubusercontent.com/44306982/233861048-b2a324ca-c4c6-47cc-b598-d251af0d6826.png)

<b>Note:</b>
- Before using the following commands, you should change the file `ingress.yaml` on your own

![image](https://user-images.githubusercontent.com/44306982/233861174-cefc1afa-fd04-48ce-a8f8-f1c03f0e43db.png)

You need to run `ingress.yaml` and wait till it complete:
```
kubectl apply -f mern/ingress.yaml
```
![image](https://user-images.githubusercontent.com/44306982/233861348-b28d38c1-9ca1-49bf-8a93-0007309f4aff.png)

<b>Result:</b><br>
URL: https://vbruksha.dynv6.net/mern
![image](https://user-images.githubusercontent.com/44306982/233861428-b010c22b-c67d-459e-8600-62c4a6aa08ba.png)
