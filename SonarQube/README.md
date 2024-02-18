# SonarQube 
*This configuration running on server SonarQube with IP 13.250.36.140*

**Requirements**
- Docker engine

## SonarQube Installation
1. Run the following command to start SonarQube in a Docker container: 
	```sh 
	docker run -d -p 9000:9000 sonarqube:lts-community 
	```
## SonarQube Setup Token
1. Go to Administrations > Security > Users > Create token	
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/SonarQube/assets/Screenshot%20from%202024-02-18%2008-55-25.png?raw=true)
2. Copy the token and paste into the credentials on [jenkins](../Jenkins)
