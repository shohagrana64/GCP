# GCP
 GCP badges practice

## Initialize
```
gcloud auth list
gcloud config list project
gcloud config set compute/zone us-central1-f

```

## Create GKE Cluster

```
gcloud services enable container.googleapis.com
gcloud container clusters create fancy-cluster --num-nodes 3
gcloud compute instances list
```

## Clone Repo

```
cd ~
git clone https://github.com/googlecodelabs/monolith-to-microservices.git

cd ~/monolith-to-microservices
./setup.sh

nvm install --lts

cd ~/monolith-to-microservices/monolith
npm start
```

## Create Docker Container with Cloud Build

```
gcloud services enable cloudbuild.googleapis.com

cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0 .


```

## Deploy container to GKE

```
kubectl create deployment monolith --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0

```

Optional k8s commands

```
# Show pods
kubectl get pods
# Show deployments
kubectl get deployments
# Show replica sets
kubectl get rs
#You can also combine them
kubectl get pods,deployments
```

## Expose GKE deployment

```
kubectl expose deployment monolith --type=LoadBalancer --port 80 --target-port 8080
kubectl get service
```

## Scale GKE deployment

```
kubectl scale deployment monolith --replicas=3
kubectl get all
```

## Make changes to the website

```
cd ~/monolith-to-microservices/react-app/src/pages/Home
mv index.js.new index.js

cat ~/monolith-to-microservices/react-app/src/pages/Home/index.js

cd ~/monolith-to-microservices/react-app
npm run build:monolith

cd ~/monolith-to-microservices/monolith

gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0 .
```

## Update website with zero downtime

```
kubectl set image deployment/monolith monolith=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0

```