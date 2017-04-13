<properties
   pageTitle="Azure Docker 電腦中建立 Docker 主機 |Microsoft Azure"
   description="說明 Docker Machine 以建立 docker 主機 Azure 中使用。"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Azure Docker 電腦中建立 Docker 主機

執行[Docker](https://www.docker.com/)容器需要主機 VM 執行 docker 精靈。
本主題說明如何使用[docker 電腦](https://docs.docker.com/machine/)命令來建立新的 Linux Vm Docker 精靈中，執行 Azure 中使用設定。 

**附註︰** 
- *本文取決於 docker 機器版本 0.7.0 大於或等於*
- *Windows 容器將支援透過 docker 機器不久*

## <a name="create-vms-with-docker-machine"></a>建立 Vm Docker 機器

建立與 Azure docker 主機 Vm`docker-machine create`命令使用`azure`驅動程式。 

Azure 驅動程式需要您的訂閱 id。 若要擷取您 Azure 的訂閱，您可以使用[Azure CLI](xplat-cli-install.md)或[Azure 入口網站](https://portal.azure.com)。 

**使用 [Azure 入口網站**
- 選取訂閱左側導覽畫面中頁面上，並複製訂閱識別碼。

**使用 Azure CLI**
- 輸入```azure account list```複製訂閱識別碼。

輸入`docker-machine create --driver azure`若要查看的選項和其預設值。
您也可以查看[Docker Azure 驅動程式文件](https://docs.docker.com/machine/drivers/azure/)的詳細資訊。 

下列範例會依賴預設值，但，您也可以選擇並開啟連接埠 80 網際網路存取 VM 上。 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>選擇 docker 主機 docker 機器
一旦主機的 docker 電腦中有一個項目，您可以在執行 docker 命令時，設定預設主機。
##<a name="using-powershell"></a>使用 PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>使用艦隊

```bash
eval $(docker-machine env MyDockerHost)
```

您現在可以針對指定的主機執行 docker 命令

```
docker ps
docker info
```

## <a name="run-a-container"></a>執行容器

使用 [主機設定，您現在可以執行簡單的網頁伺服器測試是否已正確地設定您的主機。
以下我們使用標準 nginx 圖像，指定應該聆聽連接埠 80 和主機 VM 重新啟動時，如果容器會重新啟動以及 (`--restart=always`)。 

```bash
docker run -d -p 80:80 --restart=always nginx
```

輸出看起來應該如下所示︰

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>測試容器

檢查執行容器使用`docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

若要查看執行容器，輸入的核取`docker-machine ip <VM name>`若要尋找在瀏覽器中輸入的 IP 位址︰

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![執行 ngnix 容器](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>摘要
與 docker 電腦您可以輕鬆地為您的個別 docker 主機驗證提供 docker 主機 Azure 中。
生產裝載的容器，請參閱[Azure 容器服務](http://aka.ms/AzureContainerService)

若要開發 Visual studio.NET 核心應用程式，請參閱[Visual Studio Docker 工具](http://aka.ms/DockerToolsForVS)
