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
 
For connecting to aws EKS we have following ways such as webUI , CLI and terraform code also. Here we are using CLI but for connecting to aws EKS using CLI we required secret key and access key and CLI also have two ways to connect to EKS
* aws eks command : don’t have a lot of options for customization.
* eksctl command : it is weave community slack command. It launches cluster for us and lots of customization can be done with this command.

