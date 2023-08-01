# Kubernetes-Cheat-Sheet

## Docker Images

### Create a basic Docker Image

```bash
# Use the official Ubuntu image as the base image
FROM ubuntu:latest

# Set environment variables (optional)
ENV MY_VAR=my_value

# Install any desired packages (example: installing curl)
RUN apt-get update && \
    apt-get install -y curl

# Add any other configurations or commands needed for your application

# Start your application or service (example: echoing a message)
CMD echo "Hello, this is my custom Ubuntu image!"
```

Build the image and push it to a registry:

```bash
docker build -t <DOCKER_IMAGE>:<TAG> .
docker login
docker tag <DOCKER_IMAGE> <DOCKER_HUB_USER>/<DOCKER-IMAGE>
docker push <DOCKER_HUB_USER>/<DOCKER-IMAGE>
```

## Create deployment

Create a kubernetes secret with the credentials to access the docker hub repository:
```bash
kubectl create secret docker-registry <DOCKER_HUB_SECRET_NAME> --docker-username=<DOCKER_HUB_USER> --docker-password=<DOCKER_HUB_PASSWORD> --docker-server=https://index.docker.io/v1/
```

Create a deployment.yaml file with the following configuration:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <DEPLOYMENT_NAME>
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: <DEPLOYMENT_NAME>
  template:
    metadata:
      labels:
        app: <DEPLOYMENT_NAME>
    spec:
      nodeName: <NODE NAME>
      containers:
        - name: <CONTAINER_NAME>
          image: <DOCKER_HUB_USER>/<DOCKER_IMAGE>:<TAG>
      imagePullSecrets:
        - name: <DOCKER_HUB_SECRET_NAME>
```
Create the deployment into the cluster:

```bash
kubectl apply -f deployment.yaml
kubectl get pods
```


