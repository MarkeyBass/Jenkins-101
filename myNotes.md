docker pull jenkins/jenkins:lts

docker network create -d bridge jenkins-101-network

docker volume create Jankins-101-vol

docker run --network="jenkins-101-network" -d --name jenkins-101-blueocean -p 8080:8080 -p 50000:50000 -v Jankins-101-vol:/var/jenkins_home 3385e3c0b235

# Get the Password
docker exec -it jenkins-blueocean bash
cat /var/jenkins_home/secrets/initialAdminPassword


installed modern-deshbord && blue ocean plugins


# Jenkins important global variables:
BUILD_ID 
BUILD_URL


 cd /var/jenkins_home/workspace
 ls -ltra

 cd
 ls -ls
-------
cheockout the folders/ file (plugins, updates, config.xml, logs)



# entering to Jenkins container as a root user
 docker exec -u 0 -it jenkins-101-blueocean bash

# changing password
root@1c717716f11b:/# passwd
New password: 
Retype new password: 
passwd: password updated successfully

# entering the container regurly and the switching to root user
 docker exec -it jenkins-101-blueocean bash
 su -

 # installing python
 apt-get update
 apt-get install python3
 apt-get install python3
 python3 --version


# Setting Agents in Jenkins
➡  Dashboard
  Manage Jenkins
  Nodes
  Manage nodes and clouds
  Configure Clouds
  ➡ install docker plugin in order to use cloud agent

## alpine/socat container to forward traffic from Jenkins to Docker Desktop on Host Machine

https://stackoverflow.com/questions/47709208/how-to-find-docker-host-uri-to-be-used-in-jenkins-docker-plugin

docker run -d --name 101-Agent --restart=always -p 127.0.0.1:2376:2375 --network jenkins-101-network -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock

-> inspecting
--------------
docker inspect <container_id> | grep IPAddress

docker inspect 5b2fbdb693f9aa4c1408902007d05bec6d0d6485e2650f2fc8bc2f236a753853 | grep IPAddress

docker inspect 101-Agent
  "Networks": {
    "jenkins-101-network": {
        "IPAMConfig": null,
        "Links": null,
        "Aliases": [
            "5b2fbdb693f9"
        ],
        "NetworkID": "9e494eb63da70b8580d604d824659f103c2f62fb8dbeda78f465e31b73111163",
        "EndpointID": "31fc736ac32ec85757810c56b779216bed8dcea8fb4ee62c849c18f9095792a7",
        "Gateway": "172.21.0.1",
        "IPAddress": "172.21.0.3",
        "IPPrefixLen": 16,
        "IPv6Gateway": "",
        "GlobalIPv6Address": "",
        "GlobalIPv6PrefixLen": 0,
        "MacAddress": "02:42:ac:15:00:03",
        "DriverOpts": null
    }
  }

docker inspect 101-Agent | grep IPAddress
   "IPAddress": "172.21.0.3",

## Using my Jenkins Python Agent

docker pull devopsjourney1/myjenkinsagents:python

# Setting Agents in Jenkins ➡ Contenue
➡  Dashboard
  Manage Jenkins
  Nodes
  Manage nodes and clouds
  Configure Clouds
  ➡ install docker plugin in order to use cloud agent

➡ Dashboard
Manage Jenkins
Configure Clouds
  
  --> Name: docker
      Docker Host Uri: tcp://172.21.0.3:2375
      ✅ Enabled
      Test conection
       -> Version = 20.10.22, API Version = 1.41
      
➡ Create a docker agent template:
  **Labels: docker-agent-alpine**
  ✅ Enabled
  name: docker-agent-alpine
  Docker Image: jenkins/agent:alpine-jdk11
  Instance Capacity: 2 (Amount of agents it's gonna spawn - להשריץ)
  Remote File System Root: /home/jenkins (Default folder for Jenkins Agent)
  💾👆 SAVE


# Go to th egob that will use the agents and start using them:
➡ Dashboard
my_first_job
Configuration
  -> ✅ Restrict where this project can be run
      Label Expression: **docker-agent-alpine**

Adding another image with python installed - adding it to the template
dockerfile
-----------
FROM jenkins/agent:alpine-jdk11
USER root
RUN apk add python3
RUN apk add py3-pip
USER jenkins

--> go to the Directory of the Dokerfile and run:
    docker build -t jenkins_py_agent:1.0.0 . 

    docker images jenkins_py_agent
    
    docker run --name JPA jenkins_py_agent:1.0.0
    docker start JPA

    docker exec -it JPA bash

jenkins_py_agent:1.0.0
docker run -d --name JPA --restart=always -p 127.0.0.1:2376:2375 --network jenkins-101-network -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock 

### GPT ###
===========
docker run --name JPA jenkins_py_agent:1.0.0 tail -f /dev/null
In this command, we start the container with the name JPA and the jenkins_py_agent:1.0.0 image. We also specify the command to be tail -f /dev/null, which is a command that outputs nothing and runs indefinitely. This will keep the container running until we stop it explicitly.

After running this command, you can then use the docker exec -it JPA sh command to start a shell session in the running container.