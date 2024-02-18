# Nexus
*This configuration running on server Jenkins with IP 54.151.149.151*

**Requirements**
- Docker engine

## Nexus Installation on top docker
1. Run the following command to start Nexus in a Docker container: 
	```sh
	docker run -d -p 8081:8081 sonatype/nexus3
	```
