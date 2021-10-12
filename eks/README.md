# Elasticsearch on Kubernetes

In this solution, the Elasticsearch is deployed in [EKS](https://aws.amazon.com/eks/) which is a managed container service to run and scale Kubernetes applications. This package includes the metrics server to help us.

To help us to install the cluster let's use the [eksclt](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html) it is a simple command-line utility for creating and managing Kubernetes clusters on Amazon EKS.

## Requerements

eksclt installed

python 2.7

To install in Linux:
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
```
[Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#selecting-an-ansible-artifact-and-version-to-install) installed, this version of the script was tested only in the ansible version 2.9

## Executing

Before running this code, you need to have an EC2 ssh key created and update the cluster variables in this path.
```
eks/roles/eks-cluster/vars/main.yml

#cat in the file
---
# vars file for eks_cluster
namespace_name: elastic
cluster_name: picnic
region: us-east-1
ssh_key_name: italo
zones: 'us-east-1a,us-east-1b,us-east-1c,us-east-1d,us-east-1f'
instance_type: m5.large
profile: italo
node_storage: 50
node_min: 2
node_max: 4

crds_operator:
  - https://download.elastic.co/downloads/eck/1.8.0/crds.yaml
  - https://download.elastic.co/downloads/eck/1.8.0/operator.yaml

metric_server:
  - https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
  - https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.5/aio/deploy/recommended.yaml

resources:
  - namespace.yml
  - storageClass.yml
  - eks-admin-service-account.yml
```

Command to run
```
ansible-playbook -h host main.yml
```
Execution duration is about 20 minutes, after that, you can get the load balance endpoint and elastic credentials

## Acess
The default user is elastic

Get credentials
```
kubectl get secret lab-es-elastic-user -n elastic -o go-template='{{.data.elastic | base64decode}}'

```

To get ELB endpoint
```
kubectl get services -n elastic
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S)          AGE
lab-es-http        LoadBalancer   10.100.219.146   a35ef3eab30814390a2a78021baeff44-2002423714.us-east-1.elb.amazonaws.com   9200:30904/TCP   5h45m
lab-es-node        ClusterIP      None             <none>                                                                    9200/TCP         5h45m
lab-es-transport   ClusterIP      None             <none>                                                                    9300/TCP         5h45m
```
## Metrics server

get the token to access the metrics server dashboard.
```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}')
```
Execute to access the dashboard
```
kubectl proxy
```
In your browser paste the URL
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#!/login
