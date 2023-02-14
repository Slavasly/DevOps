# Introduction to AWS

## Task: Prepare the environment and install LMS Moodle.

**1.Create an AWS account.**

![image](https://user-images.githubusercontent.com/44306982/215321752-fb4723d6-bda9-4844-bd69-aa51b6dcad69.png)

**2.Create EC2 instance**

>Requirements:
AWS EC2  server (1 GiB RAM, 1 vCPU, 10 GiB)

![image](https://user-images.githubusercontent.com/44306982/215322043-945c276c-cbab-44e6-996c-2c468ff658d8.png)

**3.Install MySQL Server**

https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04

- Create a user on the MySQL server for a specific DB (which will be used for LMS Moodle)

- Create a database for LMS Moodle

- Grant permissions on a database for the user

**4.Install Apache HTTP Server**

https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-20-04

**5.Install PHP as a module for Apache HTTP Server**

https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-22-04

**6.Download source of LMS Moodle**

https://download.moodle.org/download.php/stable401/moodle-4.1.1.tgz

- Copy source files into /var/www/ directory or another directory under web-server management

- Create a directory named “moodledata” out of the web-server directory

**7.Install moodle*

![image](https://user-images.githubusercontent.com/44306982/215322380-d39950e7-60ec-4e4f-8eb5-328c78f13392.png)

https://docs.moodle.org/311/en/Installing_Moodle

**Moodle learning platform**

http://18.196.134.49/moodle/my/

