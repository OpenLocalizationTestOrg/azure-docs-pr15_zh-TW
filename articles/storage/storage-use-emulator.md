<properties 
    pageTitle="Azure 儲存模擬器用於開發和測試 |Microsoft Azure" 
    description="Azure 儲存模擬器提供免費本機的開發環境開發和測試 Azure 儲存體。 深入了解儲存空間模擬器，包括如何驗證要求、 如何從您的應用程式連線至模擬器以及如何使用的命令列工具。" 
    services="storage" 
    documentationCenter="" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>
<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Azure 儲存模擬器用於開發和測試

## <a name="overview"></a>概觀

Microsoft Azure 儲存模擬器可提供資料列傳回 Azure Blob 與佇列中，表格服務用於開發的本機環境。 使用儲存空間模擬器，您可以測試應用程式的儲存空間服務本機，但不建立 Azure 的訂閱或支付任何成本。 當您滿意應用程式在模擬器中的運作方式時，您可以切換使用雲端中的 Azure 儲存體帳戶。

> [AZURE.NOTE] 儲存模擬器有[Microsoft Azure SDK](https://azure.microsoft.com/downloads/)的一部分。 您也可以安裝儲存模擬器使用[獨立的安裝程式](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)。 若要設定的儲存空間模擬器，您必須具備系統管理員權限在電腦上。
> 
> 儲存模擬器目前在只能在 Windows 上執行。
>  
> 請注意，在一個版本的儲存空間模擬器中建立的資料不保證存取時使用不同版本。 如果您需要長期保留您的資料，建議您在 Azure 儲存體帳戶，而非儲存模擬器中儲存的資料。

## <a name="how-the-storage-emulator-works"></a>儲存模擬器的運作方式
 
儲存模擬器使用本機的 Microsoft SQL Server 執行個體和本機檔案系統模擬 Azure 儲存服務。 根據預設，儲存模擬器，請在 Microsoft SQL Server 2012 Express 的 LocalDB 中使用的資料庫。  您可以選擇設定儲存模擬器存取，而不是 LocalDB 執行個體本機的 SQL Server 執行個體。 如需詳細資訊，請參閱[開始] 和 [初始化儲存模擬器](#start-and-initialize-the-storage-emulator)下方。

您可以安裝 SQL Server 管理 Studio Express 來管理您的 LocalDB 安裝。 儲存模擬器連線至 SQL Server] 或 [LocalDB 使用 Windows 驗證。 

之間的儲存空間模擬器和 Azure 儲存服務，有一些功能差異。 如需有關這些差異的詳細資訊，請參閱[儲存模擬器和 Azure 儲存體之間的差異](#differences-between-the-storage-emulator-and-azure-storage)。

## <a name="authenticating-requests-against-the-storage-emulator"></a>驗證的儲存空間模擬器對要求

如同 Azure 儲存在雲端，您所做的儲存空間模擬器針對每個要求必須經過驗證，除非是匿名要求。 您可以驗證要求與使用共用金鑰驗證的儲存空間模擬器，或使用共用的 access 簽章 (SA)。

### <a name="authentication-with-shared-key-credentials"></a>驗證共用索引鍵的認證

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

如需連線字串的詳細資訊，請參閱[設定 Azure 儲存空間的連接字串](storage-configure-connection-string.md)。 

### <a name="authentication-with-a-shared-access-signature"></a>使用共用的 access 簽章的驗證 

某些 Azure 儲存用戶端文件庫，例如 Xamarin 文件庫中，只支援驗證共用的存取簽章 (SA) 權杖。 您必須建立使用工具或應用程式支援共用金鑰驗證此 SA 權杖。 產生 SA 權杖簡單的方法是透過 PowerShell 的 Azure:

1. 如果您未，請安裝 PowerShell 的 Azure。 建議使用最新版 Azure PowerShell cmdlet。 如需安裝指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md#Install) 。

2. 開啟 Azure PowerShell，並執行下列命令。 請記住，若要取代*ACCOUNT_NAME*和*ACCOUNT_KEY = =*使用您自己的認證。 使用您所選擇的名稱來取代*CONTAINER_NAME* 。

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

新的容器的產生共用的 access 簽名 URI 應該類似下列︰

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

使用此範例中建立共用的 access 簽章無效的一天。 簽章授與容器內的 blob 的完整存取 （也就是閱讀、 寫入、 刪除、 清單）。

如需有關共用的 access 簽章的詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)。


## <a name="start-and-initialize-the-storage-emulator"></a>啟動和初始化儲存模擬器

若要開始 Azure 儲存模擬器，選取 [開始] 按鈕或按下 Windows 鍵。 開始輸入**Azure 儲存模擬器**，然後從應用程式清單中選取模擬器。 

當模擬器執行時，您會看到 Windows 工作列的通知區域中的圖示。

儲存模擬器啟動時，會出現在命令列的視窗。 您可以使用這個命令列視窗來啟動和停止儲存模擬器，以及清除資料、 取得目前的狀態，並初始化模擬器。 如需詳細資訊，請參閱[儲存模擬器命令列工具參考](#storage-emulator-command-line-tool-reference)。

命令列視窗關閉時，會繼續儲存模擬器執行。 若要再次顯示命令列，請依照上述步驟，如同開始儲存模擬器。

第一次執行儲存的模擬器中，為您初始化本機存放區環境。 初始設定程序中 LocalDB 會建立一個資料庫，並保留本機存放區各自 HTTP 連接埠。 

至 C:\Program Files (x86) \Microsoft SDKs\Azure\Storage 模擬器目錄的預設會安裝儲存模擬器。 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>初始化儲存模擬器使用不同的 SQL 資料庫

您可以使用儲存空間模擬器命令列工具初始化儲存模擬器指向 SQL 資料庫執行個體以外的預設 LocalDB 執行個體。 請注意，您必須執行命令列工具具有系統管理權限的儲存空間模擬器初始化後端資料庫︰

1. 按一下 [**開始**] 按鈕，或按下**Windows**鍵。 開始輸入`Azure Storage Emulator`，以顯示儲存模擬器命令列工具出現時，請選取它。
2. 在 [命令提示字元] 視窗中，輸入以下命令，其中`<SQLServerInstance>`是 SQL Server 執行個體的名稱。 若要使用 LocalDb，指定`(localdb)\v11.0`與 SQL Server 執行個體。

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    您也可以使用下列命令，將導向模擬器使用預設的 SQL Server 執行個體︰

        AzureStorageEmulator init /server .\\ 

    或者，您可以使用下列命令，重新初始化資料庫的預設 LocalDB 執行個體︰

        AzureStorageEmulator init /forceCreate 

如需有關這些命令的詳細資訊，請參閱[儲存模擬器命令列工具參考](#storage-emulator-command-line-tool-reference)。

## <a name="addressing-resources-in-the-storage-emulator"></a>處理儲存模擬器中的資源

服務端點的儲存空間模擬器是不同的 Azure 儲存體帳戶。 不同的是因為的本機電腦並不會執行網域名稱解析，所以儲存模擬器端點需要本機的地址，而不是網域名稱。

當您處理 Azure 儲存體帳戶中的資源時，您會使用下列的配置，其中帳戶名稱是部分 URI 主機名稱，而所提出的資源是 URI 路徑︰

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

例如，下列 URI 是 Azure 儲存體帳戶 blob 的有效地址︰

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

在儲存模擬器中的本機電腦並不會執行網域名稱解析，因為帳戶名稱會是 URI 路徑，而不是主機名稱的一部分。 您可以使用下列配置儲存在模擬器中執行的資源︰

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

例如，下列網址可能會用於存取 blob 儲存體模擬器中︰

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

服務端點的儲存空間模擬器是︰

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>處理次要 RA GRS 使用的帳戶

開始版本 3.1 的請儲存模擬器帳戶支援讀取權限地理重複試驗 (RA GRS)。 在雲端並在本機模擬器中的儲存空間資源，您可以存取的第二個位置，將-次要帳戶名稱。 例如，下列位址可能會用來存取 blob 儲存體模擬器中使用唯讀的次要中︰

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] 以程式設計方式存取與儲存模擬器次要，用於.NET 3.2 或更新版本中的儲存空間的用戶端文件庫。 請參閱[Microsoft Azure 儲存用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)如需詳細資訊。

## <a name="storage-emulator-command-line-tool-reference"></a>儲存模擬器工具命令列參考

當您啟動儲存模擬器時，版本 3.0 開始，您會看到命令列的視窗快顯。 使用的命令列視窗啟動和停止模擬器以及查詢的狀態，以及執行其他的作業。

> [AZURE.NOTE] 如果您有計算模擬器安裝 Microsoft Azure，當您啟動儲存模擬器時，會出現系統匣圖示。 以滑鼠右鍵按一下要顯示的功能表，提供圖形的方式來啟動和停止儲存模擬器的圖示。

### <a name="command-line-syntax"></a>命令列語法

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>選項

若要檢視的選項清單，請輸入`/help`在命令提示字元。

| 選項 | 描述                                                    | ] 命令                                                                                                 | 引數                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **開始**  | 啟動儲存模擬器。                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-同處理序*︰ 目前的程序，而不是建立新的程序中啟動模擬器。                          |
| **停駐點**   | 停止儲存模擬器。                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **狀態** | 列印儲存空間模擬器的狀態。                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **清除**  | 清除指定命令列上的所有服務中的資料。 | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*︰ 清除 blob 資料。 <br/>*佇列中*︰ 清除佇列中的資料。 <br/>*表格*︰ 清除表格的資料。 <br/>*所有*︰ 清除所有服務中的所有資料。 |
| **初始化**   | 執行一次設定模擬器初始化。       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-伺服器 serverName\instanceName*︰ 指定裝載 SQL 執行個體的伺服器。 <br/>*-sqlinstance instanceName*︰ 指定要使用預設伺服器執行個體中的 SQL 執行個體的名稱。 <br/>*-forcecreate*︰ 強制建立 SQL 資料庫，即使已經存在。 <br/>*-同處理序*︰ 目前的程序，而不是產生新的程序中執行的初始化。 您必須啟動目前的程序，以提高權限，才能執行初始化。          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>儲存模擬器和 Azure 儲存體之間的差異

因為儲存模擬器是在本機的 SQL 執行個體中執行模擬的環境，有一些功能差異在模擬器和 Azure 儲存體帳戶之間雲端中︰

- 儲存模擬器支援只有單一固定的帳戶和知名驗證索引鍵。

- 儲存模擬器可調整儲存服務並不會不支援大量的同時用戶端。

- [在儲存模擬器中的地址設定資源](#addressing-resources-in-the-storage-emulator)所述，資源收件者以不同方式與 Azure 儲存體帳戶儲存模擬器中。 此處的差別因為的網域名稱解析有在雲端，但不是本機電腦上。

- 開始版本 3.1 的請儲存模擬器帳戶支援讀取權限地理重複試驗 (RA GRS)。 在模擬器中所有的帳戶有啟用，RA GRS，並有絕不允許任何延隔時間之間的主要和次要複本。 取得 Blob 服務狀態、 取得佇列中服務狀態和取得表格服務統計作業次要帳戶支援，一定會傳回的值`LastSyncTime`回應項目與目前的時間，根據基礎的 SQL 資料庫。

    以程式設計方式存取與儲存模擬器次要，用於.NET 3.2 或更新版本中的儲存空間的用戶端文件庫。 請參閱[Microsoft Azure 儲存用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)如需詳細資訊。

- 檔案的服務及中小企業通訊協定服務端點是目前不支援儲存模擬器中。

- 如果您使用的版本儲存服務尚未模擬器您正在使用的版本不支援的儲存空間模擬器會傳回 VersionNotSupportedByEmulator 錯誤 （HTTP 狀態碼 400-錯誤的要求）。

### <a name="differences-for-blob-storage"></a>Blob 儲存體的差異 

在模擬器中 Blob 儲存體適差異如下︰

- 儲存模擬器只支援 blob 大小 2 GB。

- 將 Blob 作業可能會針對 blob 儲存體模擬器中存在並有作用中的租用成功，即使租用 ID 沒有已指定為要求的一部分。 

- 新增的 Blob 模擬器不支援的作業。 嘗試新增 blob 的作業會傳回 FeatureNotSupportedByEmulator 錯誤 （HTTP 狀態碼 400-錯誤的要求）。

### <a name="differences-for-table-storage"></a>資料表儲存體的差異 

在模擬器中的資料表儲存體適差異如下︰

- 在儲存模擬器中的表格服務中的日期屬性支援只支援的 SQL Server 2005 範圍 （*亦即*，也就是需要晚於 1753 年 1 月 1 日）。 此值會變成 1753 年 1 月 1 日之前的所有日期。 SQL Server 2005，這表示的日期為 1 精確的精確度日期的精確度只限/300th 第二個。

- 儲存模擬器中支援小於 512 個位元組的每個的磁碟分割鍵和列鍵的屬性值。 此外，帳戶名稱的資料表名稱，與分成一組的屬性名稱的總大小不能超過 900 個位元組。

- 儲存模擬器中的表格中的資料列的總大小僅限於小於 1 MB。

- 在儲存模擬器中輸入資料的屬性`Edm.Guid`或`Edm.Binary`只支援`Equal (eq)`和`NotEqual (ne)`比較運算子，在查詢中的篩選字串。

### <a name="differences-for-queue-storage"></a>佇列中的儲存空間的差異

沒有佇列中儲存在模擬器中的特定的差異。

## <a name="storage-emulator-release-notes"></a>儲存模擬器版本資訊

### <a name="version-45"></a>4.5 版本

- 固定初始化和安裝失敗時備份資料庫已重新命名的儲存空間模擬器所造成的錯誤。

### <a name="version-44"></a>4.4 版本

- 儲存模擬器現在可支援儲存服務版本 2015年-12-11 Blob 與佇列中，表格服務端點上。

- 處理大量二進位大型物件時，現在更有效率的 blob 資料的儲存空間模擬器回收。

- 固定容器中如何儲存服務會驗證方式稍有不同的 ACL XML 原因所造成的錯誤。

- 修正錯誤，有時會造成最大] 和 [最小值的日期時間值，來報告不正確的時區。

### <a name="version-43"></a>版本 4.3

- 儲存模擬器現在可支援儲存服務版本 2015年-07-08 Blob 與佇列中，表格服務端點上。

### <a name="version-42"></a>4.2 版

- 儲存模擬器現在可支援儲存服務版本 2015年-04-05 Blob 與佇列中，表格服務端點上。

### <a name="version-41"></a>4.1 版

- 儲存模擬器現在支援 2015年-02-21 的版本儲存在上的服務 Blob 與佇列中，表格服務端點，但附加 Blob 的新功能。 

- 如果您使用的版本儲存服務尚未模擬器該版本不支援的儲存空間模擬器現在會傳回一個有意義的錯誤訊息。 我們建議使用最新版的模擬器。 如果您遇到 VersionNotSupportedByEmulator 錯誤 （HTTP 狀態碼 400-錯誤的要求），請下載最新版的儲存空間模擬器。

- 固定 bug 該處競爭所造成的條件表格的實體資料同時執行的合併作業期間，不正確。

### <a name="version-40"></a>4.0 版

- 可執行的儲存空間模擬器已重新命名*AzureStorageEmulator.exe*。

### <a name="version-32"></a>版本 3.2 捨位

- 儲存模擬器現在可支援儲存服務版本 2014年-02-14 Blob 與佇列中，表格服務端點上。 請注意，檔案的服務端點目前不支援儲存模擬器中。 如需版本 2014年-02-14 詳細資訊，請參閱[Azure 儲存服務的版本設定](https://msdn.microsoft.com/library/azure/dd894041.aspx)。

### <a name="version-31"></a>3.1 的版本

- 儲存模擬器現在支援讀取權限地理多餘的儲存空間 (RA GRS)。 取得 Blob 服務狀態、 取得佇列中服務 Stats 取得表格服務 Stats Api 的次要帳戶支援，並一定會傳回 LastSyncTime 回應項目的值為基礎的 SQL 資料庫根據目前的時間。 以程式設計方式存取與儲存模擬器次要，用於.NET 3.2 或更新版本中的儲存空間的用戶端文件庫。 如需詳細資訊，以.NET 參考看到 Microsoft Azure 儲存用戶端的文件庫。

### <a name="version-30"></a>3.0 版

- Azure 儲存模擬器不再隨附在同一個封裝為計算模擬器。

- 儲存模擬器圖形使用者介面功能已遭取代，而採用編寫指令碼的命令列介面。 命令列介面的詳細資訊，請參閱儲存模擬器命令列工具參照。 圖形化介面仍會出現在版本 3.0，但它只能存取計算模擬器已安裝在系統匣圖示上以滑鼠右鍵按一下，然後選取 [顯示的儲存空間模擬器使用者介面。

- 現在完全受支援版本 2013年-08-15 的儲存空間 Azure 服務。 (先前此版本只支援儲存模擬器版本 2.2.1 預覽。)
