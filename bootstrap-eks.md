Bootstrap an EKS Cluster

# Create Cluster

Create EKS cluster using Rancher. This ensures the cluster is monitored and logs can be centrally piped to a pre-defined location

# Install Helm

Install the `tiller` service account and install Helm

```bash
kubectl create serviceaccount tiller --namespace=kube-system
kubectl create clusterrolebinding tiller-admin --serviceaccount=kube-system:tiller --clusterrole=cluster-admin
```

```bash
#
# initialize helm locally
#
helm init --service-account=tiller && helm repo update
```

# Install sealed-secrets

Sealed secrets is used to encrypt sensitive information such as API keys, database passwords etc. Install this - recovery from - or generate and backup the master key for the given environment before proceeding. There is a 1-to-1
correspondence between the secrets encrypted in the GitOps repository and the SealedSecret private key

# Using Helm to Install nginx-ingress Controller

```bash
helm install stable/nginx-ingress --name default
```

# Configure Route 53

Configure route53 to assign a DNS name to the new ingress-controller. The ingress controller should've created on AWS an classic load balancer.
If using route53, one would need to create a `A` record that is an __alias__ record to the ELB that fronts the nginx-ingress-controller

Several DNS records can front the same ELB/nginx-ingress-controller

# Image pull secrets

Make sure the default service account && flux can pull images using `imagePullSecrets` to authenticate with the private Docker registry

# Install flux

`flux` is the GitOps operator from Weaveworks. Install:
 1 - `fluxd` into the cluster (set git-url to the (environment) GitOps repo, flux will manage it)
 > To install flux go to []()
 3 - `fluxctl` into the users' machine

# Certificate issuer and certificate setup

Install cert-manager and configure nginx-ingress controller to use cert-manager managed certificates