## Use VS Code to work resources in a Azure Virtual Network

I recently had to setup a [private Azure Kubernetes Service (AKS) cluster](https://docs.microsoft.com/en-us/azure/aks/private-clusters). One of the challenges when working with vnet-secured resources is, how to work with them effectively (e.g. to run `kubectl` commands). Some common options are:

1. Use a VPN connection (e.g. point to site)
1. Use a 'jump box' VM with Azure Bastion (deployed in the same vnet)

Using a jump box with Azure Bastion is a great option (especially during development) as it is easy to setup and doesn't require any config change (VPN client) on my laptop (besides a RDP or SSH client). The most obivous option here is to RDP or SSH into the jump box VM (via Azure Bastion) and access the resources from there. That will work because the VM is on the same vnet. However, productivity might not be great and it is kind of difficult to effectively collaborate in a team.

Luckily, you can also use your local VS Code editor and establish a remote connection with the jump box. Being able to use the VS Code on my local machine gives me a big productivity boost. With that setup, you can even use VS Code's 'Live Share' when working in teams.  

Continue reading, if you want to know how to set this up.

### Prerequisites

- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
- VS Code with the [Remote - SSH extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) installed 
- Azure Bastion with a "*jump box*" Azure VM

### Setup

1. Ensure the Azure Bastion instance has "Native Client Support" enabled (see <https://docs.microsoft.com/en-us/azure/bastion/connect-native-client-windows>)

1. Open a new Linux terminal session and create a SSH tunnel (Bastion) using Azure CLI 

    ```sh
    AZ_RES_GRP="<AZURE RESOURCE GROUP>"
    BASTION_NAME="<AZURE BASTION NAME>"
    JUMPHOST_RES_ID="<AZURE RESOURCE ID OF JMPHOST VM>"
    RESOURCE_SSH_PORT="22"
    SSH_PORT="22"

    az network bastion tunnel --name $BASTION_NAME \
        --resource-group $AZ_RES_GRP --target-resource-id $JUMPHOST_RES_ID \
        --resource-port $RESOURCE_SSH_PORT --port $SSH_PORT
    ```

	> *Note: As time of writing the 'network bastion' was still in preview and under development*

3. Open VS Code & add this new SSH target `ssh <AZURE-VM-JUMPBOX-USER>@127.0.0.1 -p <SSH_PORT>`

   > *Note: Replace the parameter with the correct values*

4. When prompted sign-in with your *Azure VM jump box* user password.

### Troubleshooting

- **Problem**: VS Code can't reconnect (e.g. if opening a folder, etc.); stays in an endless loop. 
  **Solution**: Restart the SSH tunnel and retry VS Code connection
