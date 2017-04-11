<properties
    pageTitle="Web 應用程式概觀 |Microsoft Azure"
    description="瞭解 Azure 應用程式服務如何協助您開發和主機 web 應用程式"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Web 應用程式概觀

*應用程式服務 Web 應用程式*是最適合裝載網站和 web 應用程式完全受管理的計算平台。 [平台為-的-服務](https://en.wikipedia.org/wiki/Platform_as_a_service)(PaaS) 提供的 Microsoft Azure 可讓您著重於您的商務邏輯時 Azure 負責執行，以及您的應用程式不按比例縮放的基礎結構。

下列 5 分鐘視訊介紹 Azure 應用程式服務 Web 應用程式。

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>什麼是應用程式服務中的 web 應用程式？

在應用程式服務中皆為主機的網站或 web 應用程式提供 Azure 計算資源*web 應用程式*。  

根據您所選擇的價格層共用或專用的虛擬機器 (Vm)，可能計算資源。 在 [受管理的 VM 隔其他客戶中，執行應用程式碼。

您的程式碼可以在任何語言或所支援的[Azure 應用程式服務](../app-service/app-service-value-prop-what-is.md)，例如 ASP.NET、 Node.js、 Java、 PHP 或 Python 架構。 您也可以執行 web 應用程式中使用[PowerShell 和其他指令碼語言](web-sites-create-web-jobs.md#acceptablefiles)的指令碼。

有關您可以使用的 Web 應用程式的一般應用程式案例的詳細資訊，請參閱[Web 應用程式案例](https://azure.microsoft.com/documentation/scenarios/web-app/)和[Azure 應用程式服務、 虛擬機器、 服務結構和雲端服務比較](choose-web-site-cloud-service-vm.md#scenarios)的**案例與建議**一節。

## <a name="why-use-web-apps"></a>為什麼要使用 Web 應用程式？

以下是套用至 Web 應用程式服務應用程式的一些主要功能︰

- **多語言和架構**的應用程式服務有最高級支援 ASP.NET、 Node.js、 Java、 PHP，及 Python。 您也可以在 [應用程式服務 Vm 上執行[PowerShell 其他指令碼或可執行檔](../app-service-web/web-sites-create-web-jobs.md)。

- **DevOps 最佳化**-設定[持續整合及部署](../app-service-web/app-service-continuous-deployment.md)Visual Studio 小組服務、 GitHub，或 BitBucket。 升階透過[測試和臨時環境](../app-service-web/web-sites-staged-publishing.md)的更新。 執行[A / B 測試](../app-service-web/app-service-web-test-in-production-get-start.md)。 使用[PowerShell 的 Azure](../powershell-install-configure.md)或[跨平台命令列介面 (CLI)](../xplat-cli-install.md)，以管理您的應用程式，在應用程式服務。

- **全域不按比例縮放的可用性**-縮放比例[設定](../app-service-web/web-sites-scale.md)或[縮小](../monitoring-and-diagnostics/insights-how-to-scale.md)手動或自動。 主控您的應用程式，在 Microsoft 的全域資料中心基礎結構的任何位置，然後應用程式服務[SLA](https://azure.microsoft.com/support/legal/sla/app-service/)承諾可用性]。

- **SaaS 平台與內部部署資料連線**-從超過 50 個[連接器](../connectors/apis-list.md)的企業系統 （例如 SAP、 Siebel 和 Oracle），選擇 [SaaS 服務 （例如 Salesforce 和 Office 365） 和網際網路服務 （例如 Facebook 和 Twitter）。 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和[Azure 虛擬網路](../app-service-web/web-sites-integrate-with-vnet.md)存取內部部署資料。

- **安全性和法規遵循**的應用程式服務是[ISO SOC，與 PCI 相容](https://www.microsoft.com/TrustCenter/)。

- **應用程式範本**-從應用程式中的範本可讓您使用精靈來安裝常用的開啟來源軟體，例如 WordPress、 Joomla 及 Drupal [Azure Marketplace](https://azure.microsoft.com/marketplace/)延伸清單中選擇。

- **Visual Studio 整合**專用的工具，在 Visual Studio 簡化建立、 部署及偵錯的工作。

此外，在 web 應用程式可以利用的[API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)（例如 CORS 支援） 所提供的功能及[行動應用程式](../app-service-mobile/app-service-mobile-value-prop.md)（例如推入通知）。 如需在應用程式服務應用程式類型的詳細資訊，請參閱[Azure 應用程式服務概觀](../app-service/app-service-value-prop-what-is.md)。

在應用程式服務的 Web 應用程式，除了 Azure 會提供用於裝載網站和 web 應用程式的其他服務。 在大部分情況下，Web 應用程式是最佳選擇。  Microservice 架構，請考慮[服務布料的轉印圖樣](https://azure.microsoft.com/documentation/services/service-fabric)、，如果您需要更多的控制 Vm 在上執行的程式碼，請考慮[Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。 如需有關如何選擇這些 Azure 服務之間的詳細資訊，請參閱[Azure 應用程式服務、 虛擬機器、 服務結構和雲端服務比較](choose-web-site-cloud-service-vm.md)。

## <a name="getting-started"></a>快速入門

若要開始新的 web 應用程式的應用程式服務部署範例，請遵循[在 5 分鐘 Azure 您第一份的 web 應用程式部署](app-service-web-get-started.md)教學課程。 您必須是免費的 Azure 帳戶。

如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。
