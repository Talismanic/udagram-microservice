# udagram-microservice
This is the Udacity project to split the udagram application in microservice and deploy it in Kubernetes cluster in AWS.


Install the below items:

1. eksctl
2. docker-compose


To spin up the cluster run the following command:

eksctl create cluster --name talisudagram --version 1.14 --nodegroup-name standard-workers --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 4 --node-ami auto

Change the type of the services of Frontend and Reverseproxy to Loadbalancer.

Run below command to spin the application:
kubectl apply -f .

