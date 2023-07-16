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

