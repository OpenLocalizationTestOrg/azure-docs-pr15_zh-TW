<properties
    pageTitle="Azure 鍵保存庫解決方案中記錄分析 |Microsoft Azure"
    description="您可以使用記錄分析中 Azure 金鑰保存庫解決方案檢閱 Azure 金鑰保存庫記錄檔。"
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="richrund"/>

# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Azure 鍵保存庫 （預覽版本） 解決方案中記錄狀況分析

>[AZURE.NOTE] 這是[預覽解決方案](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)。

您可以使用記錄分析中 Azure 金鑰保存庫解決方案檢閱 Azure 金鑰保存庫 AuditEvent 記錄檔。

您可以啟用記錄功能 Azure 金鑰保存庫的稽核的事件。 Azure Blob 儲存體位置他們可以然後能編製索引的記錄檔分析來搜尋及分析寫入這些記錄。

## <a name="install-and-configure-the-solution"></a>安裝和設定方案

使用下列指示來安裝和設定 Azure 金鑰保存庫方案︰

1.  啟用您要監視的[診斷記錄的索引鍵保存庫](../key-vault/key-vault-logging.md)資源
2.  設定記錄分析讀取 blob 儲存體中的記錄，使用[JSON 檔案 blob 儲存體中的](../log-analytics/log-analytics-azure-storage-json.md)所述的程序。
3.  使用 [[新增記錄分析解決方案從方案庫](log-analytics-add-solutions.md)中所述的程序，以啟用 Azure 金鑰保存庫解決方案。  

## <a name="review-azure-key-vault-data-collection-details"></a>檢閱 Azure 金鑰保存庫集合詳細資料

Azure 鍵保存庫方案會從 Azure blob 儲存體診斷記錄收集的 Azure 金鑰保存庫。
沒有代理程式時需要資料集合。

下表顯示資料集合方法和其他詳細資料收集的 Azure 金鑰保存庫的方式。

| 平台 | 直接代理程式 | 系統管理中心的作業管理員 (是 SCOM) 代理程式 | Azure 儲存體 | 必要時，是 SCOM 嗎？ | 透過管理群組傳送是 SCOM 代理程式的資料 | 集合頻率 |
|---|---|---|---|---|---|---|
|Azure|![無](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![無](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![[是]](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![無](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![無](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10 分鐘的時間|

## <a name="use-azure-key-vault"></a>使用 Azure 金鑰保存庫

安裝解決方案之後，您可以檢視一段時間的使用**Azure 金鑰保存庫**您監視鍵保存庫的狀態] 磚的要求的摘要記錄分析的 [**概觀**] 頁面上。

![Azure 鍵保存庫磚的圖像](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

按一下 [**概觀**] 方塊之後，您可以檢視記錄檔的摘要，然後切入至下列類別的詳細資料︰

- 一段時間的所有按鍵保存庫作業的音量
- 無法作業區一段時間
- 平均作業的操作延遲
- 作業作業的記錄超過 1000 ms 數] 及 [作業的記錄超過 1000 ms 清單與服務的品質

![Azure 鍵保存庫儀表板的圖像](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Azure 鍵保存庫儀表板的圖像](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>若要檢視任何作業的詳細資料

1. 在 [**概觀**] 頁面上，按一下 [ **Azure 金鑰保存庫**磚。
2. 在**Azure 金鑰保存庫**儀表板，檢閱刀，其中的摘要資訊，然後按一下其中一個檢視中記錄的 [搜尋] 頁面的詳細的資訊。

    在任何記錄搜尋頁面，您可以檢視結果，時間、 詳細的結果，以及您記錄搜尋歷程記錄。 您也可以篩選 facet 來縮小結果。

## <a name="log-analytics-records"></a>記錄狀況分析

Azure 鍵保存庫解決方案分析**KeyVaults**類型的 Azure 診斷中[AuditEvent 記錄](../key-vault/key-vault-logging.md)中收集的記錄。  下表中，是這些記錄的屬性。  

| 屬性 | 描述 |
|:--|:--|
| 類型 | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| CallerIpAddress | 要求的用戶端 IP 位址 |
| 類別      | 索引鍵保存庫記錄 AuditEvent 是單一，可值。|
| 相互關聯識別碼 | 用戶端可以將傳遞給建立用戶端之間的關係選擇性 GUID 記錄與服務端 （鍵保存庫） 記錄。 |
| DurationMs | 服務 REST API 邀請，以毫秒為單位所花費的時間。 讓時間量在用戶端可能不符合這次不含網路延遲。 |
| HttpStatusCode_d | 傳回由要求 HTTP 狀態碼 |
| Id_s       | 要求的唯一識別碼 |
| Identity_o | 從權杖進行 REST API 邀請時，會看到的身分識別。 這通常是要求的 「 使用者 」、 「 服務主要 」 或組合 」 使用者 + appId 」 與 Azure PowerShell 指令程式所產生的大小寫。 |
| OperationName      | 作業，如詳加說明[Azure 金鑰保存庫記錄](../key-vault/key-vault-logging.md)的名稱|
| OperationVersion      | 用戶端要求的 REST API 版本|
| RemoteIPLatitude | Latitude 進行要求的用戶端 |
| RemoteIPLongitude | Longitude 進行要求的用戶端 |
| RemoteIPCountry | 要求的用戶端的國家/地區  |
| RequestUri_s | 要求 Uri |
| 資源   | 索引鍵保存庫的名稱 |
| ResourceGroup | 資源群組的索引鍵保存庫 |
| 預設 | Azure 資源管理員資源識別碼。 為金鑰保存庫記錄檔，這是永遠鍵保存庫資源識別碼。 |
| ResourceProvider | *MICROSOFT。KEYVAULT* |
| ResultSignature  | HTTP 狀態|
| ResultType      | REST API 邀請的結果|
| SubscriptionId | 包含金鑰保存庫的訂閱的 azure 訂閱識別碼 |


## <a name="next-steps"></a>後續步驟

- 您可以使用[記錄分析中的記錄檔搜尋](log-analytics-log-searches.md)來檢視 Azure 金鑰保存庫的詳細的資料。
