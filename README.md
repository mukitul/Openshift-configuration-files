# Openshift-configuration-files
### Openshift; How it works?
Platform as a service (PaaS) or application platform as a service (aPaaS) is a category of cloud computing services that provides a platform allowing customers to develop, run, and manage applications without the complexity of building and maintaining the infrastructure typically associated with developing and launching an app.
Openshift is Paas.
Openshift is a set of highly cohesive components like, API server, etcd, scheduler, master, worker, Kublet, controllers, container runtime engine.

When a user talk to the api server for a specific job like a pod creation, he actually does this through an API call (golang/python program/ restful call/kubectl). The API server write that info to the etcd, which is a distributed file system that stores data in key-value pair. 

Nothing had happened yet. That means no pod has been created. Openshift master has a component named scheduler. This scheduler follows the event (create pod event) and binds this pod to a node. Scheduler actually looks for perfect node for the deployment of this pod. 
When a scheduler bind a pod to a specific node, the kublet component of that node get informed that it must create a pod inside it’s node. Kublet uses a container runtime to do so. There are many container runtime, like docker, rocket. We can also use our very own runtime engine.

Now after creating pod to it’s node, kublet updates pod-states to etcd through API server.  

Master got another component called controller manager, which is actually a group of controllers.
Node Controller: Responsible for noticing and responding when nodes go down.
Replication Controller: Responsible for maintaining the correct number of pods for every replication controller object in the system.
Endpoints Controller: Populates the Endpoints object (that is, joins Services & Pods).
Service Account & Token Controllers: Create default accounts and API access tokens for new namespaces.
 
``` A deployment has three parts, deploymentConfig, route and service ```

DeploymentConfig contains information of pods template, how many replica of that template will be. Pods template contains information of container images and their claim names (volume info).

Route defines the domain name of a specific pod. Pods are ephemeral. Nodes can go down any time. Suppose, we got 3 nodes (worker) on our cluster. We have a deployment ‘CRM’ with a replication factor 3. These 3 replicas of pod have been instantiated inside 3 different nodes. Suddenly, one node got down. Node controller will update the state of the node and its pods to etcd through API server. Now in this case, replication controller will be informed that there must be 3 replicas of pod- ‘CRM’ running, but etcd saying, currently 2 of them are running. Replication Controller will inform scheduler. Scheduler will search for proper node to create instance of pod ‘CRM’. After creating pod instance, it will update pod state to etcd via API server again. 
When a user sends a request to a pod, service decides where to send the request as there can be multiple replica of that pod running. If a replica of a pod get destroyed and another replica get instantiated, service can use them or register them with label. 
