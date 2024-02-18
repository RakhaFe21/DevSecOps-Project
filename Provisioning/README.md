# Provisioning


**Requirements**

-   Local machine with Terraform
-   AWS EC2 Instances
    -   Master server t2.medium
    -  Other servers t2.small

## Provisioning Servers
1. Create code main.tf for terraform and apply
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Provisioning/assets/Screenshot%20from%202024-02-18%2005-50-45.png?raw=true)
	```sh
	module "ec2_instance" {
	  source  = "terraform-aws-modules/ec2-instance/aws"

	  for_each = toset(["master", "Jenkins", "Sonarqube", "worker1", "worker2", "webserver"])

	  name = "instance-${each.key}"
	  instance_type          = "t2.micro"
	  key_name               = "terra"
	  monitoring             = true
	  vpc_security_group_ids = ["sg-07ca04774eee482c2"]
	  subnet_id              = "subnet-0a843b917a876cda6"
	  ami = "ami-0fa377108253bf620"

	  tags = {
	    Terraform   = "true"
	    Environment = "dev"
	  }
	}
	```
2. Result on AWS EC2
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Provisioning/assets/Screenshot%20from%202024-02-18%2005-47-51.png?raw=true)
## Setting Ports
1. Add security group for all servers with port :
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Provisioning/assets/Screenshot%20from%202024-02-18%2008-19-52.png?raw=true)
