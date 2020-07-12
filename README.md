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

![13](https://user-images.githubusercontent.com/51692515/87247496-cc877780-c471-11ea-8a98-90219c17f51f.png)

Below you can see load balancer is created(as whenever page refreshed a new IPaddress is shown), anyone from outside world who know the DNS can access to webserver.

![14](https://user-images.githubusercontent.com/51692515/87247498-ce513b00-c471-11ea-9b14-989283f29963.png)

![15](https://user-images.githubusercontent.com/51692515/87247499-cee9d180-c471-11ea-8f33-3c9f36ba6b54.png)

Update code — for this we have to write one command if one has a particular webpage to be deployedkubectl cp pagename podname:/var/www/html/index.phpor it can be done by manually going inside any pod, use commandkubectl exec -it pod name — bash then changing the index.html file at the location /var/www/html/

![16](https://user-images.githubusercontent.com/51692515/87247645-b928dc00-c472-11ea-80f7-2bcce9f84612.png)

# Step 6: Creating a PVC(PersistentVolumeClaim) using a yaml file. Execute command kubectl create -f pvc.yml

           apiVersion: v1
           kind: PersistentVolumeClaim
           metadata:
               name: lwpvc1
           spec:
              storageClassName: gp2
              accessModes:
                  - ReadWriteOnce
              resources:
                  requests:
                         storage: 10Gi
                         

![17](https://user-images.githubusercontent.com/51692515/87247646-b928dc00-c472-11ea-883b-c805b16e4705.png)

Here you can see my lwpvc1 is created but it is in pending status ,for bound status we have to mount that pvc to the pod , so for mounting I created one pod with script “mytestforpvc.yaml” , you can also mount it in the running container by using one command kubectl edit deployment

                            #for reference only
                            apiVersion: v1
                            kind: Pod
                            metadata:
                              name: mypod1
                              labels:
                                env: prod
                                dc: US
                            spec:
                              containers:
                              - name: "mycon1"
                                image: "vimal13/apache-webserver-php"
                                volumeMounts:
                                 - name: myvolforeks
                                   mountPath:  /var/www/html
                              volumes:
                              - name: myvolforeks
                                persistentVolumeClaim: 
                                  claimName: lwpvc1
                                  
        
![18](https://user-images.githubusercontent.com/51692515/87247647-b9c17280-c472-11ea-8188-4cfbdafdf95d.png)

The Pending status is now changed to Bound.

![19](https://user-images.githubusercontent.com/51692515/87247648-ba5a0900-c472-11ea-8645-69d88fdfe54a.png)

![20](https://user-images.githubusercontent.com/51692515/87247642-b75f1880-c472-11ea-9c34-5236fc2ba468.png)

## EKS
Spot Instances : Aws EC2 Spot Instances provides advantage of using EC2 instances which is in unused capacity in the AWS cloud. Spot Instances are available at up to a 90% discount compared to On-Demand prices.We can create spot Instances using a script in yaml format.
2. We have two ways to manage our k8s cluster :
* Managed by us : here we have own external k8s cluster , we can provide external load balancer providers etc.
* Managed by AWS EKS : here we have a guarantee that AWS manage our whole cluster and EKS behind the scene integrate with some of the services of aws like EC2, EBS,ELB,EFS,Cloudwatch etc . So we can say that EKS is internally linked or tightly coupled with these services.
3. If we have a requirement , we want our aws , also manage our worker nodes and whenever the requirements comes for vertical scaling (scale up and scale down) and horizontal scaling ( scale in and scale out ) . So we have to use serverless architecture in this kind of scenario. So aws has one service known as fargate which provide us serverless architecture but only for containers.
* Fargate manages everything . It creates slaves i.e worker nodes on run time.
* EKS uses fargate profile behind the scene which automatically provision the slave as per our demand.
4. If we are running with two different worker nodes suppose node 1 and node 2 and if we have two pods running in node 1 so they have internal connectivity but one pod is also running in node 2 . So if we have a requirement for external connectivity between node 1 pods and node 2 pod so we have to provide a Container Network Interface i.e CNI plugins such as flannel which is a overlay network.
* EKS internally use CNI and aws has its own CNI plugin i.e aws vpc CNI for k8s.
* AWS CNI works on Elastic Network Interface i.e ENI.
* Due to ENI network card we have to seen extra ip addresses which is known to be secondary IP’s
Note : * Limitation of pods ( Instance type) :
So it totally depends on network card and IP address. In t2.micro , In one instance we can’t add more than 4 NIC i.e 4 pods while in t2.small , in one instance we can’t badd more than 12 nic card i.e 12 pods.
5. Role : It provide us internal power for communication of one service with other but it doesn’t work outside the environment . Suppose we have to connect cloud formation with EC-2 then we can use role behind the scene. But whenever the requirement of connecting the client to the cluster there role fails. In this case we have to use Identity and access management (IAM).
6. Why we need EFS ..?
* If suddenly load increase, we have multiple of OS running in parallel i.e horizontal scaling and all the OS is configure with the Apache webserver. Per OS we attach EBS volume, suppose you have to copy the same content from one OS to other in this case the same EBS volume cannot be connected to the other OS. If developer change any code in one OS , they have to copy that code in other OS also. If you want your code or data persistent EBS won’t help you if sudden load increase because it only provides you file system.
* Then the role of EFS come in play.. they give you one centralized storage known as NFS :
• NFS stands for network file system , it is easily mount to the multiple OS over the network and if developer change any code in this storage , all the OS can easily access the update code.
• NFS is a protocol name . It gives you file system and create NFS server.
• NFS share files and objects over the network. Here you can easily edit the file.
7. Helm : in k8s we have a package manager or chart manager known as helm.
* Helm hub provide us kubernetes ready apps.
* Client always use helm command to install apps or packages.
* Helm also have one server known as tiller ( server side component of helm ).
* For initializing the helm we have to use “ helm init “ command.
* We can launch Jenkins, Prometheus , grafana in one single click using helm and these tools internally connected to the kubernetes cluster.

## Let’s move back to the practical hands-on.

Begin with creating a new cluster as “addycluster”

![21](https://user-images.githubusercontent.com/51692515/87247839-d5794880-c473-11ea-9b99-5857c4fa8725.png)

![22](https://user-images.githubusercontent.com/51692515/87247841-d611df00-c473-11ea-9f86-ce40a0fe53bc.png)

![23](https://user-images.githubusercontent.com/51692515/87247843-d6aa7580-c473-11ea-83f5-69b42f9d0922.png)

![24](https://user-images.githubusercontent.com/51692515/87247844-d7430c00-c473-11ea-82d7-5e2bb9c5d36f.png)

* for checking how many node groups are running you have to write one command eksctl get nodegroup — cluster addycluster in my case my cluster name is addycluster,
>> update kubeconfig file :

![25](https://user-images.githubusercontent.com/51692515/87247845-d7dba280-c473-11ea-948f-54ee011c2915.png)

>> login inside the pod :

![26](https://user-images.githubusercontent.com/51692515/87247846-d8743900-c473-11ea-9954-157811f9a41b.png)

![27](https://user-images.githubusercontent.com/51692515/87247848-d90ccf80-c473-11ea-8108-891c0d8492e6.png)

here you can see why this pod is showing — max-pods=4 , it is because of the Limitation of pods ( Instance type) : So it’s totally depend upon network card and ip address. In t2.micro , In one instance we can’t add more than 4 nic card i.e 4 pods while in t2.small , in one instance we can’t add more than 12 nic card i.e 12 pods.

>> for checking how many pods is running inside the namespace kube-system we have to write one command “kubectl get pods -n kube-system”:

![28](https://user-images.githubusercontent.com/51692515/87247849-d9a56600-c473-11ea-8018-aae5baba2780.png)

Creating Wordpress and Mysql multitier architecture on the top EKS with the help of one single file known as kustomization.yaml :

                     #for reference only
                     apiVersion: kustomize.config.k8s.io/v1beta1
                     kind: Kustomization
                     secretGenerator:
                     - name: mysql-pass
                       literals:
                       - password=mickey
                     resources:
                       - create-storage.yaml
                       - mysql-deployment.yaml
                       - wordpress-deployment.yaml
                       
![29](https://user-images.githubusercontent.com/51692515/87247850-da3dfc80-c473-11ea-860f-91cec858619c.jpg)

![30](https://user-images.githubusercontent.com/51692515/87247851-dad69300-c473-11ea-8afe-6bf901530487.png)


* here you can see the complete multitier architecture is deployed and having wordpress and mysql pods, service type loadbalancer, replica sets and persistant volume claim. now anyone from the outside world who knows my load balancer DNS name can easily access to the wordpress sites.

![31](https://user-images.githubusercontent.com/51692515/87247836-d14d2b00-c473-11ea-9a5d-31d52da967fa.png)

![32](https://user-images.githubusercontent.com/51692515/87247837-d316ee80-c473-11ea-8861-037e7381a0fe.png)

![33](https://user-images.githubusercontent.com/51692515/87247838-d4481b80-c473-11ea-964c-52a5f6ccbced.png)


# THIS IS HOW WE SUCESSFULLY CREATED K8S CLUSTER USING EKS AND DEPLOYED THE WORDPRESS AT FRONTEND AND MYSQL AT BACKEND .......

# THANK YOU VIMAL SIR FOR TEACHING US AND GIVING US GREAT OPPORTUNITY TO LEARN AND GROW UNDER YOU ......


