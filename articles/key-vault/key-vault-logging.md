<properties
    pageTitle="Azure 鍵保存庫記錄 |Microsoft Azure"
    description="使用本教學課程中，以協助您開始使用 Azure 金鑰保存庫的記錄。"
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Azure 鍵保存庫記錄 #
Azure 鍵保存庫大部分地區都提供。 如需詳細資訊，請參閱[金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="introduction"></a>簡介  
建立一或多個鍵保存庫之後，您可能會想要監控您的主要保存庫方式和時機是存取，以及由誰。 您可以啟用金鑰保存庫，會將資訊儲存在您所提供的 Azure 儲存體帳戶中的記錄。 您指定的儲存的帳戶，會自動建立新的容器命名**獲得深入見解-記錄-auditevent** ，您可以使用此相同的儲存空間帳戶用來收集的多個鍵保存庫記錄。

您最多可以存取您的記錄資訊、 10 分鐘後鍵地窖作業。 在大部分情況下，就會更快速地超過此。  是由您來管理您儲存帳戶中的記錄︰

- 使用標準 Azure 存取控制的方法來保護您記錄限制誰能夠存取他們。
- 刪除您不再想要保留在您儲存帳戶中的記錄。

使用此教學課程以協助您開始使用 Azure 金鑰保存庫記錄，以建立您儲存的帳戶，啟用記錄]，與判讀準收集記錄資訊。  


>[AZURE.NOTE]  本教學課程中不包含如何建立索引鍵保存庫、 金鑰或機密資料的相關指示。 這項資訊，請參閱[開始使用 Azure 金鑰保存庫](key-vault-get-started.md)。 或者，跨平台命令列介面指示，請參閱[本教學課程中相同](key-vault-manage-with-cli.md)。
>
>目前您無法設定 Azure 金鑰保存庫 Azure 入口網站中。 請使用下列 PowerShell 的 Azure 指示進行。

使用記錄分析，從作業管理套件視覺化收集記錄。 如需詳細資訊，請參閱[Azure 金鑰保存庫 （預覽版本） 解決方案中記錄分析](../log-analytics/log-analytics-azure-key-vault.md)。

Azure 鍵保存庫相關的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## <a name="prerequisites"></a>必要條件

若要完成此教學課程中，您必須具備下列項目︰

- 現有金鑰保存庫您使用。  
- Azure PowerShell**最低 1.0.1 的版本**。 若要安裝 PowerShell 的 Azure，並將它與 Azure 訂閱關聯，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 如果您已安裝 PowerShell 的 Azure 並不知道版本，請從 Azure PowerShell 主控台，輸入`(Get-Module azure -ListAvailable).Version`。  
- 足夠的儲存空間上 Azure 金鑰保存庫記錄。


## <a id="connect"></a>連線至您的訂閱 ##

啟動 Azure PowerShell 工作階段，並登入您 Azure 帳戶使用了下列命令︰  

    Login-AzureRmAccount

在快顯的瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱和密碼。 Azure PowerShell 會收到所有的訂閱，此帳戶與預設相關聯，會使用第一個。

如果您有多個訂閱，您可能必須指定用來建立您的 Azure 金鑰保存庫的特定項目。 輸入下列命令以查看您的帳戶的訂閱︰

    Get-AzureRmSubscription

然後，如果要指定您將會記錄您金鑰保存庫與相關聯的訂閱，請輸入︰

    Set-AzureRmContext -SubscriptionId <subscription ID>

如需更多關於設定 PowerShell 的 Azure 的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。


## <a id="storage"></a>建立新的儲存空間客戶的記錄 ##

雖然您可以使用現有的儲存空間帳戶的記錄，我們要建立新的儲存空間帳戶會專門鍵保存庫記錄檔。 為了方便的當我們的指定此更新版本，我們會將詳細資料儲存成一個名為**索**變數。

以其他便於管理，我們也可以使用相同的資源群組為包含我們金鑰保存庫。 從[開始教學課程](key-vault-get-started.md)此資源群組為**ContosoResourceGroup** ，我們就會使用中式地址的位置，繼續]。 取代您自己的圖片，適用這些值︰

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  如果您決定要使用現有的儲存空間帳戶時，它必須為您的主要保存庫中使用同一份訂閱，而且它必須使用資源管理員部署模型，而不是傳統部署模型。

## <a id="identify"></a>識別記錄的索引鍵的保存庫 ##

在我們取得開始教學課程中，我們金鑰保存庫的名稱，我們會繼續使用該名稱，並將一個名為**kv**變數儲存詳細資料，讓已**ContosoKeyVault**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>啟用記錄功能 ##

若要啟用金鑰保存庫的記錄，我們將使用設定 AzureRmDiagnosticSetting cmdlet，與我們新的儲存空間帳戶和我們金鑰保存庫我們所建立的變數。 我們也可以設定**-啟用** **$true**的旗標和 AuditEvent （僅限類別鍵保存庫記錄），以設定類別︰


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

這個輸出包含︰

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


向您確認現在已為您的主要保存庫，將資訊儲存至您儲存的帳戶啟用記錄。

或者您也可以設定保留原則的記錄，例如會自動刪除較舊的記錄。 例如，設定使用**$true** **-RetentionEnabled**旗標的保留原則，並設定**90**的**-RetentionInDays**參數，以便早於 90 天的記錄會自動刪除。

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

什麼被記錄︰

- 記錄所有經過驗證的 REST API 邀請，其中包含失敗的要求存取權限、 系統錯誤或不正確的要求。
- 索引鍵的作業地窖本身，包括建立、 刪除、 設定金鑰保存庫存取原則，並更新金鑰保存庫屬性，例如標籤。
- 索引鍵和金鑰保存庫，包括建立、 修改或刪除這些按鍵或機密資料; 中的機密資料的作業確認作業等號後面，請加密、 解密、 自動換行列名鍵，取得機密資料、 清單索引鍵和機密資料，以及它們的版本。
- 導致 401 回應未經驗證的要求。 例如，要求沒有承載者權杖，或不正確或過期，或有無效的 token。  


## <a id="access"></a>存取您的記錄 ##

索引鍵保存庫記錄會儲存在您所提供的儲存空間帳戶的**深入見解-記錄-auditevent**容器。 若要在這個容器中的所有二進位大型物件] 清單中，輸入︰

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

輸出看起來會像這樣︰

**容器 Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**名稱**

**----**

**預設 = / 訂閱/361DA5D4-A47A-4 C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/提供者/MICROSOFT。KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**預設 = / 訂閱/361DA5D4-A47A-4 C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/提供者/MICROSOFT。KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

* * 預設 = / 訂閱/361DA5D4-A47A-4 C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/提供者/MICROSOFT。KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


二進位大型物件您可以從這個輸出看到，請遵循的命名慣例︰**預設 =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

使用 UTC 的日期和時間值。

相同的儲存空間帳戶可以用來收集的多個資源記錄中，因為 blob 名稱中的完整資源識別碼是存取或下載只需要 blob 十分有用。 但這樣做之前，先我們會如何下載所有二進位大型物件。

首先，建立要下載 blob 的資料夾。 例如︰

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

然後取得所有 blob 的清單︰  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

管道這份清單，透過 「 取得-AzureStorageBlobContent' 下載 blob 到我們的目的地資料夾︰

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

當您執行此第二個命令，**/**分隔符號 blob 名稱中的建立的完整資料夾結構下目的地資料夾，並下載並將 blob 儲存為檔案用於此結構。

若要選擇性地下載二進位大型物件，請使用萬用字元。 例如︰

- 如果您有多個鍵保存庫，並想要下載的一個按鍵保存庫記錄，名為 CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- 如果您有多個資源群組，若要下載只要資源群組的記錄，請使用`-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- 如果您要下載 2016 年 1 月的月的所有記錄，請使用`-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

現在，您準備好要開始了解功能在記錄中。 但之前移動到的您可能需要知道的取得 AzureRmDiagnosticSetting 兩個多個參數︰

- 若要查詢的金鑰保存庫資源診斷設定的狀態︰`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- 若要停用金鑰保存庫資源記錄︰`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>解譯鍵保存庫記錄 ##

個別的 blob 儲存為文字格式設定為 JSON blob。 這是範例記錄項目執行`Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


下表列出的欄位名稱和描述。


| 欄位名稱        | 描述 |
| ------------- |-------------|
| 時間      | 日期及時間 (UTC)。|
| 預設      | Azure 資源管理員資源識別碼。 為金鑰保存庫記錄檔，這是永遠鍵保存庫資源識別碼。|
| operationName      | 作業，如詳加說明下一個資料表的名稱。|
| operationVersion      | 這是用戶端要求 REST API 版本。|
| 類別      | 索引鍵保存庫記錄 AuditEvent 是單一，可值。|
| resultType      | REST API 邀請的結果。|
| resultSignature      | HTTP 狀態。|
| resultDescription     | 其他後的結果，可用的詳細描述。|
| durationMs      | 服務 REST API 邀請，以毫秒為單位所花費的時間。 不含網路延遲，讓您在用戶端測量的時間可能與這次不相符。|
| callerIpAddress      | 要求的用戶端 IP 位址。|
| 相互關聯識別碼      | 用戶端可以將傳遞給建立用戶端之間的關係選擇性 GUID 記錄與服務端 （鍵保存庫） 記錄。|
| 身分識別      | 從權杖進行 REST API 邀請時，會看到的身分識別。 這通常是要求的 「 使用者 」、 「 服務主要 」 或組合 」 使用者 + appId 」 與 Azure PowerShell 指令程式所產生的大小寫。|
| 屬性      | 此欄位會包含不同根據作業 (operationName) 的資訊。 在大部分情況下，包含 [用戶端資訊 （用戶端傳遞的使用者代理字串），精確 REST API 邀請 URI 和 HTTP 狀態碼。 此外，物件傳回當做要求 （例如，KeyCreate 或 VaultGet） 時，也會包含鍵 URI （為 「 識別碼 」）、 保存庫 URI 或私人 URI。|




**OperationName**欄位的值為 ObjectVerb 格式。 例如︰

- 所有的金鑰保存庫作業有 「 保存庫`<action>`」 格式，例如`VaultGet`和`VaultCreate`。

- 所有的金鑰作業有 」 鍵`<action>`」 格式，例如`KeySign`和`KeyList`。

- 所有的私人作業有 ' 私人`<action>`」 格式，例如`SecretGet`和`SecretListVersions`。

下表列出的 operationName 及相對應的 REST API] 命令。

| operationName        | REST API] 命令 |
| ------------- |-------------|
| 驗證      | 透過 Azure Active Directory 端點|
| VaultGet      | [取得金鑰保存庫的相關資訊](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [建立或更新索引鍵保存庫](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [刪除索引鍵保存庫](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [更新索引鍵保存庫](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [清單中的 [資源] 群組中的所有按鍵保存庫](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [建立索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [取得金鑰的相關資訊](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [匯入保存庫](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [備份鍵](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx)。|
| KeyDelete      | [刪除索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [還原索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [登入與索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [驗證與索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [自動換行鍵](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [列名索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [加密金鑰](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [使用金鑰解密](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [更新索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [清單保存庫中的索引鍵](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [索引鍵的版本] 清單](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [建立密碼](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [取得私人](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [更新密碼](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [刪除一個的秘訣](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [保存庫中的清單機密](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [列出版本的密碼](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>後續步驟 ##

在 web 應用程式中使用 Azure 金鑰保存庫的教學課程，請參閱[使用 Azure 金鑰保存庫從 Web 應用程式](key-vault-use-from-web-application.md)。

程式設計參照，請參閱[Azure 金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

適用於 Azure 金鑰保存庫 Azure PowerShell 1.0 指令程式的清單，請參閱[Azure 金鑰保存庫 Cmdlet](https://msdn.microsoft.com/library/azure/dn868052.aspx)。

索引鍵的旋轉和記錄稽核與 Azure 金鑰保存庫上的教學課程，瞭解[如何設定金鑰保存庫的端對端鍵旋轉及稽核](key-vault-key-rotation-log-monitoring.md)。
