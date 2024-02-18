# Jenkins
*This configuration running on server Jenkins with IP 54.151.149.151*

**Requirements**
- Java Development Kit 17 / openjdk 17.0.9
- Docker engine

## Jenkins Installation
1. Follow the steps in the [official Jenkins installation documentation](https://www.jenkins.io/doc/book/installing/linux/) to install Jenkins on
your Linux machine.

## Install plugins Jenkins
1. Plugin installation requirements for this project, regardless of what jenkins recommends:
	-	SonarQube Scanner
	-	Nexus Artifact Uploader
	-	Docker
	-	Docker Pipeline
	-	docker-build-step
	-	Cloudbees Docker build adn publish
	-	OWASP Dependency-Check
	-	Eclipse Temurin Installer
	-	Config file provider
## Configuration 
1. Go to Manage jenkins > Tools > Add JDK
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2008-38-40.png?raw=true)
2. Go to Manage jenkins > Tools > SonarQube Scanner
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2008-41-57.png?raw=true)
3. Go to Manage jenkins > Tools > Maven Installation
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2008-43-49.png?raw=true)
4. Go to Manage jenkins > Tools > Dependency-Check Installation
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2008-44-28.png?raw=true)
5. Go to Manage jenkins > Tools > Docker Installation
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2008-45-02.png?raw=true)

## Add Credentials
1. Go to Manage Jenkins > Credentials > System > Global Credentials 
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2009-01-20.png?raw=true)

## SonarQube connection
1. Go to Manage Jenkins > System
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2009-05-53.png?raw=true)
	*Server URL = IP from sonar server with sonar port
	Server authentication token = Token that we have create on Credentials Settings*

## Nexus Connection
