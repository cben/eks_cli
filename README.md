# EKS-CLI

EKS cluster bootstrap with batteries included

## Usage

```
$ gem install eks_cli
$ eks bootstrap us-west-2 --cluster-name=My-EKS-Cluster
$ eks create-cluster-vpc --cluster-name=My-EKS-Cluster
$ eks create-cluster-security-group --cluster-name My-EKS-Cluster --open-ports=22
$ eks create-nodegroup --cluster-name My-EKS-Cluster --group-name nodes --ssh-key-name my-ssh-key --min 1 --max 3
$ eks create-nodegroup --cluster-name My-EKS-Cluster --group-name other-nodes --ssh-key-name my-ssh-key --min 3 --max 3 --instance-type m5.2xlarge
$ eks create-nodegroup --all --cluster-name My-EKS-Cluster --yes
```

## Extra Stuff

### Setting IAM policies to be attached to EKS nodes

`$ eks set-iam-policies --cluster-name=My-EKS-Cluster --policies=AmazonS3FullAccess AmazonDynamoDBFullAccess`

Makes sure all nodegroup instances are attached with the above policies once created

### Routing Route53 hostnames to Kubernetes service

`$ eks update-dns my-cool-service.my-company.com cool-service --route53-hosted-zone-id=XXXXX --elb-hosted-zone-id=XXXXXX --cluster-name=My-EKS-Cluster`

Takes the ELB endpoint from `cool-service` and puts it as an alias record of `my-cool-service.my-company.com`

### Enabling GPU

`$ eks enable-gpu --cluster-name EKS-Staging`

Installs the nvidia device plugin required to have your GPUs exposed

*Assumptions*: 

1. You have a nodegroup using [EKS GPU AMI](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html)
2. This nodegroup uses a GPU instance (p2.x / p3.x etc)

### Adding Dockerhub Secrets

`$ eks set-docker-registry-credentials <dockerhub-user> <dockerhub-email> <dockerhub-password> --cluster-name My-EKS-Cluster`

Adds your dockerhub credentials as a secret and attaches it to the default serviceaccount imagePullSecrets

### Creating Default Storage Class

`$ eks create-default-storage-class --cluster-name My-EKS-Cluster`

Creates a standard gp2 default storage class named gp2

### Connecting to an existing VPC

`$ eks set-inter-vpc-networking VPC_ID SG_ID`

Assuming you have some resources shared on another VPC (an RDS instance for example) this command allows you to interact with from the new EKS cluster it by:

1. Creating and accepting a VPC peering connection from you EKS cluster VPC to the old VPC
2. Setting route tables on both directions to allow communication
3. Adding an ingress role to SG_ID to accept all communication from your new cluster nodes.

