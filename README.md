
# CAE-BACKSTAGE-MINIKUBE

## Minikube

minikube start  
minikube dashboard

## Backstage

npx @backstage/create-app@latest
Update app-config.production.yaml

database:
    client: pg
    connection:
      host: postgres
      port: 5432
      user: backstage
      password: hunter2

yarn build:backend --config ../../app-config.yaml
yarn tsc
yarn dev
yarn build-image --tag backstage:1.2.0
Cd backstage-yaml
kubectl apply -f backstage-yaml/backstage.yaml
sudo kubectl port-forward --namespace=backstage service/backstage 80:80
http://localhost

## Loki

helm repo add grafana https://grafana.github.io/helm-charts
Helm repo update
Create values.yaml for minio storage
helm install --values values.yaml loki grafana/loki

## Promtail as HelmChart (not recommended)

helm upgrade --install promtail grafana/promtail
kubectl apply -f Daemonset.yaml
kubectl --namespace default port-forward daemonset/promtail 3101
http://localhost:3101/targets
Got error:

## Grafana Dashboard

kubectl create namespace grafana  
kubectl apply -f grafana.yaml --namespace=grafana
kubectl port-forward service/grafana 3000:3000 --namespace=grafana

## Promtail as DaemonSet (TODO)

## Kubectl commands (mix)

kubectl config set-context --current --namespace=default
kubectl delete DaemonSet promtail-daemonset
helm upgrade --install --create-namespace --namespace it-observability-dev --values values.yaml loki grafana/loki
helm upgrade --install --create-namespace --namespace it-observability-dev --values values.yaml promtail grafana/promtail

Helm list -A (to see all installed with Helm)
helm uninstall promtail -n default scalable mode minio

## Loki as loki-stack (with Grafana)

kubectl create namespace loki-stack
kubectl config set-context --current --namespace=loki-stack 
helm upgrade --install loki --namespace=loki-stack grafana/loki-stack --set grafana.enabled=true

### Grafana with loki-stack

export POD_NAME=$(kubectl get pods --namespace loki-stack -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=loki" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace loki-stack port-forward $POD_NAME 3000

## Grafana working queries

sum(count_over_time({container="backstage"} |~ "(Error|FATAL)" [1h]))

sum(count_over_time({container="backstage"} |=`\"level\":\"info\"` [1h]))
