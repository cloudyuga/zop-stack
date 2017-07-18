# ZOP Demo

## Installation

kubectl create configmap prometheus --from-file=config.yaml --namespace=kube-system

cd accounts
kubectl create -f default.yaml

cd services
kubectl create -f zipkin.yaml
kubectl create -f prometheus.yaml
kubectl create -f grafana.yaml
kubectl create -f backend.yaml
kubectl create -f frontend.yaml

cd deployments
kubectl create -f zipkin.yaml
kubectl create -f prometheus.yaml
kubectl create -f grafana.yaml
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

docker run -ti --entrypoint=/bin/sh -p 9090:9090 prom/prometheus:v1.7.1

/bin/prometheus -config.file=/etc/prometheus/prometheus.yml -storage.local.path=/prometheus -web.console.libraries=/etc/prometheus/console_libraries -web.console.templates=/etc/prometheus/consoles

IMPORTANT: Dont forget to fix ports on GCE!!!!!


# Teardown
kubectl delete deployment frontend
kubectl delete deployment backend
kubectl delete deployment grafana --namespace=kube-system
kubectl delete deployment prometheus --namespace=kube-system
kubectl delete deployment zipkin --namespace=kube-system
kubectl delete service frontend
kubectl delete service backend
kubectl delete service grafana --namespace=kube-system
kubectl delete service prometheus --namespace=kube-system
kubectl delete service zipkin --namespace=kube-system
kubectl delete serviceaccount default --namespace=kube-system
kubectl delete configmap prometheus --namespace=kube-system
