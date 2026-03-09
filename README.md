# alloy

Grafana Alloy sends logs to Loki. It is the replacement of promtail. It is based on OpenTelemetry.

You can pull the chart from its official repo:
```bash
# get repo
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# search available versions
helm search repo grafana --versions | grep alloy | head -5
# install
helm upgrade --install alloy grafana/alloy -n monitoring --create-namespace
# or just pull
helm pull grafana/alloy --version 1.6.1 --untar --untardir charts
```
This repo contains a parent helm chart, and is initialized with: 
```bash
mkdir k8s-alloy
cd k8s-alloy
helm create helm
rm -rf helm/templates/*
```
Edit helm/Chart.yaml:
```Bash
apiVersion: v2
name: alloy
description: A Helm chart for Grafana Alloy on Kubernetes
type: application
version: 0.1.0 # version of the parent helm chart
appVersion: "v1.13.2" # version of alloy being deployed
dependencies:
  - name: alloy
    version: 1.6.1 # version of the helm chart for Grafana Alloy used
    repository: https://grafana.github.io/helm-charts
```
Run the dependency update which will download the Grafana Alloy helm chart in tgz format and place it in the charts sub-folder as a child helm chart. It will also create the Chart.lock file.
```Bash
cd helm
helm dependency update
cd charts
tar xvf *tgz
rm -f *tgz
```
Next, update the values.yaml file in the parent helm chart. It is best practice to only have those values in this values.yaml file that override the values in values.yaml in the child helm chart.

Test deploy the helm chart - from the helm folder:
```Bash
helm -n monitoring install alloy . -f values.yaml --create-namespace
```
