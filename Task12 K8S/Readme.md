## Task12 k8s (CRE Docker)

**Run Kubemaster**

![image](https://user-images.githubusercontent.com/44306982/216846862-0e31ba5c-ff1b-4456-87ab-ad95d61dbb5b.png)

**SSH to the Kubemaster VM**

![image](https://user-images.githubusercontent.com/44306982/216847792-bd19d691-e179-4f69-b8e9-a3a9f68d7fad.png)


**1.Get information about your worker node and save it in kubenode_describe.txt file**

`kubectl describe nodes kubenode > kubenode_describe.txt`

*Copy file to local directory and upload to Github*

![image](https://user-images.githubusercontent.com/44306982/216851149-af636110-89aa-4b61-bc50-4c8dd483fd9f.png)

([kubenode_describe.txt](kubenode_describe.txt))

**2.Create a new namespace (all resources below will create in this namespace)**

**3.Prepare deployment.yaml file which will create a Deployment with 3 pods of Nginx or Apache and service for access to these pods via ClusterIP and NodePort.**

- Show the status of deployment, pods and services. Describe all resources which you will create and logs from pods

**4.Prepare two job yaml files:**

- One gets content via curl from an internal port (ClusterIP)

- Second, get content via curl from an external port (NodePort)

**5.Prepare Cronjob.yaml file which will test the connection to Nginx or Apache service every 3 minutes.**

Files and output from all tasks save in GitHub repository
