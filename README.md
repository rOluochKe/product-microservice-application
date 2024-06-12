# Product Microservice Application

## Setup

### Docker

cd <project>
eval $(minikube docker-env -u)
./gradlew build
docker-compose build

unset COMPOSE_FILE

docker-compose up -d mysql mongodb rabbitmq auth-server gateway

docker-compose up -d

docker-compose logs product-composite product review recommendation | grep ": Started"

USE_K8S=false HOST=localhost PORT=8443 HEALTH_URL=https://localhost:8443 ./test-em-all.bash

docker stats --no-stream

docker compose down

docker-compose up -d mysql mongodb rabbitmq auth-server gateway

ENTRYPOINT ["java", "-Dspring.aot.enabled=true", "org.springframework.boot.loader.JarLauncher"]

docker-compose build

docker-compose up -d

docker-compose logs product-composite product review recommendation | grep "Starting AOT-processed"

docker-compose logs product-composite product review recommendation | grep ": Started"

USE_K8S=false HOST=localhost PORT=8443 HEALTH_URL=https://localhost:8443 ./test-em-all.bash

docker compose down

### Kubernetes

kubernetes/helm/
└── environments
└── dev-env-native

eval $(minikube docker-env -u)
docker save hands-on/native-product-composite-service:latest -o native-product-composite.tar
docker save hands-on/native-product-service:latest -o native-product.tar
docker save hands-on/native-recommendation-service:latest -o native-recommendation.tar
docker save hands-on/native-review-service:latest -o native-review.tar

minikube start

minikube tunnel

eval $(minikube docker-env)
docker load -i native-product-composite.tar
docker load -i native-product.tar
docker load -i native-recommendation.tar
docker load -i native-review.tar

rm native-product-composite.tar native-product.tar native-recommendation.tar native-review.tar

docker-compose build auth-server

kubectl delete namespace hands-on
kubectl apply -f kubernetes/hands-on-namespace.yml
kubectl config set-context $(kubectl config current-context) --namespace=hands-on

for f in kubernetes/helm/components/*; do helm dep up $f; done

for f in kubernetes/helm/environments/*; do helm dep up $f; done

helm upgrade -install hands-on-dev-env-native \
kubernetes/helm/environments/dev-env-native \
-n hands-on --wait

./test-em-all.bash

Kubectl delete pod -l app=product-composite
kubectl logs -l app=product-composite --tail=-1 | grep ": Started"

kubectl get pods -o jsonpath="{.items[*].spec.containers[*].image}" | xargs -n1 | grep hands-on

## Tools

Windows Terminal: v1.16.10261.0
Visual Studio Code: v1.75.1
Docker Desktop for Windows: v4.14.0
Git: v2.34.1
Java: v17.0.6
curl: v7.81.0
jq: v1.6
Spring Boot CLI: v3.0.4
Siege: v4.0.7
Helm: v3.11.2
kubectl: v1.26.1
minikube: v 1.29.1
istioctl: v1.17.0
