# Kubernetes Example Commands

This project covers the notes provided in
* [YouTube / Janakiram & Associates](https://www.youtube.com/watch?v=BadzJOlSn24) - Tips and Tricks of Using Kubectl, the Kubernetes CLI


### Notes

The following is an example list of kubectl commands

```
# Cordoning Node
kubectl get nodes
kubectl get pods -o wide

kubectl scale --replica=5 rs/web
kubectl get pods -o wide

kubectl cordon 172.17.4.201
kubectl scale --replica=10 rs/web
kubectl get pods -o wide

## Disable Scheduling on node (ex. 172.17.4.201)
kubectl uncordon 172.17.4.201
kubectl scale --replicas=20 rs/web
kubectl get pods -o wide
kubectl scale --replicas=5 rs/web

# Draining Nodes (req. uncordon)
kubectl get pods -o wide
## WARN: DRAIN KILLS Workload -OR Move workload gracefully to another node (applies to pods which are part of a REPLICASET)
## [[HOW TO ADD POD TO REPLICASET]]
kubectl drain 172.17.4.201 --force
kubectl get pods -o wide

## Bring it back
## Enable Scheduling on node (ex. 172.17.4.201)
kubectl uncordon 172.17.4.201
## Recreate db-pod and web-pod (Manually as they were not part of replica set)
kubectl create -f db-pod.yml
kubectl create -f web-pod.yml
kubectl scale --replicas=10 rs/web
kubectl pods -o wide

## Delete Pods
kubectl delete -f db-pod.yml -f web-pod.yml -f web-rs.yml

# Watching Pod Status (Standby/Live Monitor)
kubectl get pods --watch-only
## Re-create Pods
kubectl create -f db-pod.yml -f web-pod.yml -f web-rs.yml
## In Seperate Terminal: Initiate Scale/Replication
## MONITOR: Pod replication stages: Pending -> ContainerCreating -> Running
kubectl scale --replicas 20 rs/web
kubectl scale --replicas 5 rs/web
## Delete Replica Set
cd Demo/App/
kubectl delete -f web-rs.yml

# Port Forwarding
## Show Services, IPs, Ports (PRIV:PUB/PROTO)
kubectl get svc
kubectl get svc web
## Export Port (PRIV:PUB)
kubectl port-forward web 3000:3000
kubectl create -f web-service.yml

## Access shell inside Container
kubectl exec -it web /bin/sh

#Copying Files from Host to Container
kubectl exec -it web /bin/sh
cd public
kubectl cp ./test.html web:/usr/src/app/public/test.html

# Explain Objects (Show Commands & Aliases)
kubectl explain
## shortcut for pods
kubectl explain po
## shortcut for sercices
kubectl explain svc

# Format Output (YAML Definition)
kubectl get pod web -o=yaml
## Review/modify pod definition
kubectl get pod web -o=json

# List Containers in a Pod
kubectl get pods web -o jsonpath={.spec.containers[*].name}

# Sort by Name
kubectl get services --sort-by=.metadata.name

# List Pods/Nodes
kubectl get pod -o wide | awk {'print $1" " $7'}

# Edit Objects
kubectl edit pod/web
KUBE_EDITOR="sublime" kubectl edit pod/web

# Proxy
kubectl proxy --port=8000
firefox http://localhost:8000/ui
curl http://localhost:8000/api
curl -s http://localhost:8000/api/v1/nodes | jq '.items[] .metadata.labels'

# List exposed APIs
kubectl api-versions

# List API 
kubectl get nodes
## Use API to manage Kubernetes Operation (ex. Pods/Services)
curl -s http://localhost:8000/api/v1/namespaces/default/pods -XPOST -H 'Content-Type: application/json' -d@nginx-pod.json | jq '.status'
curl -s http://localhost:8000/api/v1/namespaces/default/services -XPOST -H 'Content-Type: application/json' -d@nginx-pod.json | jq '.spec.clusterIP'
curl http://localhost:8000/api/v1/namespaces/default/services/nginx-service -XDELETE
curl -s http://localhost:8000/api/v1/namespaces/default/pods -XDELETE
kubectl get pods
```