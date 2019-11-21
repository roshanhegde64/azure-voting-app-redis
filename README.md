## DevOps Engineer - Tech Test

### Instructions

We believe in `infrastructure as code` and automation of deployments.  We would like you to demonstrate your understanding and use of modern automated infrastructure and sysadmin. To demonstate this, your goal is to provision a simple web application of your choice that has a backend and database store (write a simple app or use an off-the-shelf set of Docker images).  Create an orchestrated cluster of nodes using any provisioning tool of your choice and deploy the application using an orchestration layer (e.g. Docker Swarm, Kubernetes, etc) to scale the application.  

## Solution:

I have used a simple UK voting app which is written in Python 3 and has a redis database to demonstrate above requirement. Please follow below step by step process to install, configure, deploy, test and scale the web application. 

Step 1: 

git clone https://github.com/roshanhegde64/azure-voting-app-redis.git

step 2:

docker-compose up -d

Step 3: Check if Docker image is created successfully

docker images

Step 4: You should be able to see two Docker images of redis and azure-vote-app

docker ps

Step 5: Check whether the app is running on local host.

http://localhost:8080/

Step 6: On Azure CLI - This will create a new resource group

az group create --name mydemo --location eastus

Step 7: Create Azure Container registory

az acr create --resource-group mydemo --name roshanhegde333 --sku Basic

Step 8: Please run this from your console as azure shell doesnt run docker daemon. If the azure cli is not installed then run the command brew update && brew install azure-cli for macbook

az acr login --name roshanhegde333

Step 9: Execute below command to tag your image

docker tag azure-vote-front roshanhegde333.azurecr.io/azure-vote-front:v1

Step 10: Push the docker image to Azure Container registry

docker push roshanhegde333.azurecr.io/azure-vote-front:v1

Step 11: Create Azure Kubernetes Cluster

az aks create \
    --resource-group mydemo \
    --name mycluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr roshanhegde333
	
Step 12: Ensure that you have Azure Kubernetes CLI running on your machine else run the command az aks install-cli

Step 13: Connect to cluster using kubectl

az aks get-credentials --resource-group mydemo --name mycluster

Step 14: Verify whether connected: You should see two nodes 

kubectl get nodes

Step 15: Ensure you use the updated fine name with acr server name to deploy the application.

Step 16: Deploy the application 

kubectl apply -f azure-vote-all-in-one-redis.yaml

Step 17: Test the application where we will get the external IP address 

kubectl get service azure-vote-front --watch

Step 18: Test the application with the IP generated with the above command to confirm whether deployment was successful.


NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.3.55    40.88.17.1    80:31329/TCP   17s

Step 19: In order to scale up the application use the below command. Currently scalling upto 4 pods. Check whether the pods are running using the kubectl get pods command.

kubectl scale --replicas=4 deployment/azure-vote-front

Step 20: In order to scale the nodes use below commands: Below command will scale up the nodes from 2 to 4


az aks scale --resource-group mydemo --name mycluster --node-count 4

Step 21: Make changes to the code and follow below steps to verify updated application and deployment. 
For webapp, making change to config_file.cfg.

Step 22: Once update made on code execute below step on console.

docker-compose up --build -d

Step 23: Test the application locally to verify the changes in the code.

http://localhost:8080


Step 24: Update the tag version to v2 for better understanding

docker tag azure-vote-front roshanhegde333.azurecr.io/azure-vote-front:v2

Step 25: Push the updated version to Azure Container registry 


docker push roshanhegde333.azurecr.io/azure-vote-front:v2


Step 26: Deploy the updated image using the below command

kubectl set image deployment azure-vote-front azure-vote-front=roshanhegde333.azurecr.io/azure-vote-front:v2

Step 27: Test the application using the IP. In case of my execution below is the IP

40.88.17.1













