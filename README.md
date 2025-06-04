# Task5


Task 5: Build a Kubernetes Cluster Locally with Minikube
Objective:
To deploy and manage applications using Kubernetes locally, understanding core Kubernetes concepts such as deployments, services, scaling, and accessing apps using Minikube.

Tools Used:
Minikube: To create and run a local Kubernetes cluster.
kubectl: Kubernetes command-line tool for managing the cluster.
Docker: As the container runtime and Minikube driver inside WSL.
WSL2 (Windows Subsystem for Linux): Ubuntu environment on Windows to run Minikube and kubectl.


Step-by-Step Process:
--Step 1: Setting up the Kubernetes Cluster with Minikube
Installed Minikube in WSL2 and Docker Desktop on Windows.

Started Minikube using Docker driver:
#minikube start --driver=docker
Minikube created a single-node Kubernetes cluster inside a Docker container.

Verified the cluster status with:
#kubectl get nodes
Output confirmed the node minikube was in Ready state.

--Step 2: Creating a Deployment YAML file for the App
Created a Kubernetes Deployment manifest (deployment.yaml) to deploy an Nginx web server.

This deployment defined:
replicas: 2 (two pods)
The container image as nginx:latest
Container port 80 exposed inside pods
Sample snippet:

------------------------------------------------------------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
------------------------------------------------------------------------------------
Applied the deployment to the cluster:
#kubectl apply -f deployment.yaml

Confirmed pods were running with:
#kubectl get pods

--Step 3: Exposing the Deployment via a Service
Created a Service manifest (service.yaml) to expose the deployment using a NodePort service.

The service exposed port 80 internally and mapped to NodePort 30007.
Example:
------------------------------------------------------------------------------------
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
-----------------------------------------------------------------------------------
Applied the service manifest:
#kubectl apply -f service.yaml

Verified the service was created with:
#kubectl get services

--Step 4: Accessing the Application

Used the command to get the URL for accessing the service externally:
#minikube service nginx-service --url

Due to Minikube running inside Docker on Linux (WSL), this command sets up a port forwarding tunnel.
It returned a localhost URL with a high port number (e.g., http://127.0.0.1:33573).
The terminal running this command needed to remain open to keep the tunnel active.
Accessed the URL in a browser and saw the Nginx welcome page, confirming successful deployment.

--Step 5: Scaling the Deployment
Scaled the Nginx deployment from 2 pods to 4 pods:

kubectl scale deployment nginx-deployment --replicas=4
Verified new pods started with:
#kubectl get pods

