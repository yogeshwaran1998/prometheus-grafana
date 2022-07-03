# add prometheus Helm repo 
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# add grafana Helm repo
helm repo add grafana https://grafana.github.io/helm-charts

# Deploy Prometheus
kubectl create namespace prometheus
helm install prometheus prometheus-community/prometheus --namespace prometheus --set alertmanager.persistentVolume.storageClass="gp2" --set server.persistentVolume.storageClass="gp2"
	
# Prometheus components deployed as expected
kubectl get all -n prometheus


# kubectl port forwarding
kubectl port-forward -n prometheus deploy/prometheus-server 8080:9090

# Deploy Grafana using below command
kubectl create namespace grafana
helm install grafana grafana/grafana --namespace grafana --set persistence.storageClassName="gp2" --set persistence.enabled=true --set adminPassword='<actualpass>' --values ./grafana.yaml --set service.type=LoadBalancer

# Check if Grafana is deployed
kubectl get all -n grafana

# Get Grafana ELB URL using this command
kubectl get svc -n grafana grafana -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'

# Access dashboard IDs
3119/6417