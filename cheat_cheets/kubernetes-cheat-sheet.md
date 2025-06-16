# kubectl

## get
get information about components

## get pods/deployments/services
get information about the collection of components especified in command

## get pod [name]
get information about a specific component
`kubectl get ...`

- get yaml config and save it to file
`kubectl get deployment nginx-deployment -o yaml > nginx-deployment-result.yaml`

## get [component] -A
- get all components no matter the namespace

## describe
get detailed information about a component

- example
`kubectl describe pod yaba-api-deployment-84b66cd58b-j6nhj`

## delete
kubectl delete -f nginx-deployment.yaml

## logs
`kubectl logs [pod-name]`

- Begin streaming the logs of the ruby container in pod web-1
`kubectl logs -f [pod-name]`
---
# minikube
ToDo
---

# Helm

## Helm Template

`helm template <release-name> <chart-path> > output.yaml`
helm template confluent-operator confluentinc/confluent-for-kubernetes > output.yaml
helm upgrade   
