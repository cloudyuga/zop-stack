# ZOP Demo

## Installation

kubectl create configmap prometheus --from-file=config.yaml --namespace=kube-system

cd services
kubectl create -f zipkin.yaml
kubectl create -f prometheus.yaml
kubectl create -f backend.yaml
kubectl create -f frontend.yaml

cd replicasets
kubectl create -f zipkin.yaml
kubectl create -f prometheus.yaml
kubectl create -f backend.yaml
kubectl create -f frontend.yaml

backend:
./build.sh
docker build -t dvonthenen/backend .
docker push dvonthenen/backend:latest

frontend:
./build.sh
docker build -t dvonthenen/frontend .
docker push dvonthenen/frontend:latest


docker ps --filter "status=exited" | awk '{print $1}' | xargs docker rm




# Teardown
kubectl delete service frontend
kubectl delete service backend
kubectl delete service prometheus --namespace=kube-system
kubectl delete service zipkin --namespace=kube-system
kubectl delete replicaset frontend
kubectl delete replicaset backend
kubectl delete replicaset prometheus --namespace=kube-system
kubectl delete replicaset zipkin --namespace=kube-system
kubectl delete configmap prometheus --namespace=kube-system
