<properties
    pageTitle="Azure 政府文件 |Microsoft Azure"
    description="此提供功能及的比較開發 Azure 政府版的應用程式"
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
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Azure 政府版資料庫

##  <a name="sql-database"></a>SQL 資料庫

請參閱<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx">Microsoft 資訊安全中心 SQL 資料庫引擎</a>和[Azure SQL 資料庫公用文件](https://azure.microsoft.com/documentation/services/sql-database/)的額外指引中繼資料可見度設定及保護最佳作法。

### <a name="variations"></a>變化

Azure 政府推出 SQL V12 資料庫。

SQL Azure 中的伺服器 Azure 政府版的地址是不同的︰

服務類型|Azure 公用|Azure 政府版
---|---|---
SQL 資料庫|*。 database.windows.net|*。 database.usgovcloudapi.net

### <a name="considerations"></a>考量

下列資訊識別 Azure SQL Azure 政府邊界︰

| 允許管理/控制資料 | 不允許的管理/控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 儲存，並在 Microsoft Azure SQL 中處理的所有資料都可以都包含 Azure 政府管理資料。 您必須使用資料庫工具 Azure 政府管理資料的資料傳輸。 | 包含匯出控制資料不允許 azure SQL 中繼資料。 這個中繼資料包含所有設定資料輸入時建立和維護您儲存的產品。  執行不受規範/控制將資料輸入下列欄位︰ 資料庫名稱的訂閱名稱、 資源群組、 伺服器名稱、 伺服器管理員登入、 部署名稱、 資源名稱、 資源標籤

## <a name="azure-redis-cache"></a>Azure 意指快取

這項服務，以及如何使用它的詳細資訊，請參閱[Azure Redis 快取公用的文件](https://azure.microsoft.com/documentation/services/redis-cache/)。

### <a name="variations"></a>變化

存取及管理 Azure Redis 快取中 Azure 政府 Url 有不同︰

服務類型|Azure 公用|Azure 政府版
---|---|---
快取端點|*。 redis.cache.windows.net|*。 redis.cache.usgovcloudapi.net
Azure 入口網站|https://portal.azure.com|https://portal.azure.us

>[AZURE.NOTE] 所有的指令碼和程式碼需要的適當的端點和環境。 如需詳細資訊，請參閱[如何連線至 Azure 政府雲端](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud)。


### <a name="considerations"></a>考量

下列資訊識別 Azure Redis 快取 Azure 政府邊界︰

| 允許管理/控制資料 | 不允許的管理/控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 儲存及處理 Azure Redis 快取中的所有資料都可以都包含 Azure 政府管理資料。 | 包含匯出控制資料不允許 azure Redis 快取中繼資料。 執行不受規範/控制將資料輸入下列欄位︰ 快取名稱、 VNET 名稱、 訂閱名稱、 資源群組、 資源標記、 意指屬性。  

##  <a name="next-steps"></a>後續步驟

補充資訊和更新訂閱<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府部落格。</a>
