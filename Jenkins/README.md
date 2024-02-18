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
## Configuration plugins
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
1. Go to Manage Jenkins > Managed files > add a new config > Check Global Maven settings.xml and Add ID > Provide credential for nexus server , the username and password referrer on [nexus](Nexus)
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2012-09-19.png?raw=true)
2. Configure pom.xml for Nexus connection
	- Go to source code and provide pom.xml pointing to Nexus server
		![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Jenkins/assets/Screenshot%20from%202024-02-18%2012-44-10.png?raw=true)

## Trivy Installation on Jenkins Documentation
1. Install required dependencies:
	```sh
	sudo apt-get install wget apt-transport-https gnupg lsb-release
	```
2. Add Trivy GPG key:
	```sh
	wget -qO - https://aquasecurity.github.io/trivyrepo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
	```
3. Add Trivy repository:
	```sh
	echo "deb [signed-by=/usr/share/keyrings/trivy.gpg]
	
	https://aquasecurity.github.io/trivy-repo/deb$(lsb_release -sc) main" |
	
	sudo tee -a /etc/apt/sources.list.d/trivy.list
	```
4. Update package list:
	```sh
	sudo apt-get update
	```
5. Install Trivy:
	```sh
	sudo apt-get install trivy -y
	```

## Create Pipeline
```sh 
pipeline {
    agent any

    tools{
        maven 'maven3' 
        jdk 'jdk17'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git checkout') {
            steps {
                git branch: 'main', url:'https://github.com/RakhaFe21/Ekart-maven.git'
            }
        }
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        stage('Unit test') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar'){
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=EKART -Dsonar.projectName=EKART \
                    -Dsonar.java.binaries=. '''
                }
            }
        }    
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: ' --scan ./',odcInstallation: 'DC' 
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }
        stage('Deploy to nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17',maven: 'maven3', mavenSettingsConfig: '', traceability: true) {sh "mvn deploy -DskipTests=true"}
            }
        }
        stage('Docker Build & tag Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-user', toolName:'docker') { sh "docker build -t rakhafe/ekart:latest -fdocker/Dockerfile ."
                }
            }
          }
        }
        stage('Trivy Scan') {
            steps {
                sh "trivy image rakhafe/ekart:latest > trivy-report.txt "
            }
        }
        stage('Docker push image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-user', toolName:'docker'){ sh "docker push rakhafe/ekart:latest"}
                }
            }
        }
        stage('Kubernetes Deploy') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.9.4:6443') 
                {
                sh "kubectl apply -f deploymentservice.yml -n webapps" 
                sh "kubectl get svc -n webapps"
                }
            }
        }
    }
  }

```

