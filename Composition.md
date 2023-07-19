#### Composite Resources(XR). 

An XR is a resource that you wants to create to get the opinionated resource you need for doing something. 

Below example shows an XR which when you submit, Crossplane will use its machinery to do whatever is necessary get the resource created and ready


```
apiVersion: springexample.io/v1alpha1
kind: XObjectStorage
metadata:
  name: test-bucket-awsblueprint-123456789
spec:
  resourceConfig:
    region: us-east-1
```

An XR schema is defined in another resource called XRD. It basically provider a custom API Group, Kind, and the schema of the XR. 


#### Composite Resources Definition (XRD)

XRD kind of looks like a CRD. A Sample reference is given below which defines schema for above XR. 


```
# Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
# SPDX-License-Identifier: MIT-0

apiVersion: apiextensions.crossplane.io/v1
kind: CompositeResourceDefinition
metadata:
  name: xobjectstorages.springexample.io
spec:
  claimNames:
    kind: ObjectStorage
    plural: objectstorages
  group: springexample.io
  names:
    kind: XObjectStorage
    plural: xobjectstorages
  versions:
    - name: v1alpha1
      served: true
      referenceable: true
      schema:
        openAPIV3Schema:
          properties:
            spec:
              description: ObjectStorageSpec defines the desired state of ObjectStorage
              properties:
                resourceConfig:
                  description: ResourceConfig defines general properties of this AWS
                    resource.
                  properties:
                    name:
                      description: Set the name of this resource in AWS to the value
                        provided by this field.
                      type: string
                    region:
                      type: string
                    tags:
                      items:
                        properties:
                          key:
                            type: string
                          value:
                            type: string
                        required:
                        - key
                        - value
                        type: object
                      type: array
                  required:
                  - region
                  type: object
              required:
              - resourceConfig
              type: object
            status:
              description: ObjectStorageStatus defines the observed state of ObjectStorage
              properties:
                bucketName:
                  type: string
                arn:
                  type: string
              type: object
          type: object
```


#### Composition 

The composition tells what to do when an XR is created. This may mean creating a number of managed resources (MR),  patching some fields, setting up default values. Composition refer MR. 

The sample reference is below. 


```
# Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
# SPDX-License-Identifier: MIT-0

apiVersion: apiextensions.crossplane.io/v1
kind: Composition
metadata:
  name: s3bucket.springexample.io
  labels:
    springexample.io/provider: aws
    springexample.io/environment: dev
    s3.springexample.io/configuration: standard
spec:
  compositeTypeRef:
    apiVersion: springexample.io/v1alpha1
    kind: XObjectStorage
  resources:
    - name: s3-bucket
      base:
        apiVersion: s3.aws.upbound.io/v1beta1
        kind: Bucket
        spec:
          deletionPolicy: Delete
          forProvider:
            region: ap-south-1
            forceDestroy: true # be careful with this
      patches:
        - type: FromCompositeFieldPath
          fromFieldPath: spec.resourceConfig.region
          toFieldPath: spec.forProvider.region
        - type: ToCompositeFieldPath
          fromFieldPath: status.atProvider.id
          toFieldPath: status.bucketName
        - type: ToCompositeFieldPath
          fromFieldPath: status.atProvider.arn
          toFieldPath: status.arn
```



#### Creating an XR 

The folder composite/s3-simple/composition consist of the XRD and Composition objects. You can create them in Kubernetes with kubectl apply. 

```
git clone 
```





