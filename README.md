# eks-ha-dr Project

eks-ha-dr is prototyping project for HA (High Availability) and DR (Disaster Recovery) based on AWS EKS. eks-ha-dr project consists of the following git repositories.

* [aws-terraform](https://github.com/ssup2-playground/eks-ha-dr_aws-terraform) : Terraform for EKS clusters and AWS Resources 
* [service-peccy-web](https://github.com/ssup2-playground/common_service-peccy-web) : Service peccy's web server 
* [service-peccy-app](https://github.com/ssup2-playground/common_service-peccy-app) : Service peccy's app server
* [deploy-eks](https://github.com/aws-playground/eks-ha-dr_deploy-eks) : K8s manifests for deploying peccy service

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
  * Since most Pods exist in only one AZ, Cross-AZ Traffic caused by communication between Pods is reduced, which reduces latency and traffic costs.
* HA in case of AZ failure
  * If an AZ failure occurs, traffic is not sent to the AZ where the failure occurred through Route 53's health check.

## DR Multi Cluster Architecture

<img src="/images/architecture-dr-multi-stable.png" width="800"/>

<img src="/images/architecture-dr-multi-down.png" width="800"/>
