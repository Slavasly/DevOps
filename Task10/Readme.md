## Docker  

# Task 1 

 **1. Install docker**
 
 https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04  

**1.1 Fix Got permission denied while trying to connect to the Docker daemon socket**

https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket  

**2. Prepare a dockerfile based on Apache or Nginx image**
```
FROM nginx:latest 
COPY index.html /usr/share/nginx/html 
```
https://hub.docker.com/_/nginx

**3. Added your own index.html page with your name and surname to the docker image**
```
<html> 
  <head> 
    <title>Viacheslav Bruksha, Task10: Docker</title> 
  </head> 
  <body> 
    <h1>Viacheslav Bruksha, Task10: Docker</h1> 
  </body> 
</html> 
```
**4. Build a Docker Image from Dockerfile**

`docker build -t mywebsite:v1 .`

>Sending build context to Docker daemon  4.096kB
>
> Step 1/2 : FROM nginx:latest 
>
>---> a99a39d070bf 
>
>Step 2/2 : COPY index.html /usr/share/nginx/html 
>
>---> 81bad88ca414 
>
>Successfully built 81bad88ca414 
>
>Successfully tagged mywebsite:v1 

**5. Run the docker container at port 8080**

`docker run -d -p 8080:80 mywebsite:v1`
>OK 6e170444b3a636249535a48f2499bbf3a0d28495a6a3ce0a715a52ab4801d81f

**5. Open page in Web Browser**

http://ec2-100-25-29-14.compute-1.amazonaws.com:8080/  

![image](https://user-images.githubusercontent.com/44306982/214729686-f728b973-4b34-41d0-9dc1-a68a4d00ae41.png)

**6. Report save in GitHub repository**

https://github.com/Slavasly/GL/tree/main/Task10


# Task 2

**1. Create public network win name wan**

`docker network create wan`

>8f9aa4cf7f9ba196169033b1842d5051dae2375b752abc8039f5c452f1a8957c 

**1.1 Create internal network with name lan**

`docker network create lan --internal`

>a7eb7e299b03f6e113421868a49e16a84b8d8a3a4ebb3ae6ec0ed24683ef4ec0 

**2. Prepare one Dockerfile based on ubuntu with the ping command**

```
FROM ubuntu:latest 

         RUN apt update && apt install -y iputils-ping && \ 

         apt clean && rm -rf /var/lib/apt/lists/* 

         CMD bash 
```

**2.1 Go to the foled and build Docker image**

`cd /GL/Task10/Task2`

`docker build -t ping_host .`

>Sending build context to Docker daemon  2.048kB 
>
>Step 1/3 : FROM ubuntu:22.04 
>
>---> 6b7dfa7e8fdb 
>
>Step 2/3 : RUN apt update && apt install -y iputils-ping &&          apt clean && rm -rf /var/lib/apt/lists/* 
>
>---> Running in 2249c9e3943a 
>
>Removing intermediate container 2249c9e3943a 
>
>---> 59d29d685d48 
>
>Step 3/3 : CMD bash 
>
>---> Running in acbe3a41e8bd 
>
>Removing intermediate container acbe3a41e8bd 
>
>---> cfda8ced9cb7 
>
>Successfully built cfda8ced9cb7 
>
>Successfully tagged ping_host:latest 

![image](https://user-images.githubusercontent.com/44306982/214942405-d65e7b69-3378-473a-8123-0d9bb1289fed.png)

**3. One container must have access to the private and public networks the second container must be in the private network**

- Run container from ping_host image with name Host_wan assigned to wan network

`docker run -d --name host_wan --network wan ping-host`

![image](https://user-images.githubusercontent.com/44306982/214942744-cd9cba9f-d290-478a-8189-628157c8809f.png)
- Assign container host_wan to tha internal network lan

`docker network connect lan host_wan`

![image](https://user-images.githubusercontent.com/44306982/214942822-ba91aaf3-c6fd-4759-9c94-05764ecbd2dc.png)

- Run container from ping-host image with name Host_lan assigned to internal network lan 

`docker run -d --name host_lan--network lan ping-host`

![image](https://user-images.githubusercontent.com/44306982/214943026-9b587a48-4665-4dc6-bfd2-5fdff48ddf44.png)

- Inspect internal (lan) and external (wan) ip addresses for the host_wan and host_lan conteiners 

``docker inspect --format='{{.NetworkSettings.Networks.wan.IPAddress}}` host_wan``

``docker inspect --format='{{.NetworkSettings.Networks.lan.IPAddress}}` host_wan``

``docker inspect --format='{{.NetworkSettings.Networks.lan.IPAddress}}` host_lan``

``docker inspect --format='{{.NetworkSettings.Networks.wan.IPAddress}}` host_lan``

![image](https://user-images.githubusercontent.com/44306982/214943117-b0db1719-a45b-4cbd-860d-9b1beedfe1a5.png)

**4. A ) Run a container that has access to the public network and ping some resources ( example: i.ua )**

- Run conteiner host_wan in interactive mode and ping host_lan IP
 
`docker exec -it host_wan sh`

`Ping host_wan to host_lan IP`

 ![image](https://user-images.githubusercontent.com/44306982/214943391-11dcd213-e6d4-437f-b83b-27e39714f476.png)

- Ping host_wan IP with i.ua

`Ping host_wan to i.ua`

![image](https://user-images.githubusercontent.com/44306982/214943444-9dcb5ad7-5185-4595-8285-8cf12408d14b.png)

**B ) The second container ping the first container via a private network**

- Run conteiner host_lan in interactive mode and ping internal host_wan and i.ua IP

`docker exec -it host_lan sh`

![image](https://user-images.githubusercontent.com/44306982/214943277-bed643f5-3917-440a-975f-2543052a8e2f.png)

**5. Report save in GitHub repository**

https://github.com/Slavasly/GL/tree/main/Task10
