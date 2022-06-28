# Use VS Code with Bastion (SSH tunnel)

I love working with VS Code; it is such a great tool. One of the features I like most is to work with remote resources (machines, containers, etc) as my development environment. It makes working with local resources (e.g. files, etc) so easy. 

Recently, I needed to work with private Azure resources (resources deployed in an Azure vnet with no public endpoints); things like creating files, running AZ CLI commands, etc. Azure Bastion enabled me to connect to a jump box (an Linux Azure VM) and work from there but I wasn't as productive as with VS Code. 

Luckily, there is a way to use VS Code with Bastion allowing me to use my local dev tools to 
## Prerequisites

- [Azure CLI]([How to install the Azure CLI | Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)) 
- VS Code with the [Remote - SSH extension]([Remote - SSH - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)) installed 
- Azure Bastion with a "*jump box*" Azure VM

## Setup

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

## Troubleshooting

- **Problem**: VS Code can't reconnect (e.g. if opening a folder, etc.); stays in an endless loop. 
  **Solution**: Restart the SSH tunnel and retry VS Code connection
