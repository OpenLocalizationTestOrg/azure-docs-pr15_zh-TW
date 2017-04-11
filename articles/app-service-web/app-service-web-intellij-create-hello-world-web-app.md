<properties 
    pageTitle="建立在 IntelliJ Azure 認識全球 Web 應用程式 |Microsoft Azure" 
    description="本教學課程教您如何使用 Azure 工具組的 IntelliJ 建立 Hello 全球 Web 應用程式的 Azure。" 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-intellij"></a>建立在 IntelliJ Azure 認識全球 Web 應用程式

本教學課程中會顯示如何建立並部署基本什麼應用程式來 Azure 為 Web 應用程式使用[的 IntelliJ Azure 工具組]。 基本 JSP 範例顯示簡單，但非常類似的步驟可適用於 Java servlet，就而言 Azure 部署。

當您完成本教學課程中時，您的應用程式時看起來會類似下圖中您在網頁瀏覽器中檢視︰

![][01]
 
## <a name="prerequisites"></a>必要條件

* Java 開發人員套件 (JDK)，v 1.8 或更新版本。
* IntelliJ 想法的最終版本。 這樣就可以從<https://www.jetbrains.com/idea/download/index.html>下載。
* Java 的網頁伺服器或應用程式的伺服器，例如 Apache Tomcat 或 Jetty 的通訊群組。
* Azure 訂閱，可以從<https://azure.microsoft.com/free/>或<http://azure.microsoft.com/pricing/purchase-options/>取得。
* IntelliJ Azure 工具組。 如需詳細資訊，請參閱[安裝的 IntelliJ Azure 工具組]。

## <a name="to-create-a-hello-world-application"></a>若要建立認識全球應用程式

首先，我們會先建立 Java 專案。

1. 開始 IntelliJ，並在功能表按一下 [**檔案**]，然後**新增**]，然後按一下**專案**。

   ![][02]

1. 在 [新專案] 對話方塊中，選取**Java**，然後**Web 應用程式**，然後再按 [**下一步**。

   ![][03a]

   如果提示您繼續進行指派沒有 SDK，請按一下 [**是**]。

   ![][03b]

1. 為了本教學課程， **Java-Web-應用程式-上-Azure**，為專案的名稱，然後按一下**完成**。

   ![][04]

1. 內 IntelliJ 的專案總管] 檢視中，展開**Java-Web-應用程式-上-Azure**，然後展開**網頁**，然後按兩下**index.jsp**。

   ![][05]

1. 當您 index.jsp 檔案開啟 IntelliJ 時，新增動態顯示文字中**Hello World ！** 在現有的`<body>`項目。 您更新`<body>`內容應該類似下列範例︰

   `<body><b><% out.println("Hello World!"); %></b></body>` 

1. 儲存 index.jsp。

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>部署至 Azure Web 應用程式容器應用程式

有數種方式，您可以依據部署 Azure Java web 應用程式。 本教學課程說明最其中一項︰ 您的應用程式部署至 Azure Web 應用程式容器-沒有特殊專案類型或其他工具所需。 JDK 和 web 容器軟體會提供可讓您以 Azure，因此不需要上傳您自己的;您只需要是 Java Web 應用程式。 如此一來，您的應用程式的發佈程序會秒數，而不是分鐘。

1. 在 IntelliJ 的 [專案總管] 中以滑鼠右鍵按一下**Java-Web-應用程式-上-Azure**專案。 當出現快顯功能表時，請選取**Azure**，然後按一下 [**發佈為 Azure Web 應用程式...**

   ![][06]

1. 如果您已經無法登入 IntelliJ 從 Azure，系統會提示您登入 Azure 帳戶︰

   ![][07]

   注意︰ 如果您有多個 Azure 帳戶，一些提示登入程序期間可能會顯示一次以上，即使顯示相同。 發生這種情況，請繼續追蹤登入指示進行。

1. 您順利登入您的 Azure 帳戶之後，[**管理訂閱**] 對話方塊會顯示您的認證與相關聯的訂閱清單。 如果有多個訂閱列出，而您想要使用的特定子集這些，您可能會選擇性地取消核取您想使用的項目。 當您選取了您的訂閱時，請按一下 [**關閉**]。

   ![][08]

1. **部署至 Azure Web 應用程式容器**] 對話方塊出現時，它將會顯示任何 Web App 容器您先前所建立。如果您還沒有建立任何容器，清單會是空的。   

   ![][09]

1. 如果您還沒有建立一個 Azure Web 應用程式的容器之前，或您想要發佈至新的容器的應用程式，請使用下列步驟。 否則，選取 [現有的 Web 應用程式容器，直接跳到步驟 6 下方。

  1. 按一下 [**+**

        ![][10]

  1. [**新的 Web 應用程式容器**] 對話方塊隨即出現，它將會用於後續幾個步驟。

        ![][11]

  1. 輸入您的 Web 應用程式容器; **DNS 標籤**這將會 Azure 中形成分葉 DNS 主機 URL web 應用程式的標籤。 附註︰ 名稱必須可用，而且符合命名需求 Azure Web 應用程式。

  1. 在**Web 容器**下拉式功能表，選取適當的軟體應用程式。

        目前，您可以選擇從 Tomcat 8、 Tomcat 7 或 Jetty 9。 將由 Azure 提供最新的通訊群組的所選的軟體，它會在 [最近分配 JDK 8 建立 Oracle 和 Azure 所提供的上執行。

  1. 在**訂閱**下拉式功能表，選取您想要使用此部署的訂閱。

  1. 在 [**資源群組**] 下拉式清單功能表中，選取您要建立 Web 應用程式之間的關聯的 [資源] 群組。

        附註︰ Azure 資源群組可讓您要分成相關的資源這麼一來，例如，他們可以刪除在一起。

        您可以選取現有的資源群組 （如果您有任何） 並略過步驟 g，或使用下列步驟來建立新的資源群組下列︰

      * 按一下 [**新增...**

      * 會顯示**新的資源群組**] 對話方塊︰

            ![][12]

      * 在 [**名稱**] 文字方塊中，指定新的資源群組的名稱。

      * 在 [**地區**下拉功能表中，選取適當的 Azure 資料中心的資源群組的位置。

      * 按一下**[確定]**。

  1. **應用程式服務方案**下拉式功能表列出您所選的資源群組相關聯的應用程式服務方案。

        附註︰ 應用程式服務方案指定 Web 應用程式、 價格層及計算執行個體大小的資訊，例如位置。 單一的應用程式服務方案可用於多個 Web 應用程式，也就是它從特定的 Web 應用程式部署分別維護的原因。

        您可以選取現有應用程式服務方案 （如果您有任何） 並略過步驟 h 下方，或使用下列步驟來建立新的應用程式服務方案的下列︰

      * 按一下 [**新增...**

      * 會顯示**新的應用程式服務計劃**] 對話方塊︰

            ![][13]

      * 在 [**名稱**] 文字方塊中，指定您新增的應用程式服務方案的名稱。

      * 在 [**位置**下拉功能表中，選取適當的 Azure 資料中心的計劃的位置。

      * 在 [**價格層**下拉式] 功能表中，選取適當的價格計劃。 供測試之用，您可以選擇**可用**。

      * 在**執行個體大小**下拉式功能表中，選取適當的執行個體的計劃的大小。 如需進行測試您可以選擇**小**。

  1. 當您完成上述步驟之後時，新的 Web 應用程式容器] 對話方塊應該類似下圖︰

        ![][14]

  1. 按一下**[確定**] 以完成建立新 Web 應用程式容器。

        等待幾秒重新整理，Web App 容器的清單，現在應該] 清單中選取您新建立的 web 應用程式的容器。

1. 您已準備好要完成初始 Azure; 您的 Web 應用程式的部署按一下 [部署至所選的 Web 應用程式容器 Java 應用程式的**[確定]** 。

    ![][15]

    附註︰ 根據預設，您的應用程式會部署為應用程式伺服器的子目錄。 如果您想要作為根應用程式部署，核取 [**部署至根**核取方塊，再按一下**[確定]**。

1. 接下來，您應該會看到 [ **Azure 活動記錄**] 檢視，即表示您的 Web 應用程式的部署狀態。

    ![][16]

    只有幾秒鐘完成時，應該進行部署 Azure Web 應用程式的程序。 當您的應用程式可用，您會看到 [**狀態**] 欄中名為**發佈**的連結。 當您按一下連結時，，會將您帶往您部署的 Web App 首頁，或您也可以在 [動作] 區段中使用的步驟，瀏覽至您的 web 應用程式。

## <a name="browsing-to-your-web-app-on-azure"></a>瀏覽功能至 Web 應用程式上 Azure

若要瀏覽至您的 Web 應用程式上 Azure，您可以使用**Azure 總管**檢視。

如果**Azure 總管**檢視尚未開啟，您可以將其開啟，然後按一下 [**檢視**] 功能表中 IntelliJ，然後按一下 [**工具視窗**，，然後按一下 [**服務檔案總管**。 如果您先前未登入，就會提示您執行此作業。

顯示 [ **Azure 檔案總管**] 檢視時，使用就會遵循下列步驟，若要停止 Web 應用程式︰ 

1. 展開 [ **Azure**節點。

1. 展開 [ **Web 應用程式**節點。 

1. 以滑鼠右鍵按一下您要的 Web 應用程式。

1. 出現快顯功能表，請按一下 [**瀏覽器中開啟**]。

    ![][17]

## <a name="updating-your-web-app"></a>更新您的 Web 應用程式

更新現有的執行 Azure Web 應用程式快速又簡單的程序，而且您有更新的兩個選項︰

* 您可以更新現有的 Java Web 應用程式的部署。
* 您可以發佈至相同的 Web 應用程式容器的其他 Java 應用程式。

在任一情況，程序相同，並需要只幾秒鐘︰

1. 在 [IntelliJ 專案總管] 中，以滑鼠右鍵按一下您想要更新或新增至現有的 Web 應用程式容器 Java 應用程式。

1. 出現快顯功能表中，選取 [ **Azure** ]，然後按一下 [**發佈為 Azure Web 應用程式...**

1. 因為您已登入之前，您會看到您現有的 Web 應用程式容器的清單。 選取您想要發佈或重新發佈 Java 應用程式，並按一下**[確定]**。

之後，數秒**Azure 活動記錄**檢視會顯示您更新的部署**發佈**為，然後您可以驗證在網頁瀏覽器中的應用程式更新。

## <a name="starting-or-stopping-an-existing-web-app"></a>啟動或停止現有的 Web 應用程式

啟動或停止現有的 Azure Web App 容器，（包括所有的部署的 Java 應用程式中），您可以使用 [ **Azure 檔案總管**] 檢視。

如果**Azure 總管**檢視尚未開啟，您可以將其開啟，然後按一下 [**檢視**] 功能表中 IntelliJ，然後按一下 [**工具視窗**，，然後按一下 [**服務檔案總管**。 如果您先前未登入，就會提示您執行此作業。

顯示 [ **Azure 檔案總管**] 檢視時，使用請遵循這些步驟來啟動或停止 Web 應用程式︰ 

1. 展開 [ **Azure**節點。

1. 展開 [ **Web 應用程式**節點。 

1. 以滑鼠右鍵按一下您要的 Web 應用程式。

1. 操作功能表出現時，按一下 [**啟動**或**停止**]。 請注意，功能表選項知道有內容，讓您只可以停止執行的 web 應用程式，或啟動目前不在執行 web 應用程式。

    ![][18]

## <a name="next-steps"></a>後續步驟

Java Ide 的 Azure 工具套件的相關詳細資訊，請參閱下列連結︰

- [針對蝕 azure 工具組]
  - [安裝蝕 Azure 工具組]
  - [建立 Azure 中蝕認識全球 Web 應用程式]
  - [Azure 工具組蝕的新增功能]
- [針對 IntelliJ azure 工具組]
  - [安裝 IntelliJ Azure 工具組]
  - *Azure IntelliJ （本文） 中建立的認識全球 Web 應用程式*
  - [Azure 工具組 IntelliJ 的新增功能]

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心]。

如需有關建立 Azure Web 應用程式的詳細資訊，請參閱[Web 應用程式概觀]。

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[針對蝕 azure 工具組]: ../azure-toolkit-for-eclipse.md
[針對 IntelliJ azure 工具組]: ../azure-toolkit-for-intellij.md
[建立 Azure 中蝕認識全球 Web 應用程式]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[安裝蝕 Azure 工具組]: ../azure-toolkit-for-eclipse-installation.md
[安裝 IntelliJ Azure 工具組]: ../azure-toolkit-for-intellij-installation.md
[Azure 工具組蝕的新增功能]: ../azure-toolkit-for-eclipse-whats-new.md
[Azure 工具組 IntelliJ 的新增功能]: ../azure-toolkit-for-intellij-whats-new.md

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[Web 應用程式概觀]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png
