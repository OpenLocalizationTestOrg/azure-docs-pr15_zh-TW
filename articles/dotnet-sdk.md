<properties
    pageTitle="什麼是 Azure.NET SDK"
    description="了解 Azure.NET SDK 中所包含的內容。"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>什麼是 Azure SDK.net？

## <a name="overview"></a>概觀

.NET Azure SDK 的一組 Visual Studio 工具，命令列工具執行階段的二進位檔案，與用戶端文件庫，可協助您開發、 測試和部署 Azure 中執行的應用程式。 本文詳細說明內容時，您收到安裝 SDK。 您可以下載 SDK 從[Azure 下載頁面](https://azure.microsoft.com/downloads/)。

Azure SDK.net 也包含[使用 Azure 服務的用戶端文件庫](http://go.microsoft.com/fwlink/?LinkId=510472)。 使用 NuGet 單獨安裝這些文件庫。

##<a id="included"></a>中包含哪些產品 Azure SDK.net

Azure SDK.net 安裝下列產品︰

- [Visual Studio 社群 Edition 2015](#vwd)
- [Microsoft Azure 儲存模擬器](#stgemulator)
- [Microsoft Azure 儲存工具](#stgtools)
- [Microsoft Azure 撰寫工具](#auth)
- [Microsoft Azure 模擬器](#emulator)
- [Visual Studio 與 Microsoft 的 HDInsight 工具登錄區 ODBC 驅動程式](#hdinsight)
- [Microsoft Azure.net 的文件庫](#libraries)
- [Microsoft Azure 行動應用程式 SDK](#mobile)
- [Microsoft Azure PowerShell](#ps)
- [Microsoft Visual Studio Microsoft Azure 工具](#tools)
- [Microsoft ASP.NET 網頁的和工具 Visual Studio](#wte)
- [Microsoft Azure 資料湖 Visual Studio 工具](#datalake)

###<a id="vwd"></a>Visual Studio 社群 Edition 2015

如果您沒有在您的電腦上安裝 Visual Studio，SDK 會安裝[Visual Studio 社群 Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs)。

###<a id="stgemulator"></a>Microsoft Azure 儲存模擬器

[Azure 儲存模擬器](http://msdn.microsoft.com/library/hh403989.aspx)使用 SQL Server 執行個體和本機檔案系統模擬 Azure 儲存體 （佇列、 表格、 二進位大型物件），讓您可以在本機測試。

###<a id="stgtools"></a>Microsoft Azure 儲存工具

這會安裝[AzCopy](http://aka.ms/AzCopy)，您可以將資料和 Azure 儲存體帳戶使用的命令列工具。

###<a id="auth"></a>Microsoft Azure 撰寫工具

包括下列︰

* 建立部署套件[CSPack 命令列工具](http://msdn.microsoft.com/library/gg432988.aspx)。
* [CSEncrypt 命令列工具](http://msdn.microsoft.com/library/hh404001.aspx)來加密透過遠端桌面連線存取雲端服務角色執行個體所使用的密碼。
* 執行階段雲端服務專案的二進位檔案需要通訊與執行階段環境和診斷。 這些二進位檔案中並沒有提供 NuGet 套件。

###<a id="emulator"></a>Microsoft Azure 模擬器

[Azure 模擬器](http://msdn.microsoft.com/library/dn339018.aspx)模擬雲端服務環境，讓您可以測試雲端服務專案本機電腦上之前將其部署到 Azure。

###<a id="hdinsight"></a>Visual Studio 中，與 Microsoft 的 HDInsight 工具登錄區 ODBC 驅動程式

在 [伺服器總管 HDInsight 工具可讓您瀏覽登錄區資料庫及連結的儲存空間帳戶 HDInsight 叢集、 建立資料表，並建立及提交登錄區查詢。 如需詳細資訊，請參閱[開始使用 Visual Studio HDInsight Hadoop 工具](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md)。

###<a id="libraries"></a>Microsoft Azure.net 的文件庫

包括下列︰

* Visual Studio 才能建立新的雲端服務專案時，電腦儲存的 NuGet 套件 Azure 儲存體、 服務匯流排和快取離線。
* Visual Studio 外掛程式，可讓本機 Visual Studio 中執行的專案[中的角色快取](http://msdn.microsoft.com/library/dn386103.aspx)。

###<a id="mobile"></a>Microsoft Azure 行動應用程式 SDK

[Azure 應用程式服務行動應用程式](app-service-mobile/app-service-mobile-value-prop.md)所使用的工具。

###<a id="ps"></a>Microsoft Azure PowerShell

Azure PowerShell 可讓您便可[自動化 Azure 環境建立及部署](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。

###<a id="tools"></a>Microsoft Visual Studio Microsoft Azure 工具

這可讓您使用 Azure 主要雲端服務與虛擬機器的資源︰

* [建立、 開啟及發佈雲端服務的專案](cloud-services/cloud-services-dotnet-get-started.md)。
* [建立部署套件雲端服務的專案](http://msdn.microsoft.com/library/ff683672.aspx)。
* [建立新 web 專案時建立 Azure 虛擬機器](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md)。
* [建立新的虛擬機器時建立的 PowerShell 指令碼](http://msdn.microsoft.com/library/dn642480.aspx)。
* [檢視及管理 Visual Studio 專案屬性 windows 中的雲端服務專案設定](http://msdn.microsoft.com/library/ee405486.aspx)。
* 檢視及管理[雲端服務](http://msdn.microsoft.com/library/ff683675.aspx)的[虛擬機器](http://msdn.microsoft.com/library/jj131259.aspx)，與[服務匯流排](http://msdn.microsoft.com/library/jj149828.aspx)伺服器總管] 中。
* [在遠端的雲端服務與虛擬機器偵錯模式中執行](http://msdn.microsoft.com/library/ff683670.aspx)。
* [自動化資源佈建使用 Azure 資源群組部署專案](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Visual Studio 的 Microsoft 應用程式服務工具

這可讓您使用 Azure 網站︰

* [Azure 網站發佈 web 專案](app-service-web/web-sites-dotnet-get-started.md)。
* [Azure WebJobs 發佈主控台應用程式專案](app-service-web/websites-dotnet-deploy-webjobs.md)。
* [建立新 web 專案時，或發佈 web 專案時建立 Azure 網站和 SQL 資料庫資源](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。
* [建立新網站時建立 PowerShell 部署指令碼](http://msdn.microsoft.com/library/dn642480.aspx)。
* [管理及疑難排解伺服器總管] 中的 Azure 網站](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement)。
* [在遠端的網站和 WebJobs 偵錯模式中執行](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)。

>[AZURE.NOTE] 您沒有安裝 Azure SDK.net 可以使用這些功能。也可以包含在 Visual Studio 更新。

##<a id="datalake"></a>Microsoft Azure 資料湖 Visual Studio 工具

如需詳細資訊，請參閱[教學課程︰ 開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

##<a id="notincluded"></a>當您安裝 Azure SDK.net 時，具有不會包含什麼

有幾個項目，您可能會想 Azure 開發時，不會包含您安裝 SDK。 最重要的項目如下︰

* [用戶端文件庫](http://go.microsoft.com/fwlink/?LinkId=510472)。

    Azure SDK 包含使用 Azure 服務、 用戶端文件庫，但當您安裝 SDK，而非所有已安裝。 如果您的應用程式需要 SDK 無法安裝用戶端文件庫，您可以從[NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472)取得。 如果您的應用程式會使用用戶端文件庫會在安裝 SDK 的但最好更新 NuGet.org 在目前的版本。

    **用戶端文件庫的本機複本。** Azure SDK.net 複製到您的電腦一些 Azure 用戶端的文件庫，例如儲存空間、 服務匯流排和快取的 NuGet 套件。 這些用戶端文件庫中會自動包含新雲端服務的專案，所以本機 NuGet 封裝啟用 Visual Studio 建立專案，即使您未連線至網際網路。 製作 NuGet.org 在用戶端程式庫通常更多通常比發行 SDK 的新版本，經常更新用戶端文件庫目前比就要使用 SDK。

    **專案範本，在用戶端文件庫。** 只有[Azure 雲端服務](cloud-services/cloud-services-dotnet-get-started.md)和 Azure 應用程式服務[Mobile 應用程式](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)的 project 範本會自動包含一些用戶端文件庫。 其他文件庫或其他範本，請安裝您需要的[用戶端文件庫 NuGet 套件](http://go.microsoft.com/fwlink/?LinkId=510472)。

* [行動應用程式專案範本](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

    行動應用程式範本是只能在 Visual Studio 2013 更新 2 及更新版本。 並非使用 Visual Studio 2012 或舊版中，而非 Visual Studio 2013 更新 1 或更舊版本，即使您安裝 Azure SDK.net。

##<a id="faq"></a>常見問題集

- [許多 Azure 功能已經在 Visual Studio 中。需要安裝 Azure SDK.net 嗎？](#azinvs)
- [我想要的用戶端文件庫。必須安裝才能.net Azure SDK 嗎？](#clientlib)
- [哪裡可以找到舊版 Azure SDK.net？](#olderversions)
- [什麼是 Azure SDK.net 版本的生命週期原則？](#lifecycle)
- [哪些來賓 OS 版本是相容於.NET Azure SDK？](#guestos)
- [如何解除 Azure SDK 安裝.net？](#uninstall)

###<a id="azinvs"></a>許多 Azure 功能已經在 Visual Studio 中。 需要安裝 Azure SDK.net 嗎？

最好安裝 SDK，如果您想要開發 Azure 使用最新的工具。 如果您想要而未安裝 SDK，您可以執行下列情況成立時︰

* 您已安裝最新的[Visual Studio 更新](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5)。
* 您在開發僅適用於 Azure 網站或行動電話的服務，不適用於雲端服務或虛擬機器。
* 您的應用程式不使用儲存空間，或使用儲存空間，但您不需要儲存模擬器或 AzCopy 工具。

###<a id="clientlib"></a>我想要的用戶端文件庫。 必須安裝才能.net Azure SDK 嗎？

僅限，使您可以建立雲端服務專案即使您未連線到網際網路，SDK 安裝用戶端文件庫。 目前的用戶端文件庫，可在[NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472)NuGet 套件中。 如需詳細資訊，請參閱本文件較舊版本的[安裝.NET Azure SDK 時，具有不會包含什麼](#notincluded)。

###<a id="olderversions"></a>哪裡可以找到舊版 Azure SDK.net？

如果較舊版本中，請參閱[Azure SDK.net](https://azure.microsoft.com/downloads/archive-net-downloads/)下載頁面。

###<a id="lifecycle"></a>什麼是 Azure SDK.net 版本的生命週期原則？

請參閱[Microsoft Azure 雲端服務支援週期原則](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)。

###<a id="guestos"></a>哪些來賓 OS 版本是相容於.NET Azure SDK？

請參閱[Azure 來賓作業系統版本和 SDK 相容性矩陣圖](http://msdn.microsoft.com/library/ee924680.aspx)。

###<a id="uninstall"></a>如何解除 Azure SDK 安裝.net？

本文[Azure SDK.net 中所包含的內容](#included)中顯示每個項目是 [已安裝程式在 Windows [控制台]， **[程式和功能**] 清單中的另一個程式。  若要解除安裝這些群組; 無法您必須個別解除安裝每個程式。

Azure SDK.net 已經安裝，而且您安裝新版本，則通常不需要解除安裝的舊的項目。 在大部分情況下，SDK 安裝更新現有的程式，而非在新增新和離開的舊的項目。

不過，如果您想要移除無-更長的時間-所需的剩餘的舊版，解除安裝指定較舊版本號碼的程式，而且只解除安裝後是否有相同的程式，較新的版本。 例如，2.6 四捨五入至從 2.5 更新後，您可能會看到 2.5 和 2.6 版本的 「 Microsoft Azure 工具 Microsoft Visual Studio 2013 」，並可以解除安裝 2.5 的版本。 但您可能只會看到 2.5 版本的 「 Microsoft Azure 撰寫工具 」，並不是安全解除安裝的。

請注意，在 [**程式和功能**] 中顯示的程式標題中的版本號碼可以誤導。  如果您安裝的 Visual Studio 2013 和 「 Microsoft Azure 行動應用程式 SDK V2.0 」 SDK Visual Studio 2015; SDK 2.6 版，例如包含 「 Microsoft Azure 行動應用程式 SDK V1.0 」SDK 版本，但這 Visual Studio 版本程式適用於的標記，這種情況下不是版本。

這份文件不會列出每個舊版 Azure SDK 包含; 每個程式有其他程式，您可以解除安裝舊版 SDK，因為舊版 SDK 有時包括版本被省略的程式。 例如，2.5 版安裝 」 Azure 資源管理員工具的 Visual Studio 」 但這不是這份文件的清單中因為它不會再顯示為個別的程式，在 [**程式和功能**。  本文只會列出所含的 Azure SDK.NET 2.6 版的程式。

> **附註︰**某些程式 SDK 包含可能也會是另外安裝在其他內容，即使您不需要完整 SDK 可能需要。 相同可能的程式所安裝的舊版 SDK 但版本 SDK 被省略，則為 true。 當您解除安裝程式時，請小心避免移除仍然需要在電腦上的項目。



##<a id="versions"></a>版本

若要看到的是哪個版本是最或下載較舊版本，請參閱[Azure SDK.NET 版本歷程記錄](https://azure.microsoft.com/downloads/archive-net-downloads/)] 頁面。

##<a id="resources"></a>資源

若要下載目前 Azure SDK.NET 或用戶端文件庫，請參閱[Azure 下載頁面](https://azure.microsoft.com/downloads/)。

Azure SDK.NET 來源程式碼，包括用戶端文件庫，請參閱[GitHub.com/Azure](https://github.com/azure/)。

Azure 用戶端文件庫參照文件，請參閱[Azure.NET 參照](https://azure.microsoft.com/documentation/api/)。
