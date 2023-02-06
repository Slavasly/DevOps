# Task12 k8s (CRE Docker)

**Run Kubemaster**

![image](https://user-images.githubusercontent.com/44306982/216846862-0e31ba5c-ff1b-4456-87ab-ad95d61dbb5b.png)

**SSH to the Kubemaster VM**

![image](https://user-images.githubusercontent.com/44306982/216847792-bd19d691-e179-4f69-b8e9-a3a9f68d7fad.png)


## 1.Get information about your worker node and save it in `kubenode_describe.txt` file

`kubectl describe nodes kubenode > kubenode_describe.txt`

*Copy file to local directory and upload to Github*

![image](https://user-images.githubusercontent.com/44306982/216851149-af636110-89aa-4b61-bc50-4c8dd483fd9f.png)

([kubenode_describe.txt](kubenode_describe.txt))

## 2.Create a new namespace (all resources below will create in this namespace)

`kubectl create namespace ns-devops`

*Get all namespaces*

`kubectl get ns`

![image](https://user-images.githubusercontent.com/44306982/216960470-f257c9ec-97dd-4afa-8b0c-baf9e8063b38.png)


## 3.Prepare deployment.yaml file which will create a Deployment with 3 pods of Nginx or Apache and service for access to these pods via ClusterIP and NodePort.**

- Show the status of deployment, pods and services. Describe all resources which you will create and logs from pods

*Create deployment.yml* [deployment.yml](deployment.yml)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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
        image: nginx:latest
        ports:
        - containerPort: 80
   ```
   
*Create service-clusterip.yml* [service-clusterip.yml](service-clusterip.yml)

```
apiVersion: v1
kind: Service
metadata:
  name: service-clusterip-nginx
spec:
  selector:
    app: nginx
  ports:
  - name: http
    port: 80
    targetPort: 80
  type: ClusterIP
  ```
  
*Create service-nodeport.yml* [service-nodeport.yml](service-nodeport.yml)  

```
apiVersion: v1
kind: Service
metadata:
  name: service-nodeport-nginx
spec:
  selector:
    app: nginx
  ports:
  - name: http
    port: 80
    targetPort: 80
    nodePort: 30030
  type: NodePort
  ```
 *Copy .yml files to kubemaster VM*
  
  `wget https://raw.githubusercontent.com/Slavasly/DevOps/main/Task12%20K8S/deployment.yml`\
  `wget https://raw.githubusercontent.com/Slavasly/DevOps/main/Task12%20K8S/service-clusterip.yml`\
  `wget https://raw.githubusercontent.com/Slavasly/DevOps/main/Task12%20K8S/service-nodeport.yml`
  
  ![image](https://user-images.githubusercontent.com/44306982/216979692-3196c521-f13c-4650-8e63-63a0036de5fb.png)
  
 *Delete deployment which created in wrong namespace*
 
  `kubectl delete deployment nginx-deployment`
 
 *Apply `deployment.yml` and verify deployment and pods*
 
 `kubectl apply -f ./deployment.yml -n ns-devops`
 
 *Describe deployment and pods*
 
 `kubectl describe deployment -n ns-devops`
 
 `kubectl get pods -n ns-devops`
 
![image](https://user-images.githubusercontent.com/44306982/217018930-f3d62f55-5504-48a3-a9ea-fd95f06d7bc2.png)

*Apply `Service-clusterip.yml` get and describe services and replicaset*

`kubectl apply -f ./service-clusterip.yml -n ns-devops`

`kubectl get services --namespace ns-devops`

`kubectl describe service service-clusterip-nginx --namespace ns-devops`

`kubectl get replicaset -n ns-devops`

![image](https://user-images.githubusercontent.com/44306982/217043815-e55b8913-dac4-4b32-a3bb-2c6a584d12ea.png)

*Apply `service-nodeport.yml` get, describe*

`kubectl apply -f ./service-nodeport.yml -n ns-devops`

`kubectl get services --namespace ns-devops`

`kubectl describe service service-nodeport-nginx --namespace ns-devops`

![image](https://user-images.githubusercontent.com/44306982/217046593-fdd834d3-888a-441d-b1c8-01e927644a83.png)

*Get Logs from the pods*

`kubectl logs nginx-deployment-6b7f675859-j8cg9 -n ns-devops`

`kubectl logs nginx-deployment-6b7f675859-pf65l -n ns-devops`

`kubectl logs nginx-deployment-6b7f675859-tlr8q -n ns-devops`

![image](https://user-images.githubusercontent.com/44306982/217049553-bee9c83d-3e51-47a3-9f77-c39fedd08670.png)

## 4.Prepare two job yaml files:**

- One gets content via curl from an internal port (ClusterIP)

*Create `Job_curl_ClusterIP.yml`

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job-curl-clusterip
spec:
  template:
    spec:
      containers:
      - name: job-curl-clusterip
        image: curlimages/curl
        command: ['curl', '10.97.150.254']
      restartPolicy: OnFailure
  backoffLimit: 4
  ```
*Apply job, get job and logs*
  
`kubectl apply -f ./Job_curl_ClusterIP.yml -n ns-devops`

`kubectl get job --namespace ns-devops`

`kubectl logs job/job-curl-clusterip -n ns-devops`

![image](https://user-images.githubusercontent.com/44306982/217100259-a4a47bd4-4359-43c6-84c6-6b5403114d8c.png)

- Second, get content via curl from an external port (NodePort)

*Create `Job_curl_NodePort.yml` for NodePort

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job-curl-nodeport
spec:
  template:
    spec:
      containers:
      - name: job-curl-nodeport
        image: curlimages/curl
        command: ['curl', '10.107.220.215:30030']
      restartPolicy: OnFailure
  backoffLimit: 4
  ```

`kubectl apply -f ./Job_curl_NodePort.yml -n ns-devops`

`kubectl get job --namespace ns-devops`

`kubectl logs job/job-curl-nodeport -n ns-devops`

![image](https://user-images.githubusercontent.com/44306982/217099803-e5922420-b861-4b44-8e7d-ddf18e8c596f.png)

## 5.Prepare Cronjob.yaml file which will test the connection to Nginx or Apache service every 1 minutes.**

*Create a `job_cronjob.yml`*

![image](https://user-images.githubusercontent.com/44306982/217108340-65b45848-dcf7-4277-8ef1-397b670ddbd0.png)

*Apply Cronjob, get, get job, logs job*

![image](https://user-images.githubusercontent.com/44306982/217109201-94f2e670-8a0a-4e72-ba2f-a69a0bc16240.png)

*Describe Cronjob*

![image](https://user-images.githubusercontent.com/44306982/217109751-809b9679-e40e-4028-8f48-8a84ced09df6.png)

Files and output from all tasks save in GitHub repository
