<properties 
    pageTitle="偵錯 Java Web App 中 IntelliJ Azure 上的 |Microsoft Azure" 
    description="本教學課程教您如何使用 Azure 工具組 IntelliJ 的偵錯 Azure 上執行 Java Web 應用程式。" 
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
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>偵錯 Java Web App 中 IntelliJ Azure 上

本教學課程中會顯示如何偵錯上 Azure 執行使用[Azure 工具組的 IntelliJ]Java Web 應用程式。 為方便，您會在此教學課程中，使用基本的 Java 伺服器網頁 (JSP) 範例，但步驟會類似的 Java servlet，當您在 Azure 偵錯。

當您完成本教學課程中時，您的應用程式時看起來會類似下圖中您要在 IntelliJ 偵錯︰

![][01]
 
## <a name="prerequisites"></a>必要條件

* Java 開發人員套件 (JDK)，v 1.8 或更新版本。
* IntelliJ 想法的最終版本。 這樣就可以從<https://www.jetbrains.com/idea/download/index.html>下載。
* Java 的網頁伺服器或應用程式的伺服器，例如 Apache Tomcat 或 Jetty 的通訊群組。
* Azure 訂閱，可以從<https://azure.microsoft.com/en-us/free/>或<http://azure.microsoft.com/pricing/purchase-options/>取得。
* IntelliJ Azure 工具組。 如需詳細資訊，請參閱[安裝的 IntelliJ Azure 工具組]。
* 動態 Web 專案建立並部署至 Azure 應用程式服務;例如，請參閱[建立 Hello 全球 Web 應用程式中 IntelliJ Azure]。

## <a name="to-debug-a-java-web-app-on-azure"></a>若要偵錯上 Azure Java Web 應用程式

若要完成這些步驟，在此區段中的，您可以使用現有的動態 Web 專案的已部署為上 Azure Java Web 應用程式，您下載的[範例動態 Web 專案]，然後依照[建立 Hello 全球 Web 應用程式中 IntelliJ Azure]部署 Azure 中的步驟進行。 

1. 開啟您部署至 Azure 中 IntelliJ Java Web App 專案。

1. 按一下 [**執行**] 功能表，然後按一下 [**編輯設定]**。

    ![][02]

1. 當**執行/偵錯設定**] 對話方塊隨即開啟︰ 

    1. 選取**Azure Web 應用程式**。
    1. 按一下 [上**+**若要新增新的設定。
    1. 設定中提供的**名稱**。
    1. 接受的剩餘的 Azure 工具組，會看到建議的預設值，然後按一下**[確定]**。

        ![][03]

1. 選取 Azure Web 應用程式偵錯組態您剛建立右頂端的功能表上，按一下**偵錯**

    ![][04]

1. 當畫面提示您**啟用遠端偵錯在遠端 Web 應用程式現在？**，按一下**[確定]**。

1. 當系統提示**您的 web 應用程式現在已準備好進行遠端偵錯**的，請按一下**[確定]**。

    ![][05]

1. 選取您剛剛建立的右頂端的 [] 功能表的 Azure Web 應用程式偵錯組態，然後按一下**偵錯**。

1. 在 Windows 命令提示字元或 Unix 殼層會開啟，並準備偵錯; 必要的連線必須等到遠端 Java Web 應用程式的連線已成功，再繼續進行。 如果您使用的 Windows，它看起來類似下列圖例中︰

    ![][06]

1. 在您 JSP] 頁面上插入符號點，然後開啟 [URL]，請在瀏覽器中 Java Web 應用程式︰

    1. **Azure 檔案總管**中 IntelliJ 開啟。
    1. 瀏覽至**Web 應用程式**，並想要偵錯 Java Web 應用程式。
    1. 在 [Web 應用程式中，按一下滑鼠右鍵，然後按一下 [**瀏覽器中開啟**。
    1. IntelliJ 現在會輸入偵錯模式。

## <a name="next-steps"></a>後續步驟

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心]。

如需有關建立 Azure Web 應用程式的詳細資訊，請參閱[Web 應用程式概觀]。

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[針對 IntelliJ azure 工具組]: ../azure-toolkit-for-intellij.md
[安裝 IntelliJ Azure 工具組]: ../azure-toolkit-for-intellij-installation.md
[建立在 IntelliJ Azure 認識全球 Web 應用程式]: ./app-service-web-intellij-create-hello-world-web-app.md
[範例動態網站專案]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[Web 應用程式概觀]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
