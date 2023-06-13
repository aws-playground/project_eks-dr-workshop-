# eks-ha-dr Project

eks-ha-dr is prototyping project for HA (High Availability) and DR (Disaster Recovery) based on EKS. eks-ha-dr project consists of the following git repositories.

* [aws-terraform](https://github.com/ssup2-playground/eks-ha-dr_aws-terraform) : Terraform for EKS clusters and AWS Resources 
* [service-peccy-web](https://github.com/ssup2-playground/eks-ha-dr_service-peccy-web) : Service peccy's web server 
* [service-peccy-app](https://github.com/ssup2-playground/eks-ha-dr_service-peccy-app) : Service peccy's app server
* [deploy-eks](https://github.com/aws-playground/eks-ha-dr_deploy-eks) : K8s manifests for deploying peccy service

## Service Peccy

<img src="/image/service_peccy_web.png" width="400"/>

Servic peccy is a simple web service to demonstrate eks-ha-dr project. Service peccy consists of **3-tier** architecture. Service peccy uses AWS RDS Aurora (MySQL) to store peccy's hobby and AWS EFS to store peccy's picture.

## HA Single Cluster Architecture

<img src="/image/architecture_HA_single_stable.png" width="800"/>

<img src="/image/architecture_HA_single_down.png" width="800"/>

## HA Multi Cluster Architecture

<img src="/image/architecture_HA_multi_stable.png" width="800"/>

<img src="/image/architecture_HA_multi_down.png" width="800"/>

## DR Multi Cluster Architecture

<img src="/image/architecture_DR_multi_stable.png" width="800"/>

<img src="/image/architecture_DR_multi_down.png" width="800"/>
