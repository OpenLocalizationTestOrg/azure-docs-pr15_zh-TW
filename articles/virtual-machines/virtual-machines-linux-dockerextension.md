<properties
   pageTitle="使用副檔名為 Azure Docker VM |Microsoft Azure"
   description="瞭解如何使用副檔名為 Docker VM 快速及安全地部署 Docker 環境中使用的資源管理員範本 Azure。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>建立 Docker 環境中使用副檔名為 Docker VM Azure
Docker 是常見的容器管理和影像的平台，可讓您快速使用容器 Linux （以及 Windows)。 Azure 一些您可以根據您的需求，部署 Docker 的各種方式。 本文將著重於使用副檔名為 Docker VM 和 Azure 資源管理員範本。 

如需不同部署方法，包括使用 Docker 電腦和 Azure 容器服務，請參閱下列文章︰

- 若要快速原型應用程式，您可以建立單一 Docker 主機使用[Docker 電腦](./virtual-machines-linux-docker-machine.md)。
- 大的更多穩定環境中，您可以使用 Azure Docker VM 分機]，也支援[Docker 撰寫](https://docs.docker.com/compose/overview/)產生一致的容器部署。 使用副檔名為 Azure Docker VM 此文件詳細資料。
- 若要建立生產就緒，可調整環境提供額外的排程與管理工具，您可以部署[Docker 廣域叢集 Azure 容器服務](../container-service/container-service-deployment.md)。


## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 副檔名概觀
Azure Docker VM 副檔名為安裝和設定 [Linux 虛擬機器 (VM) 中的 [Docker 精靈、 Docker 用戶端和 Docker 撰寫。 藉由使用 Azure Docker VM 副檔名，您有多個控制項和功能比只使用 Docker 電腦，或者自己建立 Docker 主機。 這些額外功能，例如[Docker 撰寫](https://docs.docker.com/compose/overview/)，讓適用於更強大的開發人員或生產環境 Azure Docker VM 副檔名。

Azure 資源管理員範本定義您的環境的整個架構。 範本可讓您建立及設定資源，例如 Docker 主機 Vm、 儲存、 角色型存取控制 (RBAC) 和診斷。 您可以重複使用這些範本，建立其他的部署一致的方式。 如需有關 Azure 資源管理員及範本的詳細資訊，請參閱[資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>部署 Azure Docker VM 副檔名為範本
讓我們來使用現有的快速入門範本來建立 Ubuntu VM 安裝和設定 Docker 主機使用 Azure Docker VM 副檔名。 您可以檢視的範本︰[簡單部署與 Docker Ubuntu VM](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)。 

您需要[最新的 Azure CLI](../xplat-cli-install.md)安裝及登入的資源管理員模式，如下所示︰

```
azure config mode arm
```

部署使用 Azure CLI 中，指定 URI 範本的範本。 下列範例會建立資源群組名稱`myResourceGroup`中`WestUS`位置。 請使用您自己的資源群組名稱和位置，如下所示︰

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

回答提示您儲存的帳戶的名稱、 提供使用者名稱和密碼，並提供 DNS 名稱。 輸出非常類似以下的範例︰

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

提示幾秒鐘，但您 Docker 主機後仍在所建立並設定 Azure Docker VM 副檔名 Azure CLI 傳回。 需要幾分鐘，完成部署。 您可以檢視詳細資料 Docker 主機狀態使用`azure vm show`] 命令。

下列範例會檢查是否有的名稱為 VM 狀態`myDockerVM`（從範本的預設名稱不變更此名稱） 的資源群組中名為`myResourceGroup`。 輸入您在上述步驟建立資源群組的名稱︰

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

輸出`azure vm show`] 命令會類似下面的範例︰

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

輸出的上方，查看`ProvisioningState`的 VM。 當這會顯示`Succeeded`部署已完成，您可以 SSH vm。

最後的輸出`FQDN`會顯示您 Docker 主機的完整的網域名稱。 這個 FQDN 是什麼您用來 SSH Docker 主機中其餘的步驟。


## <a name="deploy-your-first-nginx-container"></a>部署您的第一個 nginx 容器
部署完成之後，SSH 您新 Docker 主機從本機電腦。 請輸入您自己的使用者名稱和 FQDN，如下所示︰

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

一旦 Docker 主機登入，讓我們來執行 nginx 容器︰

```
sudo docker run -d -p 80:80 nginx
```

下載 nginx 圖像，並啟動容器，輸出非常類似下面的範例︰

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

檢查您的 Docker 主機上執行的如下所示的容器的狀態︰

```
sudo docker ps
```

輸出類似下面的範例，顯示 nginx 容器執行，並且 TCP 連接埠 80 和 443 及轉寄︰

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

若要查看您的容器的實際操作，開啟網頁瀏覽器，然後輸入您的 Docker 主機 FQDN 名稱︰

![執行 ngnix 容器](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 副檔名範本參照
上一個範例使用現有的快速入門範本。 您也可以使用您自己的資源管理員範本部署 Azure Docker VM 副檔名。 若要這麼做，請將下列資源管理員範本，定義`vmName`您 VM 的適當︰

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

您可以找到詳細的逐步解說，使用資源管理員範本閱讀[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。


## <a name="next-steps"></a>後續步驟
您可能會想要[設定 Docker 精靈 TCP 連接埠](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket)、 瞭解[Docker 安全性](https://docs.docker.com/engine/security/security/)]，或部署容器使用[Docker 撰寫](https://docs.docker.com/compose/overview/)。 如需有關 Azure Docker VM 副檔名為本身的詳細資訊，請參閱[GitHub 專案](https://github.com/Azure/azure-docker-extension/)。

閱讀關於 Azure 中的其他 Docker 部署選項的詳細資訊︰

- [使用 Docker 機器 Azure 驅動程式](./virtual-machines-linux-docker-machine.md)  
- [開始使用 Docker 與撰寫來定義並執行 Azure 虛擬機器上的多重容器應用程式](virtual-machines-linux-docker-compose-quickstart.md)。
- [部署 Azure 容器服務叢集](../container-service/container-service-deployment.md)
