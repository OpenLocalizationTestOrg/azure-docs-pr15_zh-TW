<properties
    pageTitle="Azure 應用程式服務 web、 行動電話及 API 應用程式 |Microsoft Azure"
    description="了解 Azure 應用程式服務如何協助您開發、 部署及管理網站和行動應用程式。"
    keywords="應用程式服務，azure 應用程式服務、 應用程式服務成本，可縮放比例、 應用程式部署、 azure 應用程式部署、 paas、 平台為-的-服務、 網站、 網站，azure 行動網路"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>什麼是 Azure 應用程式服務？

*應用程式服務*是[平台為-的-服務](https://en.wikipedia.org/wiki/Platform_as_a_service)(PaaS) 提供的 Microsoft Azure。 建立網站及所有的平台或裝置的行動應用程式。 您的應用程式與整合 SaaS 解決方案、 連線與內部部署的應用程式，以及自動化商務程序。 Azure 上完全受管理的虛擬機器 (Vm)，執行您的應用程式，而且您可以選擇共用的 VM 資源或專用的 Vm。

應用程式服務包括網頁和行動我們先前傳遞分別為 Azure 網站和 Azure 行動服務的功能。 也包含自動化商務程序，並裝載雲端 Api 的新的功能。 為單一整合服務，應用程式服務可讓您撰寫各種元件-網站、 行動應用程式後結束、 RESTful Api 與商務程序，將單一解決方案。

以下 4 一刻影片會提供服務應用程式如何與舊版 Azure 服務以及其中有什麼新的簡短的說明。

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>為什麼要使用應用程式服務？

以下是一些主要功能和應用程式服務的功能︰

- **多語言和架構**的應用程式服務有最高級支援 ASP.NET、 Node.js、 Java、 PHP，及 Python。 您也可以在 [應用程式服務 Vm 上執行[Windows PowerShell 和其他指令碼或可執行檔](../app-service-web/web-sites-create-web-jobs.md)。

- **DevOps 最佳化**-設定[持續整合及部署](../app-service-web/app-service-continuous-deployment.md)Visual Studio 小組服務、 GitHub，或 BitBucket。 升階透過[測試和臨時環境](../app-service-web/web-sites-staged-publishing.md)的更新。 執行[A / B 測試](../app-service-web/app-service-web-test-in-production-get-start.md)。 使用[PowerShell 的 Azure](../powershell-install-configure.md)或[跨平台命令列介面 (CLI)](../xplat-cli-install.md)，以管理您的應用程式，在應用程式服務。

- **全域不按比例縮放的可用性**-縮放比例[設定](../app-service-web/web-sites-scale.md)或[縮小](../monitoring-and-diagnostics/insights-how-to-scale.md)手動或自動。 主控您的應用程式，在 Microsoft 的全域資料中心基礎結構的任何位置，然後應用程式服務[SLA](https://azure.microsoft.com/support/legal/sla/app-service/)承諾可用性]。

- **SaaS 平台與內部部署資料連線**-從超過 50 個[連接器](../connectors/apis-list.md)的企業系統 （例如 SAP、 Siebel 和 Oracle），選擇 [SaaS 服務 （例如 Salesforce 和 Office 365） 和網際網路服務 （例如 Facebook 和 Twitter）。 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和[Azure 虛擬網路](../app-service-web/web-sites-integrate-with-vnet.md)存取內部部署資料。

- **安全性和法規遵循**的應用程式服務是[ISO SOC，與 PCI 相容](https://www.microsoft.com/TrustCenter/)。

- **應用程式範本**-從應用程式中的範本可讓您使用精靈來安裝常用的開啟來源軟體，例如 WordPress、 Joomla 及 Drupal [Azure Marketplace](https://azure.microsoft.com/marketplace/)延伸清單中選擇。

- **Visual Studio 整合**專用的工具，在 Visual Studio 簡化建立、 部署及偵錯的工作。

## <a name="app-types-in-app-service"></a>在應用程式服務應用程式類型

應用程式服務可提供數種*應用程式類型*，每一種要主控特定種類的工作量︰

- [**Web 應用程式**](../app-service-web/app-service-web-overview.md)-裝載網站與 web 應用程式。

- [**行動應用程式**](../app-service-mobile/app-service-mobile-value-prop.md)請先結束裝載行動應用程式。

- [**API 應用程式**](../app-service-api/app-service-api-apps-why-best-platform.md)-裝載 RESTful Api。

- [**邏輯應用程式**](../app-service-logic/app-service-logic-what-are-logic-apps.md)集的自動化商務程序，以及跨雲朵整合系統和資料，而不需要撰寫程式碼。

Word*應用程式*以下指的是專門用來執行工作負載的主機服務資源。 製作 「 web 應用程式 」 作為範例，您可能習慣思考 web 應用程式的做為計算資源並在一起的瀏覽器提供功能的應用程式碼。 但在應用程式服務*web 應用程式*的計算資源 Azure 提供的主機服務應用程式碼。 如果由您的應用程式的 web 前端和 RESTful 的 API 後端，您可以將兩者都在 web 應用程式部署或您可以在 web 應用程式和您 API 應用程式的後端程式碼部署前端程式碼。 您的應用程式可能會組成的不同類型的多個應用程式服務應用程式。

## <a name="app-service-plans"></a>應用程式服務方案

[應用程式服務方案](azure-web-sites-web-hosting-plans-in-depth-overview.md)指定在您的應用程式執行計算資源的類型。 如果您希望精簡流量負載時，您可以使用共用的 Vm （**空閒**和**共用**價格層）。 對於較高的載入，您可以選擇從幾種不同的專用 Vm （**基本**、**標準**、 和**進階版**層） 的大小。 多個應用程式服務應用程式可以共用相同的方案，以及它們一起縮放上下價格層計劃中。

如果您需要更多延展性和網路隔離，您可以執行您的應用程式的[應用程式服務環境](../app-service-web/app-service-app-service-environment-intro.md)中。

## <a name="pricing"></a>價格

多少應用程式服務成本的相關資訊，請參閱[應用程式服務價格](https://azure.microsoft.com/pricing/details/app-service/)。

## <a name="test-drive-app-service"></a>測試應用程式服務

[建立範例 web 應用程式、 行動應用程式或邏輯應用程式](http://go.microsoft.com/fwlink/?LinkId=523751)（1 小時），沒有信用卡，播放需要和，沒有承諾，沒有遇到的麻煩。

或開啟[免費 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)，，並嘗試以下其中一個我們的快速入門教學課程︰

* [教學課程︰ 建立 web 應用程式](../app-service-web/app-service-web-get-started.md)
* [教學課程︰ 建立行動應用程式](../app-service-mobile/app-service-mobile-android-get-started.md)
* [教學課程︰ 建立 API 應用程式](../app-service-api/app-service-api-dotnet-get-started.md)
* [教學課程︰ 建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)
