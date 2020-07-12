# amazon-eks-TASK 1

# TASK OBJECTIVES -
  * Create Kubernetes Cluster using AWS EKS.
  * Integrate EKS with EC2,EBS,LB,EFS.
  * Deploying Wordpress & MySQL Multi-tier Architecture on the top of AWS EKS.
  * Using Helm Command : install prometheus and grafana.
  * Create Farget Cluster (Server less Architecture).
 
  With Amazon EKS, the Kubernetes control plane — including the backend persistence layer and the API servers — is provisioned and scaled across various AWS availability zones, resulting in high availability and eliminating a single point of failure. Unhealthy control plane nodes are detected and replaced, and patching is provided for the control plane. The result is a resilient AWS-managed Kubernetes cluster that can withstand even the loss of an availability zone.
  EKS is integrated with various AWS services, making it easy for organizations to scale and secure applications seamlessly. From AWS Identity Access Management (IAM) for authentication to Elastic Load Balancing for load distribution, the straightforwardness and convenience factor of using EKS can’t be understated.
  So let’s begin the task with practicals, just so you are not bored. 
 
 # Step 1: First we have configure aws and install aws ,eksctl(why? see below) software :
 
  For connecting to aws EKS we have following ways such as webUI , CLI and terraform code also. Here we are using CLI but for connecting to aws EKS using CLI we required secret     key and access key and CLI also have two ways to connect to EKS
  * aws eks command : don’t have a lot of options for customization.
  * eksctl command : it is weave community slack command. It launches cluster for us and lots of customization can be done with this command.

 ![1](https://user-images.githubusercontent.com/51692515/87247386-2fc4da00-c471-11ea-8e2c-7bf5420d5f79.png)
 
 
 # Step 2: Create a cluster using script file in yaml format :
 
 ![2](https://user-images.githubusercontent.com/51692515/87247387-30f60700-c471-11ea-9a23-25ec778f2d9a.png)
 
 ** We can see eksctl program internally creating cloud formation stack for cluster automatically and we use cloud formation for auto provisioning.
 
 ![3](https://user-images.githubusercontent.com/51692515/87247381-2d628000-c471-11ea-9807-6091aab9b5ab.png)
 
 It takes a lot of time for creation of cluster approx. 15–20 min.
 
 ![4](https://user-images.githubusercontent.com/51692515/87247383-2e93ad00-c471-11ea-8056-50afcda080d1.png)
 
![5](https://user-images.githubusercontent.com/51692515/87247385-2f2c4380-c471-11ea-8cfd-e70197083baf.png)

# Step 3: Next for connecting to the master, kubectl cmd require config file (.kube) having IP , username and password. AWS automatically create this file for us by using cmd aws eks update-kubeconfig — name cluster.

![6](https://user-images.githubusercontent.com/51692515/87247500-cf826800-c471-11ea-9f24-93334b1e28f7.png)

So our nodes looks somewhat like this:

![7](https://user-images.githubusercontent.com/51692515/87247501-d0b39500-c471-11ea-8cf7-ddea8548ef1e.png)

# Step 4: Next we create a namespace — in my case I named“addy-ns” as a namespace by a command kubectl create namespace addy-ns and for setting created namespace as a default we have one command — — — kubectl config set-context — current — namespace=addy-ns :

![8](https://user-images.githubusercontent.com/51692515/87247503-d14c2b80-c471-11ea-9e91-b151de1ef9f4.png)

To get cluster info use cmd kubectl cluster-info

![9](https://user-images.githubusercontent.com/51692515/87247504-d1e4c200-c471-11ea-8bbc-2db7b16bd243.png)

# Step 5: Create a deployment, scale it a scale it and also expose pod to the outside world with service type load balancer at port no 80:

![10](https://user-images.githubusercontent.com/51692515/87247505-d27d5880-c471-11ea-9769-360f3d27b991.png)

![11](https://user-images.githubusercontent.com/51692515/87247506-d3ae8580-c471-11ea-9557-d10be8f60a51.png)

![12](https://user-images.githubusercontent.com/51692515/87247508-d4dfb280-c471-11ea-8e79-56154cdaa839.png)

Below you can see load balancer is created(as whenever page refreshed a new IPaddress is shown), anyone from outside world who know the DNS can access to webserver.





