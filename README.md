# eks-ha-dr Project

eks-ha-dr is prototyping project for HA (High Availability) and DR (Disaster Recovery) based on AWS EKS. eks-ha-dr project consists of the following git repositories.

* [aws-terraform](https://github.com/ssup2-playground/eks-ha-dr_aws-terraform) : Terraform for EKS clusters and AWS Resources 
* [service-peccy-web](https://github.com/ssup2-playground/common_service-peccy-web) : Service peccy's web server 
* [service-peccy-app](https://github.com/ssup2-playground/common_service-peccy-app) : Service peccy's app server
* [deploy-eks](https://github.com/aws-playground/eks-ha-dr_deploy-eks) : K8s manifests for deploying peccy service

## Install

* Run terraform

```bash
# Get terraform code
$ git clone https://github.com/ssup2-playground/eks-ha-dr_aws-terraform.git && rm ./eks-ha-dr_aws-terraform/ha-single-cluster/terraform.tf && rm ./eks-ha-dr_aws-terraform/ha-multi-cluster/terraform.tf && rm ./eks-ha-dr_aws-terraform/dr-multi-region/terraform.tf

# Run terraform for HA single cluster architecture
$ cd eks-neuron_aws-terraform/ha-single-cluster
$ terraform init
$ terraform apply

# Run terraform for HA multi cluster architecture
$ cd eks-neuron_aws-terraform/ha-multi-cluster
$ terraform init
$ terraform apply

# Run terraform for DR multi region architecture
$ cd eks-neuron_aws-terraform/dr-multi-region
$ terraform init
$ terraform apply
```

## Service Peccy

<img src="/images/screenshot-service-peccy-web.png" width="400"/>

Servic peccy is a simple web service to demonstrate eks-ha-dr project. Service peccy consists of **3-tier** architecture. Service peccy uses AWS RDS Aurora (MySQL) to store peccy's hobby and AWS EFS to store peccy's picture.

## HA Single Cluster Architecture

<img src="/images/architecture-ha-single-stable.png" width="800"/>

<img src="/images/architecture-ha-single-down.png" width="800"/>

“HA Single Cluster Architecture” is the most commonly used architecture. One EKS Cluster consists of multiple AZs.

* Node Groups
  * Control : Node group for controller and plugins. It is configured as Multi-AZ by ASG so that it can be quickly recovered in the event of an AZ failure.
  * Karpenter : Node group for workloads. Configured as Multi-AZ using K8s "Pod Topology Spread Constraints" feature.
* HA in case of AZ failure
  * EKS Cluster consists of multiple AZs. so even if a failure occurs in one AZ, the workload can be maintained through the remaining AZs.


## HA Multi Cluster Architecture

<img src="/images/architecture-ha-multi-stable.png" width="800"/>

<img src="/images/architecture-ha-multi-down.png" width="800"/>

“HA Multi Cluster Architecture” is an architecture in which the workload located in each EKS Cluster uses only one AZ.

* Traffic distribution between EKS clusters
  * Traffic distribution between EKS Clusters is done by Route 53.
  * Adjust the traffic ratio between EKS Clusters using Route53's weighted routing function.
* Cross-AZ Traffic
  * Since most pods exist in only one AZ, Cross-AZ Traffic caused by communication between pods is reduced, which reduces latency and traffic costs.
* HA in case of AZ failure
  * If an AZ failure occurs, traffic is not sent to the AZ where the failure occurred through Route 53's health check.

## DR Multi Cluster Architecture

<img src="/images/architecture-dr-multi-stable.png" width="800"/>

<img src="/images/architecture-dr-multi-down.png" width="800"/>

“DR Multi Cluster Architecture” is an architecture for DR with Active/Stand-by EKS Cluster.

* Route53
  * Through Route 53's Weighted Record function, all traffic is set to be transmitted only to the Active Cluster.
* Active Cluster
  * Active Cluster is a cluster where workload operates and receives traffic.
  * Active Cluster consists of Multi-AZ.
* Stand-by Cluster
  * Stand-by Cluster is a preparation cluster for quick recovery when performing DR.
  * Stand-by Cluster normally consists of only the Control Node Group.
  * Karpenter is installed, but Karpenter pod is not operating because replica of Karpenter deployment is set to 0.
  * Workload pod is deployed, but remains in Pending status because there are no EC2 Instances to be scheduled.
* How to Perform DR
  * Set replicaset of Karpenter deployment to 2.
  * When Karpenter starts operating, it discovers a workload pod with a pending status and creates EC2 instances.
  * Configure Route53 so that all traffic is sent to the stand-by cluster.
