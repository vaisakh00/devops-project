# devops-project
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
 
Jenkins installation steps 

```
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

sudo yum upgrade

sudo yum install fontconfig java-17-openjdk

sudo yum install jenkins`

sudo systemctl daemon-reload

sudo systemctl enable --now jenkins
```


Browse to http://localhost:8080 and wait until the Unlock Jenkins page appears. 

need to get the Administrator Password from 

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```


Next, proceed with installing Git on the Jenkins Server.

```
sudo yum install git -y
```
Next step is to integrate git with jenkins.

Jenkins -> Dashboard -> Manage Jenkins -> Plugins -> Available ->

Search for GitHub plugin and install it.




Next step is to Configure Jenkins.

Jenkins -> Manage Jenkins -> Tools -> Git ->

Give a name and type “git” in the Path to executable.





 Apply -> Save.

Successfully integrated Github with Jenkins.



Next step is to install Maven on the Jenkins server.

```
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.9.4/binaries/apache-maven-3.9.4-bin.tar.gz
tar -xvzf apache-maven-3.9.4-bin.tar.gz
mv apache-maven-3.9.4 maven
cd maven/bin
./mvn -v
cd ~
vim .bash_profile
    M2_HOME=/opt/maven
    M2=/opt/maven/bin
    JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.19.0.7-1.amzn2.0.1.x86_64
    PATH=$PATH:$HOME/bin:$JAVA_HOME:$M2_HOME:$M2
            (save it)
source .bash_profile
echo $PATH
```


Need to install maven plugins in jenkins.

Jenkins -> Manage Jenkins -> Plugins -> Available ->

search for Maven Integration -> install


Lets configure.

Manage Jenkins -> Tools -> JDK -> Add JDK -> give name and enter Java home path -> /usr/lib/jvm/java-11-openjdk-11.0.19.0.7–1.amzn2.0.1.x86_64








