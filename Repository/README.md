# Repository
*source code : https://github.com/RakhaFe21/Ekart-maven.git*

**Requirements**
- Dockerfile
- deployment.yml

## Create Dockerfile
```sh
FROM openjdk:8u151-jdk-alpine3.7

EXPOSE 8070

ENV APP_HOME /usr/src/app

COPY target/shopping-cart-0.0.1-SNAPSHOT.jar $APP_HOME/app.jar

WORKDIR $APP_HOME

ENTRYPOINT exec java -jar app.jar
```
## Create deployment.yml
```sh
apiVersion: apps/v1
kind: Deployment # Kubernetes resource kind we are creating
metadata:
  name: ekart-deployment
spec:
  selector:
    matchLabels:
      app: ekart
  replicas: 2 # Number of replicas that will be created for this deployment
  template:
    metadata:
      labels:
        app: ekart
    spec:
      containers:
        - name: ekart
          image: rakhafe/ekart:latest # Image that will be used to containers in the cluster
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8070 # The port that the container is running on in the cluster


---

apiVersion: v1 # Kubernetes API version
kind: Service # Kubernetes resource kind we are creating
metadata: # Metadata of the resource kind we are creating
  name: ekart-ssvc
spec:
  selector:
    app: ekart
  ports:
    - protocol: "TCP"
      port: 8070 # The port that the service is running on in the cluster
      targetPort: 8070 # The port exposed by the service
  type: NodePort # type of the service.
```
