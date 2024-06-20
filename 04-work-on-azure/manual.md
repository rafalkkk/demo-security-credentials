## How to run a workflow without secrets

This demo shows that:
- a workflow can read secret and run a command on Azure (creation of a container on storage account)
- if a machine has assigned managed identity, and is configured as runner, it can create the container without knowing password

## Required
- access to Azure
- a repo (can be this one)

## Steps
### Preparation
Create a **storage account** and gather some information. 
```
DATE=$(date "+%Y%m%d%H%M%S")
RG="demo-$DATE"
az group create --name $RG --location northeurope
ACCNAME="demo$DATE"
az storage account create --name $ACCNAME --resource-group $RG --sku Standard_LRS --location northeurope
KEY1=$(az storage account keys list --resource-group $RG --account-name $ACCNAME --query [0].value -o tsv)

echo "--------------------------------"
echo "RG:      $RG"
echo "ACCNAME: $ACCNAME"
echo "KEY1:    $KEY1"
echo "--------------------------------"
```
- (Optional) Show, that we can create a container from shell/cloud shell:
```
TIME=$(date "+%H%M%S")
az storage container create --name $TIME --account-name $ACCNAME --account-key $KEY1 --auth-mode key
```
- add secrets to repo   SA_NAME  SA_KEY1
  
### Create a storage container using secrets 
- add workflow creating the container using secrets, or use the existing container-on-storage-account.yml
```yaml
name: Create a container in storage account

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  do_some_actions_on_azure:
    runs-on: ubuntu-latest
    # runs-on: self-hosted

    env:
      SA_NAME: ${{ secrets.SA_NAME }}
      SA_KEY1: ${{ secrets.SA_KEY1 }}
      
    steps:

    - name: Create a container in Azure
      run: |
        TIME=$(date "+%H%M%S")
        az storage container create --name "key-$TIME" --account-name $SA_NAME --account-key $SA_KEY1 --auth-mode key
```
- add step creating the new container (folder) on the storage account - uncoment the first part and definition on variables
- run and show that the new container was created using the secret key

### Create a storage container using private runner
- Create or use an existing VM as a runner - choose the OS carefully, the code depends on OS
    - Set identity for the VM
    - Add assignment on the level of the RG for the VM to the role "Storage Blob Data Contributor"
    - Install azcli
- Add this computer as a runner on github (menu actions)
    - run the runner process - 
- Uncomment the code in the workflow file, which will use the private runner
- Run the workflow again - a storage account container will be created without using any secrets


## Additional scripts
- To create a Linux runner you can use following code (storage account and the VM will reside in the same resource group):
```bash
VMNAME="vm-demo-runner-01"
RGNAME='demo20240613080521'
SANAME='demo20240613080521'
read -s PASS
az vm create --resource-group $RGNAME --name $VMNAME --image Ubuntu2204 --size Standard_B2s --admin-username boss --admin-password $PASS

# configure managed identity
az vm identity assign --resource-group $RGNAME --name demovmrunner01

# grant permissions
SCOPE=$(az storage account show --name $SANAME --query "id" --output=tsv)
APPID=$(az vm show --resource-group $RGNAME --name demovmrunner01 --query identity.principalId --output=tsv)
az role assignment create --role "Storage Blob Data Contributor" --assignee $APPID --scope $SCOPE
```
- installation of AZ CLI on Linux:
``` curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash ```
- Configure the machine as runner on github (menu actions)
- Make sure the right part of the workflow is uncommented (environment // run-on // creation of container)
- Run the workflow


#### Backup:

# COMMAND=$(echo -n "curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash" | base64)
# az vm create --resource-group $RG --name $VMNAME --image Ubuntu2204 --size Standard_B2s --admin-username boss --admin-password $PASS --custom-data $COMMAND

