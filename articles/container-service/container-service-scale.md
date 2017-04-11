<properties
   pageTitle="不按比例縮放與 Azure CLI ACS 叢集 |Microsoft Azure"
   description="如何使用 Azure CLI Azure 容器服務叢集不按比例縮放。"
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker，容器，微服務，Mesos、 Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="timlt"/>

# <a name="scale-an-azure-container-service"></a>不按比例縮放 Azure 容器服務

您可以調整出 Azure 容器服務 (ACS) 有使用 Azure CLI 工具的節點數目。 當您使用 Azure CLI 不按比例縮放時，工具會傳回您新的設定檔，代表套用至容器的變更。

## <a name="about-the-command"></a>有關的指令

Azure CLI 必須是您的互動 Azure 容器 Azure 資源管理員模式。 您可以切換到 [資源管理員模式，則可電話`azure config mode arm`。 `acs`命令具有名為子命令`scale`執行容器服務的小數位數作業。 您可以取得關於使用縮放命令中執行之各種參數說明`azure acs scale --help`，其中輸出類似以下內容︰

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>使用 [縮放] 命令

若要縮放的容器服務，您必須瞭解**資源群組**和**Azure 容器服務 (ACS) 名稱**，並也指定新代理程式的計數。 藉由使用較小或更高的金額，您可以調整向下或分別設定。

您可能會想要知道哪些目前的計數代理程式的容器服務才能您不按比例縮放。 使用`azure acs show <resource group> <ACS name>`命令，以傳回 ACS 設定。 請注意<mark>計算</mark>結果。

#### <a name="see-current-count"></a>請參閱目前的計數

```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>不按比例縮放至新的計算

因此可能已經不言而喻，您可以調整容器服務，則可電話`azure acs scale`並提供**資源群組**、 **ACS 名稱**，而**代理程式計數**。 當您要縮放的容器服務時，Azure CLI 會傳回代表容器服務，包括新的代理程式計算的新設定的 JSON 字串。

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>後續步驟

- [部署叢集](container-service-deployment.md)