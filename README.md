
# SonarQube And JFrog integration With Jenkins:

In this phase, we will learn how to install SonarQube and JFrog and How to integrate with Jenkins.

This phase includes installation and integration.

SonarQube:

SonarQube is a self-managed, automatic code review tool that systematically helps you deliver clean code. SonarQube integrates into your existing workflow and detects issues in your code to help you perform continuous code inspections of your projects. Organizations start off with a default set of rules and metrics called the Sonar Way Quality Profile. This can be customized per project to satisfy different technical requirements. Issues raised in the analysis are compared against the conditions defined in the quality profile to establish your quality gate.

A Quality Gate is an indicator of code quality that can be configured to give a go/no-go signal on the current release-worthiness of the code. It indicates whether your code is clean and can move forward.

A passing (green) quality gate means the code meets your standard and is ready to be merged.

A failing (red) quality gate means there are issues to address.

      
      


## Prerequisites
•	An AWS T2.Medium EC2 instance (Ubuntu).

•	Open port 9000 in the security group.

## Installation

Login to the instance as a Ubuntu user.

Using docker we have to install the SonarQube.

Install Docker in the instance.


```bash
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
apt-cache policy docker-ce
sudo apt-get install -y docker-ce
sudo usermod -aG docker ubuntu
sudo systemctl status docker

```
Install docker-compose
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
Change permission and check the version of docker.

```bash
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```
Install SonarQube

```bash
sudo sysctl -w vm.max_map_count=262144
mkdir sonar
cd sonar
vim docker-compose.yml
sudo docker–compose up
docker ps
```
Use the docker-compose.yml
```bash
version: '2'
services:
  sonarqube:
    image: sonarqube
    ports:
      - '9000:9000'
    networks:
      - sonarnet
    environment:
      - sonar.jdbc.username=sonar
      - sonar.jdbc.password=sonar
      - sonar.jdbc.url=jdbc:postgresql://db:5432/sonar
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
    ulimits:
      nofile:
       soft: 65536
       hard: 65536
  db:
    image: postgres
    networks:
      - sonarnet
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

networks:
  sonarnet:
    driver: bridge

volumes:
  sonarqube_conf:
  sonarqube_data:
  sonarqube_extensions:
  postgresql:
  postgresql_data:
```
After Setup the SonarQube, you should get the following screen on http://{yourinstanceip}:9000 

## INTEGRATION OF SONARQUBE WITH JENKINS:
Install suitable plugins for the Sonarqube server and scanner.

Restart the Jenkins server.

You will find the token in the SonarQube Settings.

Then you need to update the SonarQube Scanner in Jenkins Tools.

Once you Configure these things, you can create the pipeline for the SonarQube Integration with Jenkins.

## REFERENCE

You can refer to this link for creating the pipeline. https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/scanners/jenkins-extension-sonarqube/
