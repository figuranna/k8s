# Notes for Kubernetes
### Kubernetes and minikube training
Course: https://www.udemy.com/course/kubernetes-microservices/

### Kubernetes
Kubernetes is an open-source system for automating deployment and scaling of containerized applications. Mainly used along with Docker containers. It was originally created and used by Google, but currently it's maintained by the Cloud Native Computing Foundation (CNCF). It became the standard for container orchestration.

### Containers

### Clusters

### Pods

### Services
* Each service is given it's own private IP address that is only visible inside the Kubernetes cluster.

### ReplicaSets
* Gives the pods new unique id (er pod/webapp-***spjtb***)
* kubectl get all
    
    ![k8s_course](https://github.com/figuranna/k8s/assets/101461379/8c5dccac-df52-4aba-8482-b76c0d186494)
    * **desired:** how many we want running
    * **current:** number of containers that are running
    * **ready:** number of containers that are responding to requests

* Automatically creates a new pod if one crashes and stops
* The downside is that there is no instance for that pod, there'll be downtime
* There can be many replicas, we need to figure out how much replicas we need for a microservice
    * *The different replicas will be running on different computers (in our case it runs on the same)*

### Deployments
Similar to ReplicaSets, but a more sophisticated version. We get automatic rollouts with 0 downtime. We can also do rollbacks if something goes wrong. There are some extra fields for tuning the rolling update, but it's optional. 
* A deployment creates a cooresponding replicaset
    * We created the deployment **->** the deployment creates a replicaset **->** the replicaset creates pods
* If we change the image that we use (er use a newer version) deployment creates a new replicaset for the new version
* The older version's replicaset is NOT deleted, it's still alive, but its replicas will be set to 0.

**Rollouts and rollbacks**
Rollouts : rolling updates, meaning that the application is updated gradually, gracefully and with no downtime
Rollbacks : opposite of *rollouts*. 
* If we do a rollout or a rollback we create a new revision each time
    * We created a rollout (rev 3)
    
    ![k8s_course2](https://github.com/figuranna/k8s/assets/101461379/95d8893c-62bc-4aba-9faf-e4e23701c8ec)
    * We created a rollback (rev 4) from the 2nd revision
    
    ![k8s_course3](https://github.com/figuranna/k8s/assets/101461379/bc9828c8-ade2-48b4-8fa4-f2bac6724f61)

* Downside of *rollbacks*: The yaml file doesn't match the state of the live, running, kubernetes system. The yaml file **SHOULD** describe the state of it. Because of this, it's only for emergency situations.
* If there is a problem with an upgrade (er wrong image link), it won't switch to the new replicaset, it'll keep the previous one running and use that instead. This way no rollback is needed.


### Networking / Service discovery
* The application and the database should be in different **pods** and **services**
* Kubernetes maintains its own private DNS service (kube-dns)
    * Basically a database that contains key valuepairs. The keys are just labels and values are the IP addresses of those services.
* The application looks up the database's IP address in kube-dns service and then uses the IP address to access the database

**Namespaces**
* We can set up 2 namespaces
    * One for front-end (Web container ...)
    * Other one for back-end (User Service, Vehicle tracking ...)
* If we don't specify a namespace, we only see the resources in the default namespace
* Kubernetes also has it's own namespaces
* *kubectl get po -n kube-system* : shows us the "hidden" pods that are running under *kube-system* namespace.
* We should store the core pods in the default namespace and put the third-party pods in other namespaces.

**If you are using Cygwin**
    
Winpty : is a Windows software package providing an interface similar to a Unix pty-master for communicating with Windows console programs. It makes it much bearable to use *kubectl exec* and create MySQL databases

* We can access any service by its name (*)
    * nslookup *database* **->** shows the database created in the cluster (the IPs match)
* (*) It doesn't actually searches using only the name, in the resolv.conf file there is a search line where it lists the "extensions". If the name is not found then it tries appending the string with these "extensions".
* You can only look up a service using only its name when it's in the default namespace, otherwise you have to use the fully qualified domain name.

### Microservices
**Monolith (traditional architecture):**
* An entire system is deployed as a single unit (er. Java application would be a single war file)
    * The war file would probably fufill many business needs. (er. For a shopping site it would contain a cart, item, product, inventory, user, accessrules, page, catalog....) 
* These naturally get bigger overtime, so usually a global database is backing these applications
* There is a high chance that all of the business areas would be able to read and write into the database.
* Releases take a long time due to the building process being complicated

**Microservice (microservice architecture):**
* Basically building a system as of self-contained components, a system as a set of small services
* Each one will be responsible for only one business requirement if possible
* These microservices can function on their own
    * Can be deployed and developed on their own
* They're also developed in their own workspace and they deploy on their standalone hardware !!!
* In an ideal world the microservices would run on its oen private physical instance of a server, but due to this being expensive, so in practice they're deployed in their own containers.
* Coding one microservice will not have direct visibility in another one

*Comunication between microservices:*
* Microservices communicate through interfaces
    * Usually it's a REST Api, but there can also be messaging between them
* The interfaces should be minimised between microservices


*Two pizza rule:*
* Microservices should be very constrined in their size

*Databases in microservices:*
* Lots of projects depend on their integration databases, but for ms architectures these are a big NO
    * They contain many different business areas and any part of the system can read and write to this database (maybe even some other systems as well)
* So each mc contains its own data store