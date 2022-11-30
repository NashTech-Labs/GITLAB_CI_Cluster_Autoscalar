# GITLAB_CI_Cluster_Autoscalar

#Pre-requisite
1. Use image with tools installed such as kubectl , aws cli
2. Make sure to add proper values before running the pipeline

You can follow this documentation also , The CI Pipeline was created by taking a reference from this
Documentation.

https://docs.aws.amazon.com/eks/latest/userguide/autoscaling.html

# Before Starting to Run CI/CD Pipline make sure you have,
- An IAM OIDC Provider for your cluster, you can check the oidc from your cluster's configuration.
- Make sure to add these tag in Auto scaling groups
  
 |      Key                    |                 Value    |
 | -----------                 |              ----------- |
 | k8s.io/cluster-autoscaler/[my-cluster]   |    owned    |
 | k8s.io/cluster-autoscaler/enabled	    |      true   |

## Creating an IAM policy and Role

- To Create a policy which is already present in the repository, You can run the first Stage of Pipeline which will create the policy. The following Command is used to create policy. 

```
   aws iam create-policy \
    --policy-name AmazonEKSClusterAutoscalerPolicy \
    --policy-document file://cluster-autoscaler-policy.json
```    

## create an IAM role and attach an IAM policy to it.

- Next Step Comes is to create a IAM Role , Which we will do manually Through Console
- To do that , You can go to IAM Role and choose Create Role
- In the Trusted entity type section, choose Web identity.
- In the Web identity section:
  For Identity provider, choose the OpenID Connect provider URL for your cluster (as shown under Configuration Details in Amazon EKS). For Audience, choose sts.amazonaws.com.

- Choose next and In the filter Policy you have to choose the policy name we created earlier , After that you have to give role name and Description and choose create role.

- After the role is created , open it for editing and Choose Trust relationships tab, and then choose Edit trust policy.

- Find the line that looks similar to the following:

  "oidc.eks.region-code.amazonaws.com/id/EXAMPLED539D4633E53DE1B71EXAMPLE:aud": "sts.amazonaws.com"
  
- Below that add another line

  "oidc.eks.region-code.amazonaws.com/id/EXAMPLED539D4633E53DE1B71EXAMPLE:sub": "system:serviceaccount:kube-system:cluster-autoscaler"

- Now update the Policy

## Deploying the Autoscalar

Before deploying make sure , You are going for the existing deployment file, Or if you choose another deployment file make sure to do all necessary changes, Here In this case The deployment file has all the changes , So no need to do patching or any other step present in reference documentation.

- Directly Execute the second Stage in the CI Pipline and it will be deployed.

