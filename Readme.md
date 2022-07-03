# Prometheus and Grafana deployment on the EKS using Helm Charts

Before running the below commands, we need to make sure that kubectl is configured and is pointing to the desired EKS cluster 

aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name) 

The above command sets the region and the cluster name, both obtained from outputs.tf (available in the EKS cluster deployed repository)

Deployment of Prometheus and Grafana using helm charts is much simpler as compared to using multiple manifest yaml files.


# Steps to deploy Prometheus and Grafana on the EKS cluster.

1. Add prometheus Helm repo 

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

2. Add grafana Helm repo

helm repo add grafana https://grafana.github.io/helm-charts

3. Deploy Prometheus

kubectl create namespace prometheus

helm install prometheus prometheus-community/prometheus --namespace prometheus --set alertmanager.persistentVolume.storageClass="gp2" --set server.persistentVolume.storageClass="gp2"	

4. Deploy Grafana using below command

kubectl create namespace grafana

helm install grafana grafana/grafana --namespace grafana --set persistence.storageClassName="gp2" --set persistence.enabled=true --set adminPassword='<actualpass>' --values ./grafana.yaml --set service.type=LoadBalancer

5. Get Grafana ELB URL using this command to access the Grafana Dashboard

kubectl get svc -n grafana grafana -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'

6. Access dashboard IDs for Grafana
3119/6417