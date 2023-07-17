## Setup the Lab Env

You need a Kubernetes Cluster to run the crossplane. Below steps will help you create and manage a kubernetes cluster on an EC2 instance using Docker and K3D.

1) Create an AWS Instance running a LINUX OS (Eg: Ubuntu Server LTS)

2) Install Docker by following the documentation => https://docs.docker.com/engine/install/ubuntu/

3) Allow ubuntu user to run docker commands

    ```
    sudo adduser ubuntu docker
    ```

4) Install K3D by following => https://k3d.io/

5) Create a Kubernetes cluster with k3d by running below command:

    ```k3d cluster create --agents=2```

6) Install Kubectl by following => https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/


## Install Crossplane 

#### 1) Set up Prerequisites: 
* Lab setup should be done following above instructions
* Helm must be installed on your laptop by following the instructions at https://helm.sh/docs/intro/install/

#### 2) Install Crossplane
You can install crossplane by following the instructions at https://docs.crossplane.io/latest/software/install/



## Install AWS Provider

You can install provider-aws package from upbound marketplace => https://marketplace.upbound.io/providers/upbound/provider-aws/v0.37.0

```A secret which consist of your aws credentials must be created to complete the provider configuurations```

## Create an S3 bucket

```
git clone https://github.com/basil1987/crossplane-springpeople.git
cd crossplane-springpeople/managed
kubectl apply -f bucket.yaml
```

## Create an EC2 Instance

```
git clone https://github.com/basil1987/crossplane-springpeople.git
cd crossplane-springpeople/managed
kubectl apply -f instance.yaml
```

## Create an RDS Instance

```
git clone https://github.com/basil1987/crossplane-springpeople.git
cd crossplane-springpeople/managed/rds
kubectl apply -f .
```