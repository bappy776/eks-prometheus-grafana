# On ESK cluster install prometheus & grafan

### Requirements
- Metrics Server
- Metrics with Prometheus
- Grafana
- Helm

### Metrics Server
- ```kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml``` - To install Metrics in EKS

### Metrics with Prometheus
- ```kubectl create namespace prometheus``` - Create seperate namerspace
- ```helm repo add prometheus-community https://prometheus-community.github.io/helm-charts```
- ```helm repo update```
- ```helm upgrade -i prometheus prometheus-community/prometheus \```
   ``` --namespace prometheus \```
   ``` --set alertmanager.persistentVolume.storageClass="gp2",server.persistentVolume.storageClass="gp2"```
   
### Grafana Install
- ```kubectl create namespace grafana```
- ```helm repo add grafana https://grafana.github.io/helm-charts```
- ```helm repo update'
- ```kubectl create namespace grafana
- ```helm install grafana grafana/grafana \```
    ```--namespace grafana \```
    ```--set persistence.storageClassName="gp2" \```
    ```--set persistence.enabled=true \```
    ```--set adminPassword='EKS!sAWSome' \```
    ```--set datasources."datasources\.yaml".apiVersion=1 \```
    ```--set datasources."datasources\.yaml".datasources[0].name=Prometheus \```
    ```--set datasources."datasources\.yaml".datasources[0].type=prometheus \```
    ```--set datasources."datasources\.yaml".datasources[0].url=http://prometheus-server.prometheus.svc.cluster.local \```
    ```--set datasources."datasources\.yaml".datasources[0].access=proxy \```
    ```--set datasources."datasources\.yaml".datasources[0].isDefault=true \```
    ```--set service.type=LoadBalancer```
- ```export ELB=$(kubectl get svc -n grafana grafana -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')``` - Get Grafana ELB url
- ```echo "http://$ELB"```
- ```kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo``` - To get Grafana web url
