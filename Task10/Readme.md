Docker  

Task 1 

 1. Install docker 
 https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04  

1.1 Fix Got permission denied while trying to connect to the Docker daemon socket 
https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket  

2. Prepare a dockerfile based on Apache or Nginx image https://hub.docker.com/_/nginx   

FROM nginx:latest 
COPY index.html /usr/share/nginx/html 

3. Added your own index.html page with your name and surname to the docker image  

<html> 
  <head> 
    <title>Viacheslav Bruksha, Task10: Docker</title> 
  </head> 
  <body> 
    <h1>Viacheslav Bruksha, Task10: Docker</h1> 
  </body> 
</html> 

4. Run the docker container at port 8080  

ubuntu@ip-172-31-50-104:~/GL/Task10/Task1$ docker build -t mywebsite:v1 . 
Sending build context to Docker daemon  4.096kB 
Step 1/2 : FROM nginx:latest 
---> a99a39d070bf 
Step 2/2 : COPY index.html /usr/share/nginx/html 
---> 81bad88ca414 
Successfully built 81bad88ca414 
Successfully tagged mywebsite:v1 

5. Open page in Web Browser 
http://ec2-100-25-29-14.compute-1.amazonaws.com:8080/  

6. Report save in GitHub repository  

Task 2 

1. Prepare private and public network  

2. Prepare one dockerfile based on ubuntu with the ping command  

3. One container must have access to the private and public networks the second container must be in the private network  

4. A ) Run a container that has access to the public network and ping some resources ( example: google.com )  

B ) The second container ping the first container via a private network  

5. Report save in GitHub repository  
