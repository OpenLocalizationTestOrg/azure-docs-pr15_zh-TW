<properties
   pageTitle="使用 C# Linux 上建立您的第一個服務布料的轉印圖樣應用程式 |Microsoft Azure"
   description="建立並部署使用 C 的服務布料的轉印圖樣應用程式#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>建立您的第一個 Azure 服務布料的轉印圖樣應用程式

> [AZURE.SELECTOR]
- [C#-Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java-Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C#-Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

服務布料的轉印圖樣提供 Sdk 的.NET 核心和 Java linux 建置服務。 在本教學課程中，我們看看如何建立 Linux 應用程式，並建立使用 C# （.NET 核心） 服務。

## <a name="prerequisites"></a>必要條件

在您開始之前，請確定您已[設定 Linux 開發環境](service-fabric-get-started-linux.md)。 如果您使用的 Mac OS X，您可以[設定使用 Vagrant 虛擬機器中的 Linux 一個方塊環境](service-fabric-get-started-mac.md)。

## <a name="create-the-application"></a>建立應用程式

服務布料的轉印圖樣應用程式可以包含一或多個服務，每一特定角色中進行應用程式的功能。 服務布料的轉印圖樣 SDK Linux 包含可讓您輕鬆建立您的第一個服務，並在稍後加入更多的[Yeoman](http://yeoman.io/)產生器。 讓我們來使用 Yeoman 建立單一服務應用程式。

1. 在 terminal 中，輸入下列命令以開始建立臨時平台︰`yo azuresfcsharp`

2. 您的應用程式的名稱。

3. 選擇您的第一個服務類型，並將其命名。 在進行本教學課程中，選擇 [可靠的動作項目服務。

  ![C 服務布料的轉印圖樣 Yeoman 產生器#][sf-yeoman]

>[AZURE.NOTE] 如需選項的詳細資訊，請參閱[服務布料的轉印圖樣程式設計模型概觀](service-fabric-choose-framework.md)。

## <a name="build-the-application"></a>建置應用程式

服務布料的轉印圖樣 Yeoman 範本包含建置指令碼，您可以用來建立終端機中的應用程式 （後瀏覽至 [應用程式] 資料夾）。

  ```bash
 cd myapp 
 ./build.sh 
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>開始測試用戶端，並執行容錯移轉

動作項目專案做上自己的任何內容。 需要其他服務或傳送訊息給他們的用戶端。 動作項目範本包含的簡單的測試指令碼，您可以使用互動的動作項目服務。

1. 執行使用監看式公用程式，若要查看的動作項目服務輸出的指令碼。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. 在服務布料的轉印圖樣總管] 中，找出節點所在的動作項目服務的主要複本。 在以下的螢幕擷取畫面，則節點 3。

    ![在服務布料的轉印圖樣總管] 中找到的主要複本][sfx-primary]

3. 按一下您在上一個步驟中，找到節點，然後從 [動作] 功能表中選取 [**停用] （重新啟動）** 。 這個動作會重新啟動其中一個五個節點本機叢集強制容錯移轉至另一個節點上執行第二個複本中。 當您執行此動作，請注意，輸出測試用戶端和計數器繼續遞增還是容錯移轉的筆記。


## <a name="next-steps"></a>後續步驟

- [深入瞭解可靠的動作項目](service-fabric-reliable-actors-introduction.md)
- [與使用 Azure CLI 服務布料的轉印圖樣叢集互動](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
