<properties 
  pageTitle="部署私人 Docker 登錄，在 Azure |Microsoft Azure"
  description="說明如何使用 Docker 登錄裝載您容器上的圖像 Azure Blob 儲存體服務。"
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>部署私人 Docker 登錄，在 Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



這份文件將說明哪些 Docker 私人登錄並顯示如何在 Microsoft Azure 使用 Azure Blob 儲存體 Docker 私人登錄部署 Docker 登錄 2.0 容器圖像。

假設此文件︰

1. 您已經知道如何使用 Docker，並以 Docker 儲存的圖像。 （您沒有嗎？ [深入了解 Docker](https://www.docker.com)）
2. 您必須已安裝的 Docker 引擎的伺服器。 （您沒有嗎？ [快速 Azure 上執行。](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>什麼是私人的 Docker 登錄？

若要將運送至雲端編應用程式，您建構 Docker 容器圖像，將其儲存位置，讓它可以用自己與其他人。 

輕鬆建立容器圖像，並將其傳送至雲端時，則儲存所產生的影像可靠的挑戰。 因此，Docker 提供集中式的服務稱為[Docker 中心][docker-hub]儲存容器在雲端上的圖像，並可讓您建立容器隨時隨地使用這些圖像。

雖然[Docker 中心][docker-hub]付費的服務來儲存您私人的應用程式的容器圖像，Docker 遵循開發人員的需求及提供儲存圖像防火牆自己私人 Docker 登錄中開啟來源工具組或內部部署不正中公用網際網路。
因為 Azure Blob 儲存體是容易保護，您可以建立及使用私人的 Docker 登錄，您可以控制您自己的 Azure 中快速使用它。

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>為什麼應該裝載 Azure Docker 登錄？

主機服務上 Microsoft Azure Docker 登錄執行個體，並將圖像儲存在 Azure Blob 儲存體，您可能會有幾項優點︰

**安全性︰**Docker 圖像不要讓 Azure 資料中心，讓它們無法執行一樣，如果您使用 Docker 中心交互公用網際網路。
  
**效能︰**Docker 圖像會儲存在相同的資料中心或地區，為您的應用程式。 這表示會更快且更可靠比較 Docker 中心提取圖像。

**可靠性︰**藉由使用 Microsoft Azure Blob 儲存體，您可以讓使用許多儲存空間屬性，例如高的顯示狀態，重複，進階儲存空間 (SSDs)，依此類推。

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>設定 Docker 登錄使用 Azure Blob 儲存體

（建議您閱讀[Docker 登錄 2.0 文件][登錄文件]之前。）

您可以[設定][ registry-config] Docker 登錄兩種方式。
您可以︰

1. 使用`config.yml`檔案。 您需要在此情況下建立個別的 Docker 圖像上方的`registry`圖像。
2. 覆寫預設設定檔，透過環境變數︰ 取得建立和維護個別的 Docker 圖像不完成的項目。

為方便，本主題後面選項 2，使用環境變數。

若要執行 Docker 登錄執行個體的︰

* 使用您的 Azure 儲存體帳戶儲存圖像
* 接聽虛擬機器的連接埠 5000
* 不有設定任何驗證 （不建議使用，請參閱下列附註）

您需要執行下列 Docker 命令中艦隊終端機 (取代`<storage-account>`和`<storage-key>`與您的認證):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

一旦命令結束時，您可以看到執行裝載您私人的 Docker 登錄執行個體的容器`docker ps`您主機上的命令︰

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] 此文件中未涵蓋設定 Docker 登錄機碼的安全性，登錄會凡是沒有驗證，依預設，如果您開啟登錄上的連接埠的虛擬機器端點的連接埠或負載平衡器，如果您是使用上方的 [部署] 命令。
>
> 請閱讀[設定 Docker 登錄][registry-config]若要瞭解如何保護登錄執行個體及圖像的文件。

## <a name="next-steps"></a>後續步驟

一旦您有登錄設定，則到使用其他的時間。 Docker[登錄文件]的開頭。 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[登錄文件]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 
