# Kubernetes
*run all these commands below using root!*

**Requirements**
- Kubernetes
- Docker engine

## Kubernetes setup on both Master and Worker servers
1. Update and install Docker:
	```sh
	sudo apt-get update -y
	sudo apt-get install docker.io -y
	sudo service docker restart
	```
2. Add Kubernetes repository:
	```sh
	sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
	
	echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" >/etc/apt/sources.list.d/kubernetes.list
	
	sudo apt-get update
	```
3. Install Kubernetes components:
	```sh
	sudo apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
	```
## Kubernetes Setup on Master server
1. Initialize Kubernetes with a pod network CIDR:
	 ```sh
	 kubeadm init --pod-network-cidr=192.168.0.0/16
	 ```
2. Set up kubeconfig:
	```sh
	mkdir -p $HOME/.kube
	
	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	
	sudo chown $(id -u):$(id -g) $HOME/.kube/config
	```
3. Apply network plugins:
	```sh
	kubectl apply -f https://docs.projectcalico.org/v3.20/manifests/calico.yaml

	kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml
	```
## Kubernetes setup on worker servers
This command appear after kubernetes config init on master

```sh
kubeadm join 172.31.9.4:6443 --token uq7jww.9to6h173km3ezlxa     --discovery-token-ca-cert-hash sha256:3beb27b67d1f505bbff6b47b4b0f66ff53743757537cc9065936a902737d251c
```
## Create Service Account, Role, Bind Role, and Create token for service account

1. Creating Service Account
	`kubectl create namespace webapps`
	`nano sa.yaml`
	```sh
	apiVersion: v1
	kind: ServiceAccount
	metadata:
	  name: jenkins
	  namespace: webapps
	```
	`kubectl apply -f sa.yml`
2. Create Role

	`nano role.yml`
	```sh
	apiVersion: rbac.authorization.k8s.io/v1
	kind: Role
	metadata:
	  name: app-role
	  namespace: webapps
	rules:
	  - apiGroups:
	        - ""
	        - apps
	        - autoscaling
	        - batch
	        - extensions
	        - policy
	        - rbac.authorization.k8s.io
	    resources:
	      - pods
	      - componentstatuses
	      - configmaps
	      - daemonsets
	      - deployments
	      - events
	      - endpoints
	      - horizontalpodautoscalers
	      - ingress
	      - jobs
	      - limitranges
	      - namespaces
	      - nodes
	      - pods
	      - persistentvolumes
	      - persistentvolumeclaims
	      - resourcequotas
	      - replicasets
	      - replicationcontrollers
	      - serviceaccounts
	      - services
	    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
	```
	`kubectl apply -f role.yml`	
	
3. Bind the role to service account
	`nano assign.yml`
	```sh
	apiVersion: rbac.authorization.k8s.io/v1
	kind: RoleBinding
	metadata:
	  name: app-rolebinding
	  namespace: webapps 
	roleRef:
	  apiGroup: rbac.authorization.k8s.io
	  kind: Role
	  name: app-role 
	subjects:
	- namespace: webapps 
	  kind: ServiceAccount
	  name: jenkins 
	```
	`kubectl apply -f assign.yml`		
4. Generate [token](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#:~:text=To%20create%20a%20non-expiring,with%20that%20generated%20token%20data.) using service account in the namespace
`nano sec.yml`
	```sh
	apiVersion: v1
	kind: Secret
	type: kubernetes.io/service-account-token
	metadata:
	  name: mysecretname
	  annotations:
	    kubernetes.io/service-account.name: jenkins
	```
	`kubectl apply -f sec.yml -n webapps`	

	To get the token you can run:
	```sh
	kubectl -n webapps describe secret mysecretname
	```
	![enter image description here](https://github.com/RakhaFe21/DevSecOps-Project/blob/main/Kubernetes/assets/Screenshot%20from%202024-02-18%2021-00-48.png?raw=true)
	This token will be attached to the [jenkins](../Jenkins) credentials
