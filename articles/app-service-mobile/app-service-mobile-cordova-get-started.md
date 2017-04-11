<properties
    pageTitle="Azure 應用程式服務行動應用程式建立 Cordova 應用程式 |Microsoft Azure"
    description="請遵循此教學課程以開始使用 Azure 行動應用程式 backends Apache Cordova 開發"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova，javascript]，在行動用戶端" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>建立 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概觀

本教學課程教您如何使用 Azure 行動應用程式後端 Apache Cordova 行動應用程式新增雲端後端服務。  您會建立新的行動應用程式後端和簡易_待辦清單_的應用程式的資料儲存在 Azure Apache Cordova 應用程式。

完成此教學課程是 Azure 應用程式服務中使用行動應用程式功能的相關所有其他 Apache Cordova 教學課程的必要條件。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程中，您需要下列項目︰

* 使用[Visual Studio 社群 2015年]或更新版本的電腦。
* [Visual Studio Apache Cordova 工具]。
* [作用中的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

您也可能會略過 Visual Studio 並直接使用 Apache Cordova 命令列。  完成本教學課程 Mac 電腦上的時，這是很有用。  本教學課程中未涵蓋編譯 Apache Cordova 用戶端應用程式使用命令列。

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure 行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[觀看影片，顯示類似的步驟](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>設定伺服器專案

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>下載並執行 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>後續步驟

現在，當您完成此快速入門教學課程，請移至其中一項動作的教學課程︰

* [新增驗證]您 Apache Cordova 應用程式。
* [新增推入通知]您 Apache Cordova 應用程式。

進一步瞭解 Azure 應用程式服務的重要概念。

* [驗證]
* [推播通知]

瞭解如何使用 Sdk。

* [Apache Cordova SDK]
* [ASP.NET 伺服器 SDK]
* [Node.js 伺服器 SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio 社群 2015]: http://www.visualstudio.com/
[Visual Studio Apache Cordova 工具]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[新增驗證]: app-service-mobile-cordova-get-started-users.md
[新增推入通知]: app-service-mobile-cordova-get-started-push.md
[驗證]: app-service-mobile-auth.md
[推播通知]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET 伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 伺服器 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
