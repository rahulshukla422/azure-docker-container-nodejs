# azure-docker-container-nodejs

Below are the steps for building the node js application and publishing into azure container registry. And deploying the appplication in azure docker container.

Step 1: Create a docker file without extension

Step 2: Add the below details for your application

	FROM node:8.9.3-alpine
	RUN mkdir -p /usr/src/app
	COPY ./app/* /usr/src/app/
	WORKDIR /usr/src/app
	RUN npm install
	CMD node /usr/src/app/index.js

Step 3: Run the docker build command 

	docker build -t hello-world-docker .

Step 4: Docker run command to  run the application locally

	docker run -d -p 8080:80 hello-world-docker

Step 5: Now create the azure container registry 
	
	# create the resource group
	az group create --name rgdemo --location eastus
	
	# create the azure container registry
	az acr create --name acrdemo11 --resource-group rgdemo --sku basic
	
Step 6: Now create login to the azure container registry and tag the image to acr
	
	# login to azure container registry
	az acr login --name acrdemo11
	
	# tag the image to acr
	docker tag hello-world acrdemo11.azurecr.io/hello-world:v1
	
	# push docker image to azure container registry 
	docker push acrdemo11.azurecr.io/hello-world:v1

Step 8: Deploy the docker image in the azure containers 

	# Create the docker container with the registry image 
	az container create --name hello-world --resource-group rgdemo --image acrdemo11.azurecr.io/hello-world:v1 --cpu 1 --memory 1 --registry-login-server  acrdemo11.azurecr.io --registry-username acrdemo11 --registry-password gQzIIpL5hLR+rl88GMsceorUaku3XHYL --dns-name-label firstdockerapp --ports 80 443
	
Step 9: Now to get the fqdn details then run the below command and open in browser the url
	 
	# Get the fqdn url of the deployed container 
	az container show --name hello-world --resource-group rgdemo --query ipAddress.fqdn
	
Step 10: Open the url in the browser you application will run !!.