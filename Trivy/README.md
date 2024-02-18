# Trivy
*This configuration running on server Jenkins with IP 54.151.149.151*

**Requirements**
- Docker engine

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
