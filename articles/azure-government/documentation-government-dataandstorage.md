<properties
    pageTitle="Azure 政府文件 |Microsoft Azure"
    description="此提供功能及指引的比較開發 Azure 政府版的應用程式"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="09/30/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-data-and-storage"></a>Azure 政府資料和儲存

##  <a name="azure-storage"></a>Azure 儲存體

這項服務，以及如何使用它的詳細資訊，請參閱[Azure 儲存體公用的文件](https://azure.microsoft.com/documentation/services/storage/)。

### <a name="variations"></a>變化

Azure 政府版中的儲存空間帳戶的 Url 是不同的︰

服務類型|Azure 公用|Azure 政府版
---|---|---
Blob 儲存體|*。 blob.core.windows.net|*。 blob.core.usgovcloudapi.net
佇列中的儲存空間|*。 queue.core.windows.net|*。 queue.core.usgovcloudapi.net
資料表儲存體|*。 table.core.windows.net| *。 table.core.usgovcloudapi.net

>[AZURE.NOTE] 所有的指令碼和程式碼需要的適當的端點。  請參閱[設定 Azure 儲存空間的連接字串](../storage-configure-connection-string.md#creating-a-connection-string-to-the-explicit-storage-endpoint)。 

如需有關 Api，請參閱<a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">雲端儲存空間帳戶建構函式</a>。

若要在這些多載中使用的端點後置字元是 core.usgovcloudapi.net 

### <a name="considerations"></a>考量

下列資訊識別 Azure 政府邊界 Azure 儲存空間︰

| 允許管理/控制資料 | 不允許的管理/控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 資料輸入，儲存，和 Azure 儲存體中處理產品可以包含控制匯出資料。 靜態驗證器，例如密碼和 Azure 平台元件以存取智慧卡 Pin。 用來管理 Azure 平台元件憑證私密金鑰。 其他安全性資訊/機密資料，例如憑證、 加密金鑰、 主索引鍵和儲存 Azure 服務中的儲存空間金鑰。 | 包含匯出控制資料不允許 azure 儲存中繼資料。 這個中繼資料包含所有設定資料輸入時建立和維護您儲存的產品。  不要將下列欄位輸入 Regulated/控制資料︰ 資源群組、 部署名稱、 資源名稱、 資源標籤  

##  <a name="premium-storage"></a>進階版儲存空間

這項服務，以及如何使用它的詳細資訊，請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)。

###  <a name="variations"></a>變化

通常 USGov 維吉尼亞州中使用進階版儲存空間。 這包含 DS 數列虛擬機器。 

### <a name="considerations"></a>考量

進階版儲存帳戶套用相同的儲存空間資料考量列於上方。 

##  <a name="sql-database"></a>SQL 資料庫

請參閱<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx">Microsoft 資訊安全中心 SQL 資料庫引擎</a>和[Azure SQL 資料庫公用文件](https://azure.microsoft.com/documentation/services/sql-database/)的其他指引中繼資料可見度設定及保護最佳作法。

### <a name="variations"></a>變化

Azure 政府推出 SQL V12 資料庫。

SQL Azure 中的伺服器 Azure 政府版的地址是不同的︰

服務類型|Azure 公用|Azure 政府版
---|---|---
SQL 資料庫|*。 database.windows.net|*。 database.usgovcloudapi.net

### <a name="considerations"></a>考量

下列資訊識別 Azure 政府邊界 Azure 儲存空間︰

| 允許管理/控制資料 | 不允許的管理/控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 儲存及處理 Microsoft Azure SQL 中的所有資料都可以都包含 Azure 政府管理資料。 您必須使用資料庫工具 Azure 政府管理資料的資料傳輸。 | 包含匯出控制資料不允許 azure SQL 中繼資料。 這個中繼資料包含所有設定資料輸入時建立和維護您儲存的產品。  執行不受規範/控制將資料輸入下列欄位︰ 資料庫名稱的訂閱名稱、 資源群組、 伺服器名稱、 伺服器管理員登入、 部署名稱、 資源名稱、 資源標籤

##  <a name="next-steps"></a>後續步驟

補充資訊和更新訂閱<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府部落格。</a>
