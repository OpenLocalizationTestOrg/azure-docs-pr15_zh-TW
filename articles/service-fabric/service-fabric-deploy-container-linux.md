<properties
   pageTitle="服務布料的轉印圖樣及部署容器中 Linux |Microsoft Azure"
   description="服務布料的轉印圖樣及使用 Docker 容器的部署 microservice 應用程式。 本文將說明服務架構提供容器的功能，以及如何將叢集部署 Docker 容器影像"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>將 Docker 容器部署至服務布料的轉印圖樣

> [AZURE.SELECTOR]
- [部署 Windows 容器](service-fabric-deploy-container.md)
- [部署 Docker 容器](service-fabric-deploy-container-linux.md)

本文會引導您建置 Linux 編的 Docker 容器中的服務。

服務布料的轉印圖樣有數種容器功能可協助您建置組成的應用程式的 microservices 的是編。 這些稱為編的服務。

功能包括︰

- 容器圖像部署和啟動
- 資源管理
- 存放庫驗證
- Host （主機） 連接埠對應至容器連接埠
- 容器的容器探索及通訊
- 若要設定，並設定環境變數的功能


## <a name="packaging-a-docker-container-with-yeoman"></a>使用 yeoman 包裝 docker 容器
當包裝上 Linux 容器，您可以選擇使用 yeoman 範本或[以手動方式建立應用程式套件](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container)。

服務布料的轉印圖樣應用程式可以包含一或多個容器，每一特定角色中進行應用程式的功能。 服務布料的轉印圖樣 SDK Linux 包含可讓您輕鬆地建立您的應用程式，新增容器圖像的[Yeoman](http://yeoman.io/)產生器。 現在就讓我們來建立新的應用程式與單一 Docker 容器稱為*SimpleContainerApp*使用 Yeoman。 您可以新增更多服務稍後編輯產生資訊清單檔案。

## <a name="create-the-application"></a>建立應用程式

1. 在 terminal 中，輸入**yo azuresfguest**。

2. 架構選擇 [**容器**]。

3. 命名例如 SimpleContainerApp 應用程式

4. 從 DockerHub repo 容器影像提供 URL。 這樣的形式 [repo] / [影像名稱]

![容器的服務布料的轉印圖樣 Yeoman 產生器][sf-yeoman]

## <a name="deploy-the-application"></a>部署應用程式

應用程式建立後，您可以將其部署到本機叢集使用 Azure CLI。

1. 連線到本機服務布料的轉印圖樣叢集。

    ```bash
    azure servicefabric cluster connect
    ```

2. 使用範本中提供的安裝指令碼應用程式套件叢集的圖像存放區，請註冊應用程式類型，然後建立應用程式的執行個體。

    ```bash
    ./install.sh
    ```

3. 開啟瀏覽器，然後瀏覽至服務布料的轉印圖樣總管在 http://localhost:19080 Explorer （如果使用 Mac OS X 上的 [Vagrant VM 的私用的 ip 取代本機）。

4. 展開 [應用程式] 節點，請注意，現在您的應用程式類型的項目，另一個用於該類型的第一個執行個體。

5. 使用範本中提供的解除安裝指令碼，刪除的應用程式執行個體，並解除登錄應用程式類型。

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>後續步驟

- [服務布料的轉印圖樣及容器的概觀](service-fabric-containers-overview.md)
- [與使用 Azure CLI 服務布料的轉印圖樣叢集互動](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

