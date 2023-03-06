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