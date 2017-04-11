<properties
    pageTitle="Azure 診斷版本歷程記錄"
    description="說明中不同的 Microsoft Azure Sdk 版本的不同版本的 Azure 診斷為隨附的變更。"
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Microsoft Azure 診斷版本歷程記錄

Azure 診斷程式的新嗎？ 請參閱[Azure 診斷概觀](azure-diagnostics.md)。

Azure SDK 的每個版本通常是隨附於 Azure 診斷程式的新版本。 下表說明 sdk 相關聯的 Azure SDK 和 Azure 診斷版本。



Azure SDK 版本 | Azure 診斷版本 | 模型
--- | --- | ---
1.x      | 1.0 | 外掛程式
若要 2.4 2.0| 1.0 | "
2.5      | 1.2 | 副檔名
2.6      | 1.3 | "
2.7      | 1.4 | "
將 2.8 顯示      | 為 1.5 來計算 | "


最新版本是隨附於 Azure SDK 2.8 1.5 倍。 Azure 診斷副檔名 SDK 隨附的版本僅用於本機模擬器案例。 部署的應用程式會在 Azure 中執行不管的資料使用建置版本的 SDK 應用程式時，會自動使用最新版本。 不過，除非您安裝最新的 Azure SDK，您可能會包含所有的工具，協助您使用的新功能。

各種功能，如下所述的變更。

## <a name="azure-sdk-28"></a>Azure SDK 2.8
Azure SDK 2.8 加入傳送診斷資料至[應用程式的深入見解](./application-insights/app-insights-cloudservices.md)容易診斷問題，在您的應用程式，以及系統和基礎結構的層級的功能。

## <a name="azure-26-diagnostics-changes"></a>Azure 2.6 診斷變更

在 Visual Studio 中 Azure SDK 2.6 雲端服務專案，下列進行變更。 （這些變更也適用於較新版本的 Azure SDK。）

- 本機模擬器現在支援診斷。 這表示您可以收集診斷資料，並確保您的應用程式時您是開發及測試 Visual Studio 中建立的右追蹤。 連接字串`UseDevelopmentStorage=true`啟用診斷資料集合，當您執行的雲端服務專案 Visual Studio 中使用 Azure 儲存模擬器。 在 （開發儲存區） 儲存帳戶收集所有診斷資料。

- 診斷儲存帳戶連線字串 (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) 會再次儲存於 [服務設定 (.cscfg) 檔案。 Azure SDK 2.5 中 diagnostics.wadcfgx 檔案中指定的診斷儲存帳戶。

有的連接字串的處理方式在 Azure SDK 2.4 及舊版和其如何運作在 Azure SDK 2.6 及更新版本的一些主要差異。

- 在 Azure SDK 2.4 及更早版本，連接字串作為執行階段診斷外掛程式取得轉接診斷記錄儲存帳戶資訊。

- 在 Azure SDK 2.6 及更新版本，診斷連線字串使用 Visual Studio 來設定診斷副檔名發行期間的適當的儲存帳戶資訊。 連接字串，可讓您定義不同的儲存為不同的服務設定 Visual Studio 用來發佈的帳戶。 不過，因為 （後 Azure SDK 2.5) 已不再提供診斷外掛程式，單獨.cscfg 檔案無法啟用診斷副檔名。 您必須啟用分別透過工具，例如 Visual Studio 或 PowerShell 副檔名。

- 若要簡化透過 PowerShell 設定診斷副檔名的程序，Visual Studio 封裝的輸出也包含公用設定 XML 針對每一個角色的診斷副檔名。 Visual Studio 會使用的診斷連接字串來填入公用設定中的儲存空間帳戶資訊。 公用設定檔案會建立在 [延伸] 資料夾，然後依照 PaaSDiagnostics 的模式。<RoleName>.PubConfig.xml。 任何 PowerShell 基礎部署可以使用這個模式對應至角色的每個設定。

- Azure 入口網站也使用.cscfg 檔案中的連接字串來存取診斷資料，讓它可以出現在**監視**] 索引標籤。 若要設定顯示在入口網站的詳細資訊的監控資料服務需要連線字串。

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>移轉至 Azure SDK 2.6 及更新版本中的專案

時從 Azure SDK 2.5 Azure SDK 2.6 或更新版本、 升級，如果您有.wadcfgx 檔案中所指定的診斷儲存帳戶，然後將其會留在那裡。 若要利用使用不同的儲存設定不同的儲存帳戶的彈性，您必須手動新增至專案的 [連線字串。 如果您在從 Azure SDK 2.4 或更舊版本 Azure SDK 2.6 遷移專案，會保留診斷連接字串。 不過，請注意如何連接字串會被視為 Azure SDK 2.6 中指定前一節中的變更。

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Visual Studio 如何判斷診斷儲存帳戶

- 如果.cscfg 檔案中指定診斷連線字串，則 Visual Studio 會用它來設定診斷副檔名，發佈時，與期間包裝產生公用設定 xml 檔案時。

- 如果沒有診斷連接字串指定.cscfg 檔案中，然後 Visual Studio 回到使用.wadcfgx 檔案中所指定的儲存空間帳戶設定診斷副檔名為發佈，並產生公用設定 xml 檔案時包裝。

- 診斷連接字串.cscfg 檔案中的優先於.wadcfgx 檔案中的儲存空間帳戶。 如果.cscfg 檔案中指定診斷連線字串，則 Visual Studio 就會使用的檔案，並忽略.wadcfgx 中的儲存空間帳戶中。

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>「 更新開發儲存空間的連接字串...」 功能 核取方塊嗎？

**更新開發儲存空間的診斷與使用 Microsoft Azure 儲存體帳戶認證在發行到 Microsoft Azure 快取的連接字串**] 核取方塊可讓您方便的方式來發佈期間所指定的 Azure 儲存體帳戶以更新任何開發儲存帳戶的連線字串。

例如，假設您選取此核取方塊，並診斷連線字串指定`UseDevelopmentStorage=true`。 當您將專案發佈至 Azure 時，Visual Studio 會自動與您在 [發佈] 精靈中指定的儲存空間帳戶更新診斷連線字串。 不過，如果真實儲存體帳戶已指定為診斷連線字串，然後該帳戶會使用。

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>診斷 Azure SDK 2.4 與先前和 Azure SDK 2.5 及更新版本之間的功能差異

如果您從 Azure SDK 2.4 至 Azure SDK 2.5 或更新版本升級您的專案，您應該謹記下列診斷程式的功能差異。

- **設定 Api 已遭取代**– 診斷程式設定用於 Azure SDK 2.4 或舊版中，但功能已遭取代在 Azure SDK 2.5 及更新版本。 如果您的診斷設定目前定義在程式碼，必須重新設定在移轉專案中的 [診斷從頭這些設定，以繼續工作。 Azure SDK 2.4 的診斷設定檔是 diagnostics.wadcfg，與 diagnostics.wadcfgx Azure SDK 2.5 或更新版本。

- **診斷雲端服務應用程式只能在角色層級，不在執行個體層級設定。**

- **每當您部署應用程式，更新診斷設定**– 如果您從伺服器總管變更診斷設定，然後重新部署您的應用程式，這可能會導致不一致性致歉問題。

- **診斷設定檔中，不在程式碼中設定中 Azure SDK 2.5 及更新版本、 當機傾印**– 如果您有損毀傾印設定程式碼中，您必須以手動方式傳輸設定程式碼的設定檔中，因為損毀傾印不會移轉過程傳輸至 Azure SDK 2.6。
