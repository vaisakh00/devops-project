# Devops-project
#### Jenkins-Maven-Tomcat-Github-Docker-Ansible
Welcome to the DevOps Webpage Deployment Project! This project aims to streamline the deployment process of a web application on a Tomcat server using a robust DevOps pipeline. Leveraging the power of Docker, Jenkins, and Ansible, I've designed a comprehensive solution that automates the building, testing, and deployment stages, ensuring a smooth and efficient workflow.

Project Highlights

**Containerized Deployment**:
Utilize Docker for containerization, allowing for consistent deployment across different environments.

**Continuous Integration with Jenkins**: 
Implement continuous integration to automatically build and test your application whenever changes are pushed to the repository.

**Efficient Deployment using Ansible**:
Automate the deployment process with Ansible playbooks, ensuring reliable and repeatable deployments.

**Web Application on Tomcat**: 
Deploy your web application on an Apache Tomcat server, a widely-used and reliable servlet container.


Initially, we are going to setup Jenkins server
 
### Jenkins installation steps 

```
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

sudo yum upgrade

sudo yum install fontconfig java-17 -y

sudo yum install jenkins -y

sudo systemctl daemon-reload

sudo systemctl enable --now jenkins
```


Browse to http://localhost:8080 and wait until the Unlock Jenkins page appears. 

need to get the Administrator Password from 

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/jenkinhome.png)


Next, proceed with installing Git on the Jenkins Server.

```
sudo yum install git -y
```
Next step is to integrate git with jenkins.

Jenkins -> Dashboard -> Manage Jenkins -> Plugins -> Available ->

Search for GitHub plugin and install it.

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot1.png)


Next step is to Configure Jenkins.

Jenkins -> Manage Jenkins -> Tools -> Git ->

Give a name and type “git” in the Path to executable.

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot2.png)



 Apply -> Save.

Successfully integrated Github with Jenkins.


#### Next step is to install Maven on the Jenkins server.

```
yum install maven -y
```

Need to install maven plugins in jenkins.

Jenkins -> Manage Jenkins -> Plugins -> Available ->

search for Maven Integration -> install


![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot3.png)


Lets configure.

Manage Jenkins -> Tools -> JDK -> Add JDK -> give name and enter Java home path -> /usr/lib/jvm/java-17-openjdk

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot6.png)

Maven -> Add Maven -> give name -> enter Maven home path -> /usr/share/maven



![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot5.png)


Apply -> Save.

Successfully configured and integrated Maven with Jenkins.

### Next step is setting up a docker server

launch a ec2 instance, connect to terminal and install docker


Lets Integrate Docker with Jenkins

Jenkins -> Manage Jenkins -> Plugins -> Available -> Search for Publish Over SSH -> Install


![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot7.png)



Lets configure Docker in Jenkins

Jenkins -> Manage Jenkins -> System -> Publish Over SSH -> SSH Servers -> Add -> Give Name -> Hostname : <Docker host private ip> -> Username : dockeradmin -> advanced -> enable password based authentication -> password : <password of dockeradmin -> test configuration -> Apply -> Save


![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot8.png)


Successfully instegrated docker with jenkins.

We are going to use Ansible as a deployment tool 

### setting up a Ansible server.

For that we need to create an ec2 instance and connect to terminal


```
sudo -i
useradd ansadmin
passwd ansadmin
vim /etc/ssh/sshd_config
  ( PasswordAuthentication yes )
vim /etc/sudoers
  ansadmin        ALL=(ALL)       NOPASSWD: ALL
systemctl restart sshd
sudo su - ansadmin
ssh-keygen
```

install ansible 

```
sudo yum install ansible -y
```
Lets Integrate Docker with Ansible

Connect to Docker host terminal
```
useradd ansadmin
passwd ansadmin
vim /etc/sudoers
   ansadmin        ALL=(ALL)       NOPASSWD: ALL
```

Go to the Ansible terminal

```
vim /etc/ansible/hosts
```

Clear everything in this file and add this 

```
[docker]
< docker private ip >
```
save it.

```
sudo su - ansadmin
ssh-copy-id < docker private ip >
```

```
ansible all -m ping
```

Lets Integrate Ansible with Jenkins

Jenkins -> Manage Jenkins -> System -> Pubish Over SSH -> Add -> SSH Server -> Give Name (ansible-server)-> Hostname : < ansible private ip address > -> Username : ansadmin -> Advanced -> enable password authentication -> Password : ( give the password of ansadmin ) -> Test Configuration.

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot9.png)



Goto Ansible server terminal

```
sudo su - ansadmin
cd /opt
sudo mkdir docker
sudo chown ansadmin:ansadmin docker
```

```
sudo yum install docker -y
sudo usermod -aG docker ansadmin
sudo systemctl enable --now docker
cd /opt/docker
vim Dockerfile
    FROM tomcat:latest
    RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
    COPY ./*.war /usr/local/tomcat/webapps
sudo chmod 777 /var/run/docker.sock
```

```
sudo vim /etc/ansible/hosts
    [docker]
    < docker private ip >

     [ansible]
    < ansible private ip >
```

```
ssh-copy-ip < ansible private ip >
sudos u - ansadmin
docker login
```

Enter Username and Password of Dockerhub account to login


```
cd /opt/docker
vim webapp.yml
 ---
 - hosts: ansible
   tasks:
     - name: create docker image
       command: docker build -t webapp:latest .
       args:
         chdir: /opt/docker
 
     - name: create tag to push image onto dockerhub
       command: docker tag webapp:latest vaisakh573/webapp:latest
 
     - name: push docker image
       command: docker push vaisakh573/webapp:latest

```
```
cd /opt/docker
vim deploy_webapp.yml
 ---
 - hosts: docker
   tasks:
     - name: Stop existing container
       command: docker stop webapp-server
       ignore_errors: yes
 
     - name: Remove the container
       command: docker rm webapp-server
       ignore_errors: yes
 
     - name: Remove image
       command: docker rmi vaisakh573/webapp:latest
       ignore_errors: yes
 
     - name: Create container
       command: docker run -d --name webapp-server -p 8082:8080 vaisakh573/webapp:latest
```                                

Goto Docker host terminal

```
chmod 777 /var/run/docker.sock
```

### Now lets create the jenkins job

Jenkins -> New item -> Name -> Maven project -> OK

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot9-1.png)


in source code management -> Git: add repositry url -> branches to build : specify the branch

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot10.png)


in Build Triggers -> Poll SCM schedule * * * * * 

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot11.png)


build -> Root POM : pom.xml -> Goals and options: clean install

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot12.png)



Post-build Actions -> send build artifacts over SSH -> SSH Server -> name: ansible-server -> source files: webapp/target/*.war -> remove prefix: webapp/target -> remote directory: //opt//docker -> Exec command
```
ansible-playbook /opt/docker/webapp.yml;
sleep;
ansible-playbook /opt/docker/deploy_webapp.yml
```
Apply -> save

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot13.png)



Then build the project


![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot14.png)

now you can access the webpage with **http://docker-server-ip:8082/webapp/**

![Alt Text](https://github.com/vaisakh00/devops-project/raw/main/images/Screenshot15.png)

We have configured our Jenkins job in such a way that if somebody modified code, it should automatically build the code, create the image, create the container and we could able to access those changes from the browser.














