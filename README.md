# Elasticsearch on Kubernetes
For this case, I first tried the approach of installing the Elasticsearch directly in EC2 instances managed by my own.

I found some issues during the deployment, I tried to use a storage class to have dynamic provisioning, but using EBS attached to instances only local storage is possible, and each node needs to have its own PVC and PV. Link to implementation: [EC2](https://github.com/italosousap/es-k8s/tree/main/ec2-hosted)

So I deployed the Elasticsearch in EKS, here how it was implemented: [EKS](https://github.com/italosousap/es-k8s/tree/main/eks)
