# udagram-microservice
This is the Udacity project to split the udagram application in microservice and deploy it in Kubernetes cluster in AWS.


Install the below items:

#### 1. eksctl
#### 2. docker
#### 3. docker-compose

### Step 01
After decomposing the services, please run the following command to make the docker images and push the images to registry (docker hub).

#### docker-compose -f docker-compose-build.yaml build
#### docker-compose -f docker-compose-build.yaml push

Check the images in docker hub. We should see something like below:

<img width="807" alt="docker-hub-images" src="https://user-images.githubusercontent.com/21278048/76054670-a7529c00-5f9b-11ea-8b24-8fa0f0385b9c.PNG">

In case you want to use my images, you can find those here:

[frontend](https://hub.docker.com/repository/docker/talismanic/udacity-frontend)

[feed service](https://hub.docker.com/repository/docker/talismanic/udacity-restapi-feed)

[user service](https://hub.docker.com/repository/docker/talismanic/udacity-restapi-user)

[reverseproxy](https://hub.docker.com/repository/docker/talismanic/reverseproxy)



### Step 02
Now to spin up the cluster run the following command:

#### eksctl create cluster --name MyClusterName --version 1.14 --nodegroup-name standard-workers --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 4 --node-ami auto


### Step 03
Change the type of the services of Frontend and Reverseproxy to Loadbalancer in the yaml definition of the services.

Run below command to spin the application:
#### kubectl apply -f .

If everything is alright, then run the following command to see all the resources:
#### kubectl get all

Above command should bring the resoucres like below
<img width="930" alt="udagram-kubectl-get-all" src="https://user-images.githubusercontent.com/21278048/76054353-99e8e200-5f9a-11ea-81fd-31236ebe50cf.PNG">

### Step 04
As the frontend and reverseproxy services has been added as LoadBalancer, you will see that AWS has attached public interface in the external load balancer field. Other two services are running as the ClusterIP type.  

Now you need to copy the reverse proxy EXTERNAL_IP and change the source code of the front end service to use this IP as backend instead of localhost. Note that, to access the feed and user service from frontend service from browser, you need to add CORS header in the nginx.conf file. Details changes are inside the repo. Rebuild the docker image for frontend service and reverse proxy. The update the kubernetes deployments applying kubectl apply command similar to step 3.

### Step 04
To check whether the application is accessible, please copy the EXTERNAL-IP of frontend service and open it in browser with port 8100. This should bring the frontend like below:

<img width="940" alt="udagram-running-browser" src="https://user-images.githubusercontent.com/21278048/76054539-38754300-5f9b-11ea-95c2-7a2a71879b33.PNG">

### Step 05
#### Integrating with Travis-CI
Connect your Github Repo with TravisCI. Make the changes mentioned in the .travis.yml file. Make any change in the code base and push it to Repo. This should automatically trigger the build and after successful build you should see something like below:

<img width="856" alt="travis-ci-build" src="https://user-images.githubusercontent.com/21278048/76055049-c30a7200-5f9c-11ea-82da-06478afc610e.PNG">



### Step 06
When you have collected all your artifacts required to submit the assignment, do not forget to clear the cluster to avoid additional charging. Please use below command to clear this.

#### eksctl delete cluster --name=MyClusterName
