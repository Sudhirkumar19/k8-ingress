# Ingress Controller

STEP: 1

---

eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster expense \
    --approve

---

# Ingress controller is outside of EKS , to provide access use above command, oidc provider, this is part of IAM services


STEP-2:

We need to create some policies

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.10.0/docs/install/iam_policy.json

STEP-3:

IAM policy:

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
----

STEP-4:
---

Create an IAM role and kubernetes ServiceAccount for the LBC , use the ARN from the previous step

eksctl create iamserviceaccount \
--cluster=expense \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::315069654700:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve

# in above policy use your arn id

----

STEP-5
----


next install drivers through HELM charts

---

helm repo add eks https://aws.github.io/eks-charts

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=expense --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller





