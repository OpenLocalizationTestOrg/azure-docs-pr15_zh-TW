<properties
   pageTitle="偵錯本機 Docker 容器中的應用程式 |Microsoft Azure"
   description="了解如何修改本機 Docker 容器中執行的應用程式、 編輯及重新整理透過容器的重新整理及設定偵錯中斷點"
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
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>偵錯本機 Docker 容器中的應用程式

## <a name="overview"></a>概觀
Visual Studio 工具的 Docker 提供一致的方式，用來在開發和驗證本機 Linux Docker 容器中的應用程式。
您不需要重新啟動容器每次您做變更的程式碼。
本文將說明如何使用 「 編輯並重新整理] 功能來啟動本機 Docker 容器中的 [ASP.NET 核心 Web 應用程式、 進行任何必要的變更，然後重新整理瀏覽器，請參閱這些變更。
它也會告訴您如何設定偵錯中斷點。

> [AZURE.NOTE] Windows 容器支援將即將在未來版本

## <a name="prerequisites"></a>必要條件
需要安裝下列工具。

- [Visual Studio 2015 更新 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- 安裝[Visual Studio 2015 更新 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Microsoft ASP.NET 核心 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

若要執行 Docker 容器本機，您需要本機 docker 用戶端。
您可以使用發行的[Docker 工具箱](https://www.docker.com/products/overview#/docker_toolbox)需要 HYPER-V 停用時，它，或者您可以使用[Docker Windows Beta](https://beta.docker.com)會使用 HYPER-V，並需要 Windows 10。

如果使用 Docker 工具箱，您必須[設定 Docker 用戶端](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1.建立 web 應用程式

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2.新增 Docker 支援

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3.編輯您的程式碼並重新整理

若要快速重複的變更，可以啟動應用程式內容器，並繼續進行的變更，如同使用 IIS 快速檢視。

1. 方案組態設定為`Debug`按**&lt;CTRL + F5 >**建立 docker 圖像，並在本機上執行。

    一旦容器圖像已建立，並在 Docker 容器執行，Visual Studio 便會啟動預設瀏覽器中的 Web 應用程式。
    如果您使用 Microsoft Edge 瀏覽器或有錯誤，請參閱[疑難排解](vs-azure-tools-docker-troubleshooting-docker-errors.md)一節。

1. 移至 [關於] 頁面，也就是我們，讓我們變更的位置。

1. 返回 Visual Studio 並開啟`Views\Home\About.cshtml`。

1. 將下列 HTML 內容新增至檔案的結尾，然後儲存變更。

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  當.NET 建置完成，而且您會看到這些檢視輸出] 視窗中，切換回瀏覽器，並重新整理 [關於] 頁面。

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  已套用變更 ！

## <a name="4-debug-with-breakpoints"></a>4.使用偵錯中斷點

通常，變更會需要進一步檢查，並充分使用 Visual Studio 的偵錯功能。

1.  返回 Visual Studio 並開啟`Controllers\HomeController.cs`

1.  About 方法的內容取代下列動作︰

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  設定左邊的中斷點`string message`...線條。

1.  按下**&lt;F5 >**啟動偵錯。

1.  瀏覽至要叫用中斷點的 [關於] 頁面。

1.  切換至 Visual Studio 檢視中斷點，並檢查訊息的值。

    ![][2]

##<a name="summary"></a>摘要

使用[Docker 的 Visual Studio 2015 工具](https://aka.ms/DockerToolsForVS)，您可以取得本機，使用開發 Docker 容器內的生產真實性的生產力。

## <a name="troubleshooting"></a>疑難排解

[疑難排解 Visual Studio Docker 開發](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>更多關於使用 Visual Studio、 Windows 和 Azure Docker 資訊

- [Visual Studio docker 工具](http://aka.ms/dockertoolsforvs)-開發容器中將您.NET 核心程式碼
- [Visual Studio 小組服務 docker 工具](http://aka.ms/dockertoolsforvsts)-建立並部署 docker 容器
- [Visual Studio 程式碼 docker 工具](http://aka.ms/dockertoolsforvscode)語言的即將 e2e 狀況編輯 docker 檔案的服務
- [Windows 容器資訊](http://aka.ms/containers)-Windows Server Nano 伺服器資訊
- [Azure 容器服務](https://azure.microsoft.com/services/container-service/) - [Azure 容器服務內容](http://aka.ms/AzureContainerService)
-    更多範例使用 Docker 的詳細資訊，請參閱從[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 連線[示範](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)[使用 Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) 。 從 HealthClinic.biz 示範更多的快速入門，請參閱[Azure 開發人員工具快速入門](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)。

## <a name="various-docker-tools"></a>各種 Docker 工具

[一些很棒的 docker 工具 （Steve Lasker 部落格）](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>建議的文件

[從 NGINX Microservices 簡介](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>簡報

- [Steve Lasker︰ 與 Live 「 拉斯維加斯 」 2016-Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [ASP.NET 核心簡介@建立 2016-位置您在示範](https://channel9.msdn.com/Events/Build/2016/B810)
- [開發.NET 容器，頻道 9 中的應用程式](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
