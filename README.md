# Create a service principal for Terroform
```
# In .bashrc
export MSYS_NO_PATHCONV=1

az ad sp create-for-rbac --name terraform --role Contributor --scopes /subscriptions/<subscription_id>

# in .bashrc
export MSYS_NO_PATHCONV=1
export ARM_SUBSCRIPTION_ID="<subscription_id>"
export ARM_CLIENT_ID="<client-id>"
export ARM_CLIENT_SECRET=<client-secret>"
export ARM_TENANT_ID="<tenant-id>"

export TF_VAR_aks_service_principal_app_id=$ARM_CLIENT_ID
export TF_VAR_aks_service_principal_client_secret=$ARM_CLIENT_SECRET

terraform init
terraform plan -out main.tfplan
terraform apply main.ftplan

# get the k8s configuration from the terraform output
# remove << EOT at the beginning and EOT at the end, 
echo "$(terraform output kube_config)" > ~/.k8s-training-config
export KUBECONFIG=~/.k8s-training-config

# verify the health of the cluster.
kubectl get nodes

terraform plan -destroy -out main.destroy.tfplan
terraform apply main.destroy.tfplan
```

# Install Kustomize
```
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash
```

# Install ArgoCD

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# access argocd from outside. Use Ingress instead for production
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

# install argocd CLI
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64

# log into argocd
# get the base64 encoded initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
# find external ip of argocd server
kubectl describe pod argocd-server-<id> -n argocd
argocd login <ip>

# allow argocd to create resources in namespace k8s-training
# add the following to the config map
# data:
#  application.namespaces: app-team-one, app-team-two
kubectl edit configmap -n argocd argocd-cmd-params-cm

# Create ArgoCD app
# ghcr.io repo must be public (or you'll find out how to configure access from aks to ghcr.io)


```