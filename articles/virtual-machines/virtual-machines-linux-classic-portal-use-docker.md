<properties
    pageTitle="使用 Linux Docker VM 副檔名 |Microsoft Azure"
    description="描述 Docker 和 Azure 虛擬機器擴充功能，以及如何建立使用傳統的部署模型中的 Azure CLI docker 主機的 Azure 虛擬機器。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Azure 傳統入口網站中使用 Docker VM 副檔名

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/)是其中一個最常用的區隔資料並計算共用資源的方式使用[Linux 容器](http://en.wikipedia.org/wiki/LXC)，而不是虛擬機器虛擬化方法。 您可以使用 [管理[Azure Linux 代理程式]的副檔名為 Docker VM 建立 Docker VM 裝載您的應用程式上 Azure 容器的任何數字。

> [AZURE.NOTE] 本主題說明如何建立 Docker VM 從 Azure 傳統入口網站。 若要查看如何建立 Docker VM 在命令列，請參閱[如何使用 Docker VM 副檔名從 Azure 命令列介面 (Azure CLI)]。 若要查看高層級討論的容器及他們的優點，請參閱[Docker 高等級白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)。

## <a name="create-a-new-vm-from-the-image-gallery"></a>從 [圖像] 庫中建立新的 VM
第一步需要從支援 Docker VM 延伸圖像庫中使用 Ubuntu 14.04 LTS 圖像作為範例伺服器圖像和 Ubuntu 14.04 桌面用戶端的 Linux 圖像 Azure VM。 在入口網站中，按一下**[+ 新增**中建立新的 VM 執行個體，並從可用選項或完成的圖像庫中，選取 Ubuntu 14.04 LTS 圖像左下角，如下所示。

> [AZURE.NOTE] 目前，更新於 2014 年 7 月只 Ubuntu 14.04 LTS 圖像支援 Docker VM 延伸。

![建立新的 Ubuntu 圖像](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>建立 Docker 憑證

在建立 VM 之後，請確定 Docker 用戶端電腦上安裝。 （如需詳細資訊，請參閱[Docker 安裝指示](https://docs.docker.com/installation/#installation)）。

建立根據[Https 的執行 Docker] Docker 通訊的憑證和金鑰檔案，並將它們在**`~/.docker`**目錄用戶端電腦上。

> [AZURE.NOTE] Docker VM 入口網站中目前需要 base64 編碼的認證。

在命令列中，使用**`base64`**或另一個最愛的編碼工具來建立 base64 編碼的主題。 執行此動作以簡單的憑證和金鑰檔案看起來可能像這樣︰

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>新增 Docker VM 副檔名
若要新增 Docker VM 副檔名，找出您建立的 VM 執行個體和向下**延伸**捲動並按一下以顯示 VM 擴充功能，如下所示。
> [AZURE.NOTE] 在預覽入口網站中僅支援此功能︰ https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>加入副檔名
按一下**+ 新增**以顯示您可以新增此 vm 可能 VM 副檔名。

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>選取 Docker VM 副檔名
選擇 Docker VM 副檔名，會顯示 [Docker 描述和重要的連結，然後按 [**建立**底部開始安裝程序。

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>新增您的憑證和主要的檔案︰

在表單欄位中，輸入 base64 編碼的版本您 CA 的憑證，您的伺服器憑證，而您伺服器的金鑰下, 圖所示。

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] 請注意，（如上述圖像） 2376年填入預設。 您可以輸入任何端點，但下一個步驟會以開啟相符的結束點。 如果您變更預設值，請確定開啟相符的結束點，在下一個步驟。

## <a name="add-the-docker-communication-endpoint"></a>新增 Docker 通訊端點
當您檢視您已建立的 [資源] 群組中，選取您的 VM 相關聯的網路安全性群組，然後按一下 [**輸入安全性規則**來檢視的規則，如下所示。

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

按一下 [ **+ 新增]**以新增另一個規則，並在預設大小寫，輸入 （在此範例中， **Docker**），結束點名稱和 2376年 ' 目的地連接埠範圍]。 設定顯示**TCP**，[通訊協定] 的值，然後按一下**[確定**] 以建立規則。

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>測試您的 Docker 用戶端和 Azure Docker host （主機）
找到並複製您 VM 的網域，然後在您的用戶端電腦，類型的命令列名稱`docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` （位置*dockerextension*會由子網域為您 VM）。

結果看起來應該像這樣︰

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

當您完成上述步驟時，您現在已設定為從遠端從其他用戶端連線至 Azure VM 上執行的完整功能 Docker 主機。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

您準備好移至[Docker 使用者指南]並使用您 Docker VM。 如果您想要自動化建立 Docker 主機 Azure Vm 上的透過命令列介面，請參閱[如何使用 Docker VM 副檔名從 Azure 命令列介面 (Azure CLI)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[如何使用 Docker VM 副檔名從 Azure 命令列介面 (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux 代理程式]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[執行 Docker https]: http://docs.docker.com/articles/https/
[Docker 使用者指南]: https://docs.docker.com/userguide/
