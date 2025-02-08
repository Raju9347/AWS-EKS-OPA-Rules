# AWS-EKS-OPA-Rules
aws eks open policy agent.


## Create EKS cluster
eksctl create cluster --name my-eks --node-type t2.large --nodes 1 --nodes-min 1 --nodes-max 2 --region eu-west-1 --zones=eu-west-1a,eu-west-1b,eu-west-1c

eksctl create cluster --name my-eks --region eu-west-1

## Create node groups
eksctl create nodegroup --region eu-west-1 --cluster my-eks --name ng-windows --node-type t2.large --nodes 3 --nodes-min 1 --nodes-max 4 --managed=false --node-ami-family WindowsServer2019FullContainer

## Get EKS Cluster service
eksctl get cluster --name my-eks --region eu-west-1

## Update Kubeconfig 
aws eks update-kubeconfig --name my-eks --region eu-west-1

## Get EKS Pod data.
kubectl get pods --all-namespaces

## Delete EKS cluster
eksctl delete cluster --name my-eks --region eu-west-1

###############################################################################################################################
######################################### OPA GATEKEEPER SETUP IN EKS #########################################################
1. Deploy OPA Gatekeeper using Prebuilt docker images

helm repo add gatekeeper https://open-policy-agent.github.io/gatekeeper/charts
helm install gatekeeper/gatekeeper --name-template=gatekeeper --namespace gatekeeper-system --create-namespace


2. Check the pods in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system

3. Observe OPA Gatekeeper Component logs once operational
kubectl logs -l control-plane=audit-controller -n gatekeeper-system
kubectl logs -l control-plane=controller-manager -n gatekeeper-system

4. Build Constraint Templates
kubectl create -f ./constrainttemplate.yaml

5. Build Constraint
kubectl create -f ./constraint.yaml

6. Test it

6.1) Check the constraint
kubectl get constraint
kubectl get constrainttemplate

6.2) deploy a privileged nginx pod
kubectl create -f ./example.yaml
