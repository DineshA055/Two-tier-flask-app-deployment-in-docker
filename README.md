# Two-tier-flask-app-deployment-in-docker  - PART-1

 TWO-TIER_ARCHITECTURE

Process up to deployment in Docker 
![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/15e9c7de-8088-4d28-b688-ad54e97bd726)




Docker solving the problem 

* When developer working in windows machine but client using linux machine to run the application 
* Here we are using docker to build a container
* We required a virtual environment 
* Build once run any where in supporting platform where docker can run the conatiner

![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/09691169-8ec1-476d-be49-48ef20211bb1)









Using Docker file to build the image and run in to the container

![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/d82364f9-d955-4d0b-9358-87d28b0acb3d)






Here an example for Docker file to build an image for a simple pyhon flask app

DOCKER_FILE_TO_BUILD_AN_IMAGE :-
![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/7689d5de-bf02-471c-be0f-35df3c5166fb)


NEXT  :- We start the process for deployment

Step1:-  If we start with Virtual machine to run the docker 
              We required an EC2 Instance 
Step2 :- Create a Ec2 instance for practise choose free tier for cost optimization on aws
              chosse free tier
Step3:- Also you can use to create a Ec2 instance with VPC secure-network in few steps 
             You can use TERRAFROM to write logic once create instance any time 

Next:- 
1. Instance is ready with some security allow only reuqired port for application this is applicable if you are using terraform logic to build the instance

2. Now Instance is ready with Ip will be shown in your output
3. Make sure with creation of key pair to connect your instance securley using SSH (secure shell)
* if you are using aws console use key-pair to create
* if you are using terraform logic add key_pair logic in code and generate a key from terminal
       CMD :- ssh-keygen -t rsa
                     provide a name for key 
                      it will be available in directory
4. Open terminall connect with secure shell SSH before you have to give access to read access to key 
* find the key directory use cmd to give read access
       cmd :- chmod 600 / path / key_name     (now you given read access)

To connect Instance using SSH command
CMD :-     ssh -i /directory and path / privtae_key_name  remote_machine_name@instance Ip

and give yes for finger print sign to connect instance 

NEXT:-
* Next upgrade the package to support for installting Docker and other software as well 
   Always maske sure with updating the package while you are in linux
 
Steps to follow :-

     CMD :- sudo apt update -y                      -y will skip asking yes to proceed

# install docker from browser using CMD :- 
                    sudo apt-get install docker.io

Next :- Add user to docker usergroup

CMD :- sudo usermod -aG docker $USER

CMD :- systemctl status docker           # to know the docker demaon is running 

Some time we required to restart to effect the change or exit and reconnect the remote instance 

Next :- we start build the image using Docker file 
            please check in github you have required source code for python and requirement file available in Git-hub repository
 
Once verfied all source code available in repository 

You can now clone the repository in terminal by coping the address from CODE BUTTON 
Click you can see clone copy Https :- copy url to clipboard      git repo

go and paste in termina with below commnad
CMD :- git clone   https: git -repo

Now git repository has been cloned in instance 

Now CD get in to the directoy of repository 

Create a DOCKER_FILE to build the image 

example giving below how to create a Docker file 
![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/44895965-3a1a-4ab2-b3a6-fb335ee51d62)


Next :- After creation of Docker file run command to build an image
 Make sure you are in repo directory of application you are building

CMD :- docker build   -t  laskapp:latest  .                       #Here dot . Metioned in end of command to get the docker file from directory to start search and get the file to build the image 

######## Rember to addd this cmd in docker file rm -rf /var/lib/apt/lists/*  ### it will remove temp file to save some space 

Image build is completed you can see the result :-

![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/4854ebcc-87bb-4446-adc4-61117ac6cfbc)

Before going to run the image in Container you can push the image in Docker Hub to save the image run are use it anywhere by pull the image from Docker

First :- Login to Docker 

Tag the image  CMD :- 
check the image  :- docker images
next :- tag it before push to docker hub

CMD :-   docker tag reposirotyname:latest docker-username/repositoryname       #repository name you can see in docker images command it will display name in repository
 
Push the image to Docker hub CMD :- 
      Again use cmd :- docker images          #you can see in repo image was tagged with user and name

psuh the image to docker hub :- 
    CMD   :- docker push docker_hub_accout_user-name/reponame of tagged  image   
     ex:   docker push   karthik1/app2:latest

NEXT :- you can run the container 

CMD :-  docker push karthik1/app2:latest   

Successfully piushed in to Docker Hub

Now Run the container and expose to port render the application in web page 

CMD :- docker -d -p 5000:5000 app2:latest                  

    # 5000:bind with container port 5000 app to open in port 

Now next need to run Mysql conatiner as well 
CMD :- docker -d -p 3000:3000 –name mysql  -e MYSQL_ROOT_PASSWORD=”admin” mysql:5.7

# mysql name of container  and  mysql have database it may required user and password and we required to give root password


Now you can see application running in browser make sure in security group 5000 is allowed in inbound in custom to access by particular for secure reason don’t open connect from eveywhere


Next :- CMD :-    docker ps 

will show the application running with conatiner ID and image and status of UP time and  PORT 

for practise you can choose my IP or Custom 
Example :-    Run in brwoser instacane ip    

 Ip:5000 in browser you can see application render in browser
![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/698f36bd-5c1c-4c2a-8223-df00e54e95e6)


*****You can see error in page because backend flask app conatiner and mysql conatiner is running but now connected ****** so we required to createa network connection when try to run the connatiner run

For practise always make sure discuss with team go for kill the conatiner 

Ex:- CMD :     docker kill backendContainerID  mysql ContainerID

Now you can proceeed with connect with same newtwork  

Before proceed create a network :- 
                CMD :- docker network create twotier


CMD :- docker run -d -p 5000:5000 –network=twotier   -e MYSQL_HOST=mysql -e MYSQL_USER=admin  -e MYSQL_PASSWORD=admin   -e MYSQL_DB=myDb app2:latest

Refer this bewlo of -e environment required provided in source code
# Here again running the conatiner with a newtwork has twotier for backend application and next run the Sql connatiner with same network 

Run SQL container with backend with same network

CMD :- docker run -d -p 3306:3306 –network=twotier -e MYSQL_DATABASE=myDb -e MYSQl_USER=admin  MYSQL_PASSWORD=admin MYSQL_ROOT_PASSWORD=admin mysql:5.7

#Am given mysql 5.7 image and also you can see both container baskend and mysql container in same network in twotier
 
Now check CMD :- dokcer ps 
two container where running with same network

if you want to know the network name 
CMD :- docker network ls

Now refresh the page you can see Mysql was connected with backed conatiner 

But still show Table has not created display a message and show some message 

# WE REQUIRED TO CREATE A TABLE IN DATABASE 

For that we need to login to MYSQL container to create a table

docker ps   and copy the container ID

CMD :- docker exec -it f92873921f86 bash            -it (iteractive terminal)

bash-4.2# mysql -u root -p                                   username with root
password:                                                              please provide passsword

mysql>                                                     # Now entered in to mysql

check myDb was created ---> show databases;
use myDB;
![image](https://github.com/DineshA055/Two-tier-flask-app-deployment-in-docker/assets/101075223/37d4797e-d3f5-467a-b99e-62f4b2b85fec)

next add table creation copy from github repo and paste to create table refer in above screeshot

Now you are inside sheel you can exit 

you can exit from mysql :- exit from bash

NOW REFRESH THE PAGE YOU CAN SEE FINAL PAGE RESULT OF BACKEND AND MYSQL has beed RUNNING AND RENDRING IN PAGE





















Before please conform with team and kill the container of backend and mysql container ID
  CMD :-   docker kill backend container ID and mysql conainer ID


NEXT :- 
***********STARTING WITH DOCKER COMPOSE*******************YAML file
Build two container with single yaml file 

Before we saw it was little long process to run each continer long process

Reduce time and use solution called docker-compose  
 

Use docker compose to simplify the process to run Docker container 


First step to install    CMD :- sudo apt-get install docker-compose

Next :- create a single docker-compose.yml with two container will be created 

We required to create a docker-compose.yml file 

version
service 
   backed and frontend
backend 
   image : get image form docker hub   karthik1/app2
   port :
  environment:                                       (-e mysql_host -e ...........)

  depends_on:
      - mysql                          (**** depend on below mysql container if we not given this backend                                                                    -                                                         end container will create unable to find mysql ***)

  mysql:
     image : mysql: 5.7
     port :
     environment :

    volumes:
      - ./message.sql:/docker-entrypoint-initdb.d/message.sql   # Mount sql script into container's /docker-entrypoint-initdb.d directory to get table automatically created
      - mysql-data:/var/lib/mysql  # Mount the volume for MySQL data storage

volumes:
  mysql-data:          #local machine mysql data available in case if container delete re-use again new


NOW execute the CMD to run backend and mysql container 

CMD :- docker-compose up -d

YOU CAN SEE O/p of Creating app2 mysql1    ..... done 
                                       Creating flask app2  backend  ..... done 

CMD :- docker ps 

both container is running with you can see the port as well

you can check in browser ip:5000

Broser will show the application is running 

****************COMPLETED WITH DOCKER COMPOSE**************************


NEXT – DOCUMET CONTIUNE WITH USING KUBERNETES
