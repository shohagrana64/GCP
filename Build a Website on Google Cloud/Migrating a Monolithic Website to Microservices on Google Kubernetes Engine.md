# GCP
 GCP badges practice

## Initialize
```
gcloud auth list
gcloud config list project
gcloud config set compute/zone us-west1-a

```

## Clone Git

```
cd ~
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd ~/monolith-to-microservices
./setup.sh
```

## Create a GKE cluster

```
gcloud services enable container.googleapis.com
gcloud container clusters create fancy-cluster --num-nodes 3 --machine-type=e2-standard-4
gcloud compute instances list
```
## Deploy the existing monolith

```
cd ~/monolith-to-microservices
./deploy-monolith.sh

kubectl get service monolith


```

## Migrate orders to a microservice

### Create a Docker container with Cloud Build
```
cd ~/monolith-to-microservices/microservices/src/orders
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/orders:1.0.0 .

```

### Deploy container to GKE

```
kubectl create deployment orders --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/orders:1.0.0

kubectl expose deployment orders --type=LoadBalancer --port 80 --target-port 8081

```

## Reconfigure the monolith

```
cd ~/monolith-to-microservices/react-app
nano .env.monolith

// change values to
REACT_APP_ORDERS_URL=http://<ORDERS_IP_ADDRESS>/api/orders
REACT_APP_PRODUCTS_URL=/service/products

npm run build:monolith


cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0 .

// deploy to gcp

kubectl set image deployment/monolith monolith=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0

```

## Migrate Products to microservice

### docker container 

```
cd ~/monolith-to-microservices/microservices/src/products
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/products:1.0.0 .
```

### deploy to gke

```
kubectl create deployment products --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/products:1.0.0
kubectl expose deployment products --type=LoadBalancer --port 80 --target-port 8082
```

### Reconfigure the monolith

```
cd ~/monolith-to-microservices/react-app
vi .env.monolith

REACT_APP_PRODUCTS_URL=http://<PRODUCTS_IP_ADDRESS>/api/products

npm run build:monolith

```

### deploy gke

```
kubectl set image deployment/monolith monolith=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:3.0.0
```

## Migrate frontend to microservice

### Create a new frontend microservice

```
cd ~/monolith-to-microservices/react-app
cp .env.monolith .env
npm run build

cd ~/monolith-to-microservices/microservices/src/frontend
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/frontend:1.0.0 .

kubectl create deployment frontend --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/frontend:1.0.0

kubectl expose deployment frontend --type=LoadBalancer --port 80 --target-port 8080

```