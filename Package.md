## Packaging and Distributing your compositions.

Crossplane has "Configurations" that allows you to package your compositions, xrds and the metadata to into OCI compatable images. Below diagram demonstrates a typical workflow. 



## package the application 

1) To package, you need a machine with crossplane cli installed. 

Follow the link to install Crossplane CLI on the AWS machine => https://gist.github.com/jonashackt/2da4dd06798fa391eda1a3fc299490c3

```
curl -sL https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh | sh
sudo mv kubectl-crossplane /usr/local/bin
```

You can verify the installation by running the command "kubectl crossplane".


2) Package your configurations into an OCI-compatible image. The folder "packages/my-dynamo-bucket" consist of a sample configuration ready to be packaged.

```
git clone https://github.com/basil1987/crossplane-springpeople.git
git pull # if you already cloned.
cd packages/my-dynamo-bucket
ls
kubectl crossplane build configuration # Build the package
```

Once the package is ready you will be able to see it (file that ends with .xpkg) in your current folder. 

```
ubuntu@ip-172-31-35-216:~/crossplane-springpeople/packages/my-dynamo-bucket$ ls
composition.yaml  crossplane.yaml  definition.yaml  my-dynamo-bucket-e7750a56771c.xpkg
```

3) Push to the oci image to a registry (Docker Hub / ECR) to share and distribute. 

```
kubectl crossplane push configuration YOUR_DOCKERID/my-dynamo-bucket:latest
```

NOTE: You must login to the registry by using "docker login" and providing your credentials at Docker Hub. If you are using ECR, go to the ECR page and find the commands there to login. 


## Install the Package a Kubernetes Cluster that has crossplane installed. 

You can insatll the package on any Kubernetes (k3d, eks) by running below command. Make sure that crossplane cli is installed first.

```
kubectl crossplane install configuration basilvarghese/my-dynamo-bucket:latest 
```

Replace "basilvarghese/my-dynamo-bucket:latest" with your own oci image ID that you pushed in the last step. 

Alternatively you can also create a cnfiguration by defining it as a configuration object of pkg.crossplane.io/v1 . Create a YAML file with below content and apply it with kubectl.


```
apiVersion: pkg.crossplane.io/v1
kind: Configuration
metadata:
  name: my-org-infra
spec:
  package: basilvarghese/my-dynamo-bucket:latest
  packagePullPolicy: IfNotPresent
  revisionActivationPolicy: Automatic
  revisionHistoryLimit: 1
  PackagePullSecrets: YOUR-SECRET
```


NOTE: If you are using a private registry to store your packages, your kubernetes cluster wont be able to pull the packages unless a "PackagePullSecrets" field is mentioned. 



## Verify the installation 

You can verify that configuration got created using below command. 

```
kubectl get configurations
```

Wait until status becomes READY=true .

make sure that compositions and xrd exists in the cluster. 

```
kubectl get compositions
kubectl get xrd 
```

The above commands should display the composition and xrd that you packaged. 



## Make a claim 

You can make a claim by applying below YAML using kubectl apply.

Please note that Claims are namespaced. So create the claim in the namespace of your chouce (eg: dev) when you apply.


```
apiVersion: custom-api.example.org/v1alpha1
kind: custom-database
metadata:
  name: claimed-eu-database-basil
  namespace: dev
spec:
  region: "AP"
```

Copy above content to a file called Claim.yaml and run 

```
kubectl create namespace dev
kubectl apply -f Claim.yaml --namespace dev
```

After you created the claim, verify that it got created by running. 

```
kubectl get custom-database.custom-api.example.org
```

Verify the XR got created by running

```
kubectl get composite
```

make sure that it has a status of Synced=True and READY=true.


If READY is False, run the describe command and look under the "spec.Resource Refs". You should see all the managed resources reference here. Make sure that MRs are present there. 


Now check the status of the MRs by running below command. 

```
kubectl get managed
```

You will see an S3 Bucket as well as a Dynamo table. Both resources must have READY=true.

If READY=False, run the kubectl describe command against each managed resource which false status. 

