<properties
    pageTitle="儲存檢視診斷中的資料和 Azure 儲存 |Microsoft Azure"
    description="取得 Azure 診斷資料至 Azure 儲存體和檢視"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>儲存檢視診斷中的資料和 Azure 儲存體

除非您傳送至 Microsoft Azure 儲存模擬器或 Azure 儲存空間，不會永久儲存診斷資料。 一次在儲存空間，可以檢視擁有下列其中一種可用的工具。

## <a name="specify-a-storage-account"></a>指定儲存帳戶

您指定您想要使用 ServiceConfiguration.cscfg 檔案中的儲存空間帳戶。 帳戶資訊被定義為連線字串中的設定。 下列範例顯示預設的連接字串建立新的雲端服務專案 Visual Studio 中︰


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

您可以變更此連線字串，提供 Azure 儲存體帳戶的帳戶資訊。

根據診斷收集的資料類型，Azure 診斷使用 Blob 服務或表格服務。 下表顯示的資料來源，也會和其格式。

|資料來源|儲存格式|
|---|---|
|Azure 記錄|表格|
|IIS 7.0 記錄|Blob|
|Azure 診斷基礎結構的記錄|表格|
|無法要求追蹤記錄檔|Blob|
|Windows 事件記錄檔|表格|
|效能計數器|表格|
|損毀傾印|Blob|
|自訂錯誤記錄檔|Blob|

## <a name="transfer-diagnostic-data"></a>傳送診斷資料

SDK 2.5 或更新版本，就會發生的要求診斷資料透過設定檔。 您可以在中設定所指定的排程時間間隔轉移診斷資料。

SDK 2.4 和前一個您可以要求程式的方式傳送診斷資料透過設定檔。 以程式設計的方式也可讓您可以視需要傳輸。


>[AZURE.IMPORTANT] 當您將診斷的資料傳輸至 Azure 儲存體帳戶時，會造成診斷資料使用的儲存空間資源的成本。

## <a name="store-diagnostic-data"></a>診斷資料儲存

記錄資料儲存在 Blob 或表格的儲存空間，使用下列名稱︰

**表格**

- **WadLogsTable** -撰寫程式碼使用追蹤接聽的記錄。

- **WADDiagnosticInfrastructureLogsTable** -診斷監視器及設定變更。

- **WADDirectoriesTable** – 監視診斷監視器的目錄。  這包含 IIS 記錄檔，IIS 無法要求記錄和自訂目錄]。  [容器] 欄位中指定 blob 記錄檔的位置，名稱 blob 的是 [RelativePath] 欄位中。  Azure 虛擬機器上存在 AbsolutePath 欄位會指出的位置和檔案的名稱。

- **WADPerformanceCountersTable** – 效能計數器。

- **WADWindowsEventLogsTable** – Windows 事件記錄檔。

**二進位大型物件**

- **wad 控制項容器**– （僅適用於 SDK 2.4 與上一個） 的內容控制項的 Azure 診斷 XML 設定檔。

- **wad-iis-failedreqlogfiles** – 記錄包含從 IIS 無法要求的資訊。

- **記錄 iis wad 檔**– 包含 IIS 資訊記錄。

- **「 自訂 」** – 自訂容器根據設定監視診斷監視器的目錄。  WADDirectoriesTable 內，將會指定此 blob 容器的名稱。

## <a name="tools-to-view-diagnostic-data"></a>若要檢視診斷資料的工具
數種工具，可檢視的資料傳輸至儲存體後。 例如︰

- 在 Visual Studio-伺服器總管] 如果您已安裝 Azure 工具適用於 Microsoft Visual Studio 中，您可以使用 Azure 儲存體節點伺服器總管] 中，檢視 [從 Azure 儲存體帳戶的 [唯讀 blob 與表格資料。 您可以從您的本機存放區模擬器帳戶顯示資料，也存放區帳戶建立 Azure。 如需詳細資訊，請參閱[瀏覽和管理儲存空間資源伺服器檔案總管。](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md)

- [Microsoft Azure 儲存檔案總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)是一種獨立應用程式，可讓您輕鬆地使用 Windows、 OSX，以及 Linux Azure 儲存體資料。

- [Azure Management Studio 中](http://www.cerebrata.com/products/azure-management-studio/introduction)包含 Azure 診斷管理員可讓您檢視、 下載及管理收集 Azure 上的應用程式的診斷資料。


## <a name="next-steps"></a>後續步驟

[追蹤與 Azure 診斷在雲端服務應用程式中的流程](cloud-services-dotnet-diagnostics-trace-flow.md)
