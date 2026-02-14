1. why kubernetes
- handles container orchestration auto scaling and downing
- handles failure of containers and automatically restart in case if any container goes down

2. terminology
pod - inside this your docker containerr runs, one pod one container generally
node - a machine or virtual machine. A node can have multiple pods
cluster - a group of nodes is cluster.

3. what is kind
kubernetes in docker, lets you run kubernetes in your local docker.

4. what is kubectl
command line interface to talk to kubernetes
few commands
kubectl get pods
kubectl get nodes
kubectl logs mypod
kubectl delete pod mypod

5. so download kubectl and kind on ubuntu or windows

6. use kind to create a cluster
kind create cluster --name ml-cluster

6.1 create a dockerfile no need to expose port here
then build the image docker build -t my-fastapi .

7. load image into kind 
kind load docker-image my-fastapi --name ml-cluster

8. Universal Structure of Kubernetes YAML
```yaml
apiVersion: ___ # kubernetes api version not your app version 
kind: ___  
metadata:
  ___
spec:
  ___
```

9. different type of kind
pod - Runs a container. used rarely
deployment - Runs pods & keeps them alive.
service - Exposes pods.
job - Runs & exits.
cronjob - Runs periodically.
configmap - non-sensitive configuration like Hyperparameters, flags, settings.
secret - Sensitive config Passwords, API keys.
StatefulSet - Stateful apps

10. create a deployment.yaml
```yml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fastapi-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fastapi # same
  template:
    metadata:
      labels:
        app: fastapi  # same
    spec:
      containers:
      - name: fastapi2
        image: my-fastapi
        imagePullPolicy: Never
        ports:
        - containerPort: 8000
```

11. apply this deployment
kubectl apply -f deployment.yaml

12. so you flow is like this create image load image to cluster, apply deployment

13. to restart a pod
kubectl delete pod -l app=fastapi

14. create a service.yaml
```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: fastapi-service
spec:
  selector:
    app: fastapi
  ports:
  - port: 80
    targetPort: 8000
  type: NodePort
```

15. kubectl apply -f service.yaml

16. kubectl get svc

17. flow of ports
FastAPI App → 8000
Pod → 8000
Service targetPort → 8000
Service port → 80
NodePort → 31002 # it picks randomly

18. to run this application use
kubectl port-forward svc/fastapi-service 8000:80
with this you can run the app on 8000 using localhost

19. other wise
kubectl get nodes -o wide
use internal-ip from here
kubectl get services
get port from here

20. delete deployment and service 
kubectl delete deployment fastapi-deployment

21. kubectl get deployment

22. delete everything
delete deployment, service, cluster using kind

23. pods are ephemeral means they can die, and each pod have a unique ip, when a pod dies a new pod
is created which has a different ip. if you are using ip to communicate with pods this will be troublesome
so we use service and attach it to each pod, which has static ip, even if a pod dies and a new pod takes its
place its service ip remains same.

24. there is one more component of kuberenetes, ingress. it takes the request and forward it to service. ingress
open the port to outerworld using secure https protocol

25. minikube another way of using kubernetes locally
first install a hyperwiser then minikube 

26. kubectl describe pod pod-name

27. enter terminal of a pod
kubectl exec -it pod-name --bash

28. kubectl get pod -o wide

29. Labels – Key-value tags attached to objects
Selectors – Used to find matching objects
Namespace – Logical isolation inside cluster

30. deployment practice
resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "500m"

31. ClusterIP (default service type)
Explain:
ClusterIP → Internal only
NodePort → External via node IP
LoadBalancer → Cloud environments
service can have these above types

32. ✔ kubectl get events (SUPER useful)