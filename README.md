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


 

