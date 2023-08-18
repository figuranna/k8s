# Notes for Kubernetes
### Kubernetes and minikube training
Course: (https://www.udemy.com/course/kubernetes-microservices/)https://www.udemy.com/course/kubernetes-microservices/

### Kubernetes
### Containers
### Pods
### Services

### ReplicaSets
* Gives the pods new unique id (er pod/webapp-***spjtb***)
* kubectl get all
    * ![k8s_course](https://github.com/figuranna/k8s/assets/101461379/8c5dccac-df52-4aba-8482-b76c0d186494)
    * **desired:** how many we want running
    * **current:** number of containers that are running
    * **ready:** number of containers that are responding to requests

* Automatically creates a new pod if one crashes and stops
* The downside is that there is no instance for that pod, there'll be downtime
* There can be many replicas, we need to figure out how much replicas we need for a microservice
    * *The different replicas will be running on different computers (in our case it runs on the same)*

### Deployments
Similar to ReplicaSets, but a more sophisticated version. We get automatic rolling updates with 0 downtime. We can also do rollbacks if something goes wrong. There are some extra fields for tuning the rolling update, but it's optional. 
* *A deployment* creates a cooresponding *replicaset*
    * We created the deployment **->** the deployment creates a replicaset **->** the replicaset creates pods
* If we change the image that we use (er use a newer version) deployment creates a new replicaset for the new version
* The older version's replicaset is NOT deleted, it's still alive, but the replicas will be set to 0.
    * Command to access the older replicaset: 
# Kubernetes and minikube training
Course: https://www.udemy.com/course/kubernetes-microservices/)https://www.udemy.com/course/kubernetes-microservices/
