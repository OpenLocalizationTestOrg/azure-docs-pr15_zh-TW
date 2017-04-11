<properties
    pageTitle="什麼是行動應用程式"
    description="了解哪些優點會應用程式服務將您的企業版行動應用程式。"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Mobile 應用程式是什麼？

Azure 應用程式服務是完全受管理的[的服務的平台](https://azure.microsoft.com/overview/what-is-paas/)(PaaS) 提供專業的開發人員，讓一組豐富的網頁，行動裝置的功能與整合案例。 *Azure 應用程式服務*中的*行動應用程式*提供彈性，全域可用的行動應用程式開發平台的企業開發人員與系統整合，讓行動開發人員豐富的功能。

![行動應用程式](./media/app-service-mobile-value-prop/overview.png)

##<a name="why-mobile-apps"></a>為什麼行動應用程式？
*Azure 應用程式服務*中的*行動應用程式*提供彈性，全域可用的行動應用程式開發平台的企業開發人員與系統整合，讓行動開發人員豐富的功能。 使用行動應用程式中，您可以︰

- **建立原生和跨平台應用程式**-您正在建立原生 iOS、 Android 和 Windows 應用程式或跨平台 Xamarin 或 Cordova (Phonegap) 應用程式，您可以利用應用程式服務使用原生 Sdk。
- **連線到您的企業系統**-您可以在分鐘上新增公司標誌和連線至您企業的內部部署或雲端資源的行動應用程式。
- **建立離線準備資料同步處理的應用程式**-將您的行動工作人員生產力所建立的應用程式離線工作並使用行動應用程式在背景中的同步處理資料連線時使用的任何企業資料來源或 SaaS Api 進行簡報。
- **推入通知數百萬秒數**-加入您的客戶的需求，正確時間時傳送個人化的任何裝置上的 [立即推播通知。

## <a name="mobile-app-features"></a>行動應用程式功能
啟用雲端的行動應用程式開發重視下列功能︰

- **驗證與授權**-從不斷身分識別提供者，包括 Azure Active Directory 企業驗證，以及社交的提供者，例如 Facebook、 Google、 Twitter 與 Microsoft 帳戶清單中選取。  Azure Mobile 應用程式提供 OAuth 2.0 服務每個提供者。  您也可以整合 SDK 身分識別提供者提供者特定功能。

  探索深入瞭解我們[驗證功能]。

- **資料存取**Azure Mobile 應用程式提供連結到 SQL Azure 或內部部署的 SQL Server mobile 易於 OData v3 若資料來源。  這項服務為基礎的實體架構，可讓您輕鬆地與其他 NoSQL 整合與 SQL 資料提供者，包括[Azure 資料表儲存體]與 MongoDB、 [DocumentDB] SaaS API 的提供者所 Office 365 和 Salesforce.com。
- **離線同步處理**的設定，讓您輕鬆地建立強大和回應行動應用程式，使用離線的資料作業我們的用戶端 Sdk 讓能夠自動同步處理的後端的資料，包括衝突解決支援。

  探索深入瞭解我們[資料功能]。

- **推播通知**-我們的用戶端 SDK 完美地整合 Azure 通知集線器，讓您可以同時傳送給數百萬個使用者的推入通知的註冊功能。

  探索深入瞭解我們的[推播通知功能]。

- **用戶端 Sdk** -我們提供一組完整的用戶端 Sdk 涵蓋原生開發 ([iOS]， [Android]和[Windows])，跨平台開發 （[ [Xamarin 表單]中的 [[Xamarin iOS 和 Android 版]） 和混合式應用程式開發 ([Apache Cordova])。  每個用戶端 SDK，就 MIT 授權，並開啟來源。

## <a name="azure-app-service-features"></a>Azure 應用程式服務功能。
下列的平台功能是通常適用於行動生產網站。

- **自動調整大小**的應用程式服務可讓您快速縮放比例或分門別類處理任何內送的客戶載入。 手動設定來調整您的行動應用程式後端根據 [載入] 或 [排定自動調整大小或選取的數字與 Vm 的大小。

  探索關於[自動調整大小]的詳細資訊。

- **執行環境**的應用程式服務可以執行多個版本的網站，以便讓您執行 A / B 測試，測試生產較大的 DevOps 計劃的一部份，就地臨時的新的後端。

  探索關於[暫存的環境]的詳細資訊。

- **連續部署**-應用程式服務可以與一般 SCM 系統整合，讓您可以自動 SCM 系統的分支推入部署您的後端的新版本。

  探索更多有關[部署選項]。

- **虛擬網路**-應用程式服務可以連線到內部部署資源使用虛擬網路、 ExpressRoute 或混合式部署的連線。

  探索瞭解[混合式連線]、[虛擬網路]和[ExpressRoute]。

- **隔離 / 專用環境**-應用程式服務可以執行完全隔離及專用的環境的安全地在高執行 Azure 應用程式服務應用程式。  這是更高的小數位數、 隔離或安全網路存取要求的應用程式工作負載的理想。

  探索深入瞭解[應用程式服務環境]。

## <a name="getting-started"></a>快速入門 ##
若要開始使用行動應用程式，請依照[開始]教學課程。  這將涵蓋產生的行動裝置的後端和用戶端的您的選擇，然後整合驗證、 離線同步處理及推入通知的基本概念。  您可以遵循[開始]教學課程數次-一次每個用戶端應用程式。

如需有關 Azure Mobile 應用程式的詳細資訊，請檢閱我們[學習地圖]的資訊。
如需 Azure 應用程式服務平台上的詳細資訊，請參閱[Azure 應用程式服務]。

>[AZURE.NOTE]如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](https://tryappservice.azure.com/?appServiceName=mobile)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure 應用程式服務]: ../app-service/app-service-value-prop-what-is.md
[快速入門]: app-service-mobile-ios-get-started.md
[Azure 資料表儲存體]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[驗證功能]: ./app-service-mobile-auth.md
[資料功能]: ./app-service-mobile-offline-data-sync.md
[推播通知功能]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[IOS 和 Android 版 Xamarin]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin 表單]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[自動調整大小]: ../app-service-web/web-sites-scale.md
[執行環境]: ../app-service-web/web-sites-staged-publishing.md
[部署選項]: ../app-service-web/web-sites-deploy.md
[混合式連線]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[虛擬網路]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[應用程式服務環境]: ../app-service-web/app-service-app-service-environment-intro.md
[學習地圖]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
