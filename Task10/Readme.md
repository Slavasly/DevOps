 1. Install docker  https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04  

Fix Got permission denied while trying to connect to the Docker daemon socket 
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

ubuntu@ip-172-31-50-104:~/GL/Task10/Task1$ docker run -d -p 8080:80 mywebsite:v1
6e170444b3a636249535a48f2499bbf3a0d28495a6a3ce0a715a52ab4801d81f

5. Open page in Web Browser http://ec2-100-25-29-14.compute-1.amazonaws.com:8080/  

6. Report save in GitHub repository  
