# ebs-csi-project
Dynamically provision of AWS EBS volumes to be used in statefulsets in a kubernetes cluster

aws iam create-policy \
--policy-name AmazonEKS_EBS_CSI_Driver_Policy \
--policy-document /home/ubuntu/ebs-csi-iam-policy.json

# make sure the eks nodes roles have the policy above attached to them or create  a service account attached to the policy then indicate the service account name in the deployment manifest file
#------------------------------------------------------------------------

eksctl create iamserviceaccount \
--name ebs-sci-controller-sa \
--namespace kube-system \
--cluster redbiller-eks-cluster-prod \
--attach-policy-arn arn:aws:iam::<account-id>:policy/AmazonEKS_EBS_CSI_Driver_Policy \
--approve \
-- override-existing-serviceaccounts

#---------------------------------------------------------------------------
# install ebs-csi-driver using kubectl

kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"

# To verify ebs-csi pods, run:
kubectl get pods -n kube-system

# Apply the manifest files above to test. The following are created: a storageclass, PersistentVolumeClaim and pod. A persistent volume is dynamically created by the ebs-csi-controller.


# note: container image registry address depends on the aws region, check: https://docs.aws.amazon.com/eks/latest/userguide/add-ons-images.html

