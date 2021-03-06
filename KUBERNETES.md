
https://en.wikipedia.org/wiki/Kubernetes
https://kubernetes.io/docs/concepts/architecture/cloud-controller/
https://kubernetes.io/docs/tutorials

# Building Blocks
## Pods
The basic scheduling unit in Kubernetes is called a "pod". It adds a higher level of abstraction to 
containerized components. A pod consists of one or more containers that are guaranteed to be co-located 
on the host machine and can share resources. Each pod in Kubernetes is assigned a unique (within the cluster) 
IP address, which allows applications to use ports without the risk of conflict. A pod can define a volume, 
such as a local disk directory or a network disk, and expose it to the containers in the pod. Pods can 
be managed manually through the Kubernetes API, or their management can be delegated to a controller.

## Controllers
A controller is a reconciliation loop that drives actual cluster state toward the desired cluster state.
It does this by managing a set of pods. One kind of controller is a "Replication Controller," which handles 
replication and scaling by running a specified number of copies of a pod across the cluster. It also handles 
creating replacement pods if the underlying node fails. Other controllers that are part of the core 
Kubernetes system include a "DaemonSet Controller" for running exactly one pod on every machine (or some 
subset of machines), and a "Job Controller" for running pods that run to completion, e.g. as part of a batch job.
The set of pods that a controller manages is determined by label selectors that are part of the controller’s definition.

## Services
A Kubernetes service is a set of pods that work together, such as one tier of a multi-tier application. The set of 
pods that constitute a service are defined by a label selector. Kubernetes provides service discovery and request 
routing by assigning a stable IP address and DNS name to the service, and load balances traffic in a round-robin 
manner to network connections of that IP address among the pods matching the selector (even as failures cause the 
pods to move from machine to machine). By default a service is exposed inside a cluster (e.g. back end pods 
might be grouped into a service, with requests from the front-end pods load-balanced among them), but a service 
can also be exposed outside a cluster (e.g. for clients to reach frontend pods).

#Architecture
Kubernetes follows the master-slave architecture. The components of Kubernetes can be divided into those 
that manage an individual node and those that are part of the control plane.

## Control Plane

**etcd** is a persistent, lightweight, distributed, key-value data store developed by CoreOS that reliably stores 
the configuration data of the cluster, representing the overall state of the cluster at any given point of time. 

The **API server** processes and validates REST requests and updates state of the API objects in etcd, thereby 
allowing clients to configure workloads and containers across Worker nodes.

The **scheduler** is the pluggable component that selects which node an unscheduled pod (the basic entity managed 
by the scheduler) should run on based on resource availability. Scheduler tracks resource utilization on each 
node to ensure that workload is not scheduled in excess of the available resources.

The **controller manager** is the process that the core Kubernetes controllers like DaemonSet Controller and 
Replication Controller run in. The controllers communicate with the API server to create, update and delete 
the resources they manage (pods, service endpoints, etc.)


# Node Management
* A **Kubernetes Node** also known as Worker or Minion is a machine where containers (workloads) are deployed. Every node 
in the cluster must run the container runtime (such as Docker), as well as the below-mentioned components, for 
communication with master for network configuration of these containers.
* **Kubelet** is responsible for the running state of each node (that is, ensuring that all containers on the node are healthy). 
It takes care of starting, stopping, and maintaining application containers (organized into pods) as directed by the 
control plane. Kubelet monitors the state of a pod and if not in the desired state, the pod will be redeployed to the 
same node. The node status is relayed every few seconds via heartbeat messages to the master. Once the master detects 
a node failure, the Replication Controller observes this state change and launches pods on other healthy nodes.
* The **Kube-proxy** is an implementation of a network proxy and a load balancer, and it supports the service abstraction 
along with other networking operation. It is responsible for routing traffic to the appropriate container based on 
IP and port number of the incoming request.
* **cAdvisor** is an agent that monitors and gathers resource usage and performance metrics such as CPU, memory, 
file and network usage of containers on each node.

## Kuberenetis Commands
[Hands-on Tutorial](https://www.katacoda.com/courses/kubernetes)

## Launch a Single Node Cluster
```ssh
//info
kubectl cluster-info
//all nodes that can be used to host our applications
kubectl get nodes 
//deploy container with the given image
kubectl run first-deployment --image=katacoda/docker-http-server --port=80 
//see container deployment status
kubectl get pods
//exposing the container using NodePort (dynamic port to a container)
kubectl expose deployment first-deployment --port=80 --type=NodePort
```

## Launch Multi-Node Cluster Using Kubeadm
```ssh
//init cluster, start a master
kubeadm init --token=102952.1a7dd4cc8d1f4cc5 --kubernetes-version v1.8.0
//join cluster from another machine
kubeadm join --token=102952.1a7dd4cc8d1f4cc5 172.17.0.35:6443
//export configuration to the users home directory and sets env. varibale for use with the CLI 
sudo cp /etc/kubernetes/admin.conf $HOME/
sudo chown $(id -u):$(id -g) $HOME/admin.conf
export KUBECONFIG=$HOME/admin.conf
//run on master, will see 2 nodes, the nodes will be in NotReady state
kubectl get nodes
//deploy Container Networking Interface (CNI, defines how different nodes communicate) - WeaveWorks; /opt/weave-kube - WeaveWorks  config file
kubectl apply -f /opt/weave-kube
//see WeaveWorks pods on the cluster
kubectl get pod -n kube-system
//deploy pod, always run on master
kubectl run http --image=katacoda/docker-http-server:latest --replicas=1
kubectl get pods
//once pod is running, you can see it on the deployment machine
docker ps | grep docker-http-server
``` 

## Launch a Multi-Tier Web Application Using Kubernetes and Docker. 
The Guestbook example application stores notes from guests in Redis via JavaScript API calls. 
Redis contains a master (for storage), and a replicated set of redis 'slaves'.
```ssh
//launch kubernetes cluster
//start redis controller
kubectl create -f redis-master-controller.yaml
//get redis controller, pods, docker processes
kubectl get rc
kubectl get pods
docker ps | grep redis
//create redis master service and see it
kubectl create -f redis-master-service.yaml
kubectl get services
kubectl describe services redis-master
//create redis slave controller and see now both master and slave controllers
kubectl create -f redis-slave-controller.yaml
kubectl get rc
//create redis slave service and see both master and slave services
kubectl create -f redis-slave-service.yaml
kubectl get services
//delopy the web application: create controller and service
kubectl create -f frontend-controller.yaml
kubectl create -f frontend-service.yaml
//find node port 
kubectl describe service frontend | grep NodePort
//view web app URL
```

## Deploy Containers Using Kubectl
```ssh
//launch cluster
launch.sh
//launch deployment, this does several things:
//1. searched for a suitable node where an instance of the application could be run (we have only 1 available node)
//2. scheduled the application to run on that Node
//3. configured the cluster to reschedule the instance on a new Node when needed
kubectl run http --image=katacoda/docker-http-server:latest --replicas=1
//view the deployment
kubectl get deployments
kubectl describe deployment http
// we can use kubectl to create a service which exposes the Pods on a particular port.
kubectl expose deployment http --external-ip="172.17.0.63" --port=8000 --target-port=80
//Under the covers, this exposes the Pod via Docker Port Mapping. Now we can access the deployment
curl http://172.17.0.63:8000
response: <h1>This request was processed by host: http-2821759659-g4b7s</h1>
//and see the services
kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
http         ClusterIP   10.0.0.21    172.17.0.63   8000/TCP   2m
kubernetes   ClusterIP   10.0.0.1     <none>        443/TCP    7m
//see details of http service
 kubectl describe svc/http
//run and expose port using one command
kubectl run httpexposed --image=katacoda/docker-http-server:latest --replicas=1 --port=80 --hostport=8001
//scale containers - launch replica pods; once each Pod starts it will be added to the load balancer service. 
by describing the service you can view the endpoint and the associated Pods which are included.
kubectl scale --replicas=3 deployment http
kubectl get pods
NAME                    READY     STATUS              RESTARTS   AGE
http-2821759659-g4b7s   1/1       Running             0          8m
http-2821759659-jddmd   0/1       ContainerCreating   0          4s
http-2821759659-nsd3z   0/1       ContainerCreating   0          4s
//now when accessing the service (curl http://172.17.0.63:8000) you'll be redirected to one of the resplicas.
```

# Networking Introduction: Cluster IP, Target Ports, NodePort, External IPs, Load Balancer

Cluster IP is the default approach when creating a Kubernetes Service. 
The service is allocated an internal IP that other components can use to access the pods.
Run descride a specific service cmd to see cluster ip and endpoints.
```ssh
export CLUSTER_IP=$(kubectl get services/[service name] -o go-template='{{(index .spec.clusterIP)}}')
echo CLUSTER_IP=$CLUSTER_IP
curl $CLUSTER_IP:80
```
Target ports allows us to separate the port the service is available on from the port the application 
is listening on. TargetPort is the Port which the application is configured to listen on. Port is 
how the application will be accessed from the outside.

While TargetPort and ClusterIP make it available to inside the cluster, the NodePort exposes the 
service on each Node’s IP via the defined static port. No matter which Node within the cluster 
is accessed, the service will be reachable based on the port number defined.

Another approach to making a service available outside of the cluster is via External IP addresses.

## Create Ingress Routing to a Cluster
Kubernetes have advanced networking capabilities that allow Pods and Services to communicate 
inside the cluster's network. An Ingress enables inbound connections to the cluster, 
allowing external traffic to reach the correct Pod. Ingress enables externally-reachable urls, 
load balance traffic, terminate SSL, offer name based virtual hosting for a Kubernetes cluster.
```ssh
cat ingress-rules.yaml
- host: my.kubernetes.example
  http:
    paths:
    - path: /webapp1
      backend:
        serviceName: webapp1-svc
        servicePort: 80
    - path: /webapp2
      backend:
        serviceName: webapp2-svc
        servicePort: 80
    - backend:
        serviceName: webapp3-svc
        servicePort: 80
		
kubectl create -f ingress-rules.yaml	
Accessing a specific service:
curl -H "Host: my.kubernetes.example" 172.17.0.25/webapp1	
curl -H "Host: my.kubernetes.example" 172.17.0.25/webapp2
curl -H "Host: my.kubernetes.example" 172.17.0.25 //will be processed by webapps3
```

# Running Stateful Services on Kubernetes
NFS is a protocol that allows nodes to read/write data over a network. The protocol works by 
having a master node running the NFS daemon and stores the data. This master node makes certain 
directories available over the network.

Clients access the masters shared via drive mounts. From the viewpoint of applications, 
they are writing to the local disk. Under the covers, the NFS protocol writes it to 
the master.
```ssh
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <friendly-name>
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Recycle
  nfs: //definning persistent volumes
    server: <server-name>
    path: <shared-path>

kubectl get pv//see persistent volumes
```
Once a Persistent Volume is available, applications can claim the volume for their use. 
The claim is designed to stop applications accidentally writing to the same volume and 
causing conflicts and data corruption. The claim specifies the requirements for a volume. 
This includes read/write access and storage space required. An example is as follows:
```ssh
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: claim-mysql
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi

kubectl get pvc
NAME          STATUS    VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-http    Bound     nfs-0001   2Gi        RWO,RWX                       9s
claim-mysql   Bound     nfs-0002   5Gi        RWO,RWX                       15s	  
```
When a deployment is defined, it can assign itself to a previous claim. The following snippet 
defines a volume mount for the directory /var/lib/mysql/data which is mapped to the storage 
mysql-persistent-storage. The storage called mysql-persistent-storage is mapped to the 
claim called claim-mysql.
```ssh
  spec:
      volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql/data
  volumes:
    - name: mysql-persistent-storage
      persistentVolumeClaim:
        claimName: claim-mysql
```
Our Pods can now read/write. MySQL will store all database changes to the NFS Server 
while the HTTP Server will serve static from the NFS drive. When upgrading, restarting 
or moving containers to a different machine the data will still be accessible.		
```ssh
docker exec -it nfs-server bash -c "echo 'Hello World' > /exports/data-0001/index.html" //write data to NFS
ip=$(kubectl get pod www -o yaml |grep podIP | awk '{split($0,a,":"); print a[2]}'); echo $ip
curl $ip //read data from NFS
```
Pods can  be deleted and recreated, and they will still have an access to the stored data as the data 
is stored in NFS.	


## Operations on Pods
Pods are running in an isolated, private network - so we need to proxy access to them so we can debug and interact with them. To do this, we'll use the command to run a proxy in a second terminal window. 
```ssh
kubectl proxy 
```

We can execute commands directly on the container once the Pod is up and running. For this, we use the exec command and use the name of the Pod as a parameter.
```ssh
kubectl exec $POD_NAME env 
```
Open bash terminal for a pod and execute commands:
```ssh
kubectl exec -ti $POD_NAME bash
```

## Using a Service to Expose Your App
(https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/)

## Use Kubernetes to Manage Secrets	
```ssh
//create the Base64 strings and store them as variables to use in a yaml file
username=$(echo -n "admin" | base64)
password=$(echo -n "a62fjbd37942dcs" | base64)
echo "apiVersion: v1
kind: Secret
metadata:
  name: test-secret
type: Opaque
data:
  username: $username
  password: $password" >> secret.yaml
//create a secret  
kubectl create -f secret.yaml  
kubectl get secrets //see secrets
NAME                  TYPE                                  DATA      AGE
default-token-77v7n   kubernetes.io/service-account-token   3         3m
test-secret           Opaque                                2         2m
```

Consume secrets via evironment variables:
```ssh
secret-env.yaml:
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
    - name: mycontainer
      image: alpine:latest
      command: ["sleep", "9999"]
      env:
        - name: SECRET_USERNAME
          valueFrom:
            secretKeyRef:
              name: test-secret
              key: username
        - name: SECRET_PASSWORD
          valueFrom:
            secretKeyRef:
              name: test-secret
              key: password
  restartPolicy: Never
  
kubectl create -f secret-env.yaml //launch pod  
kubectl exec -it secret-env-pod env | grep SECRET_//see secrets,
//Kubernetes decodes the base64 value when populating the environment variables. 
//You should see the original username/password combination we defined. These 
//variables can now be used for accessing APIs, Databases etc.
SECRET_USERNAME=admin
SECRET_PASSWORD=a62fjbd37942dcs
```

The use of environment variables for storing secrets in memory can result 
in them accidentally leaking. The recommend approach is to use mount them as a Volume.
```ssh
cat secret-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-vol-pod
spec:
  volumes:
  - name: secret-volume
    secret:
      secretName: test-secret
  containers:
    - name: test-container
      image: alpine:latest
      command: ["sleep", "9999"]
      volumeMounts:
          - name: secret-volume
            mountPath: /etc/secret-volume
			
kubectl create -f secret-pod.yaml //create secret pod			
//Once started you can interact with the mounted secrets. 
You can list all the secrets available as if they're regular data. 
>kubectl exec -it secret-vol-pod cat /etc/secret-volume/username
admin
  > kubectl exec -it secret-vol-pod cat /etc/secret-volume/username
admin
  > kubectl exec -it secret-vol-pod cat /etc/secret-volume/password
a62fjbd37942dcs
```

## Liveness and Readiness Healthchecks
Readiness Probes checks if an application is ready to start processing traffic. This probe 
solves the problem of the container having started, but the process still warming up and 
configuring itself meaning it's not ready to receive traffic. Liveness Probes ensure that 
the application is healthy and capable of processing requests.

Based on the configuration, Kubernetes will execute the Liveness Probe. 
If the Probe fails, Kubernetes will destroy and re-create the failed container. 
```ssh
//see pod status
kubectl get pods --selector="name=frontend"
```

## Deploy Docker Compose with Kompose
Kompose takes existing Docker Compose files and enables them to be deployed onto Kubernetes. 
Compose is a tool for defining and running multi-container Docker applications. With Compose, 
you use a Compose file to configure your application’s services.
Configuration is in docker-compose.yml file.
```ssh
kompose up //run komposite with docker-compose.yml file
kubectl get deployment,svc,pods,pvc //see what has been deployed
```
Kompose also has the ability to take existing Compose files and generate the related 
Kubernetes Manifest files. The command "kompose convert will" generate the files.

Kompose also supports different Kubernetes distributions, for example OpenShift.
```ssh
kompose --provider openshift convert
```

By default, Kompose generates YAML files. It's possible to generate JSON based files by 
specifying the -j parameter.
```ssh
kompose convert -j
```

## Helm Package Manager

This scenario teaches you how to use Helm, the package manager for Kubernetes, to deploy Redis. 
Helm simplifies discovering and deploying services to a Kubernetes cluster.
```ssh
//install and init helm
curl -LO https://storage.googleapis.com/kubernetes-helm/helm-v2.6.1-linux-amd64.tar.gz
tar -xvf helm-v2.6.1-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/
helm init
helm repo update

//find app chart/configuration helm repo, app in this case is redis
helm search redis
//inspect chart/configuration
helm inspect stable/redis
helm install stable/redis //install redis
helm ls //see the helm installations 
//see results:
kubectl get pods
kubectl get deploy
kubectl get svc
```

## Installing Weave Scope on Kubernetes
Weave Scope lets you monitor and control your containerized microservices applications. 
By providing a visual map of your Docker Containers, you can see the dependencies and 
communication links between them. Scope automatically detects processes, containers, hosts.
```ssh
//get configuration
curl -L https://cloud.weave.works/launch/k8s/weavescope.yaml
//deploy scope
kubectl create -f 'https://cloud.weave.works/launch/k8s/weavescope.yaml'
//see pods
kubectl get pods -n weave
//create service which exposes the port (extrnal-ip paramter)
//Exposing the service to on a public IP is not recommended. Instead, it should require a VPN connection to access.
pod=$(kubectl get pod -n weave --selector=name=weave-scope-app -o jsonpath={.items..metadata.name})
kubectl expose pod $pod -n weave --external-ip="172.17.0.151" --port=4040 --target-port=4040 
//now you can see deployment details in a browser
```

## Getting Started with Kubeless
With Kubeless you can deploy functions without the need to build containers. 
These functions can be called via regular HTTP(S) calls or triggered by events 
submitted to message brokers like Kafka.
Kubeless aims to be an open source FaaS solution to clone the functionalities of 
AWS Lamdba/Goole Cloud Functions.
```ssh
kubectl create ns kubeless //create kubeless namespace
//install kubeless
curl -sL https://github.com/kubeless/kubeless/releases/download/v0.3.0/kubeless-rbac-v0.3.0.yaml | kubectl create -f 
kubectl get pods -n kubeless
//deploy a function
kubeless function deploy toy --runtime python2.7 \ //language runtime
                              --handler toy.handler \ //name of the function handler
                              --from-file toy.py \//file with the function
                              --trigger-http //how the function is triggered
//see all the functions
kubeless function ls
//Under the hood, Kubeless automatically created a Kubernetes deployment and service. 
//You can check that a Pod containing your function is running:							  
kubectl get pods
//call the function with params
kubeless function call toy --data '{"hello":"world"}'
//run proxy and call the function using proxy
kubectl proxy --port 8080 &
curl --data '{"hello":"world"}' localhost:8080/api/v1/proxy/namespaces/default/services/toy:8080/ --header "Content-Type:application/json"
//view function log
kubeless function logs toy
//view function description
kubeless function describe toy
//update function to use source code from another file
kubeless function update toy --from-file toy-update.py
//delete the function
kubeless function delete toy
```

## Helm Chart: Introduction
https://docs.bitnami.com/kubernetes/how-to/create-your-first-helm-chart/
