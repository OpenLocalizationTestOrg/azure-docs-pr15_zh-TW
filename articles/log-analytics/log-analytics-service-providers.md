<properties
    pageTitle="登入服務提供者的分析功能 |Microsoft Azure"
    description="記錄檔分析可協助管理服務提供者 (MSPs)，大型企業，獨立軟體廠商 (Isv) 並主機服務提供者管理並監控伺服器的客戶的內部部署或雲端基礎架構。"
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
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>服務提供者的記錄檔分析功能

受管理的服務提供者 (MSPs)、 大型企業、 獨立軟體廠商 (Isv) 及主機服務提供者管理並監控伺服器的客戶的內部部署或雲端基礎架構，可協助記錄分析。 

大型企業許多的相似與共用服務提供者，特別是在有負責管理集中的 IT 小組時，才有許多不同業務單位的 IT。 為求，這份文件使用字詞*服務提供者*，但相同功能也適用於企業和其他客戶。

## <a name="cloud-solution-provider"></a>雲端解決方案提供者

合作夥伴與服務提供者的[雲端解決方案提供者 (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview)程式的一部分，記錄分析是其中一個可用的 CSP 訂閱的 Azure 服務。 

記錄檔分析中*雲端解決方案提供者*訂閱會啟用下列功能。

*雲端解決方案提供者*為您可以︰

+ 建立的租用戶 （客戶） 訂閱中的記錄檔分析工作區。
+ Access 工作區建立的租用戶。 
+ 新增和移除使用者存取使用 Azure 使用者管理工作區。 當 OMS 入口網站中的租用戶的工作區中的使用者管理頁面下時設定無法使用
  - 記錄檔分析不支援角色型存取還-授與使用者`reader`Azure 入口網站中的權限可讓他們在 OMS 入口網站中進行設定變更

若要登入租用戶的訂閱，必須指定租用戶識別碼。 租用戶識別碼，通常會用來登入的電子郵件地址的最後一部分。

+ 在 [OMS 入口網站中新增`?tenant=contoso.com`入口網站的 URL 中。 例如，`mms.microsoft.com/?tenant=contoso.com`
+ 在 PowerShell 中使用`-Tenant contoso.com`時使用參數`Add-AzureRmAccount`cmdlet
+ 當您使用時，就會自動新增租用戶識別碼`OMS portal`從 Azure 入口網站，以開啟並登入所選取的工作區的 OMS 入口網站的連結

為*客戶*的雲端解決方案提供者，您可以︰

+ 建立記錄檔 CSP 訂閱中的分析工作區
+ 建立 CSP 存取工作區
  -  使用`OMS portal`從 Azure 入口網站，以開啟並登入所選取的工作區的 OMS 入口網站的連結
+ 檢視與使用中的使用者管理頁面下設定 OMS 入口網站

>[AZURE.NOTE] 記錄檔分析的備份與還原網站的解決方案無法連線至修復服務保存庫，而且無法設定 CSP 訂閱中。

## <a name="managing-multiple-customers-using-log-analytics"></a>管理多個客戶使用記錄狀況分析 

建議您在每個客戶管理建立記錄分析工作區。 記錄檔分析工作區提供︰

+ 儲存資料的地理位置。 
+ 資料粒度的帳單 
+ 獨立資料 
+ 唯一的設定

藉由建立工作區，每位客戶，您就可以分開每個客戶資料，以及也追蹤每位客戶的使用方式。

何時及為什麼来建立多個工作區的詳細說明 [管理記錄分析資料的存取權] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need)。

使用[PowerShell](log-analytics-powershell-workspace-configuration.md)[資源管理員範本](log-analytics-template-workspace-configuration.md)，或使用[REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/)，可以自動建立及設定客戶的工作區。

資源管理員範本用於工作區設定可讓您可以用來建立及設定工作區主設定。 您可以放心的工作區的客戶建立時它們會自動設定您的需求。 當您更新您的需求時，範本會更新，然後重新套用現有的工作區。 此程序可確保甚至現有的工作區符合您的新標準。    

當管理多個記錄分析工作區，我們建議您現有的票券系統整合各個工作區操作主控台使用[通知](log-analytics-alerts.md)功能。 與現有的系統整合，支援人員會持續追蹤他們熟悉的程序。 記錄檔分析定期檢查通知您指定的準則針對每個工作區，並需要採取動作時，會產生警示。

高階主管的層級報表的摘要資料跨工作區中，您可以使用記錄分析及[中](log-analytics-powerbi.md)之間的整合。 如果您需要的其他報告系統整合，您可以使用搜尋 API （透過 PowerShell 或[其他](log-analytics-log-search-api.md)） 若要執行查詢並匯出搜尋結果。

## <a name="next-steps"></a>後續步驟

+ 自動建立和使用[資源管理員範本](log-analytics-template-workspace-configuration.md)的工作區設定
+ 將工作區使用[PowerShell](log-analytics-powershell-workspace-configuration.md)的建立作業自動化 
+ 使用[提醒](log-analytics-alerts.md)來與現有的系統整合
+ 產生使用[中](log-analytics-powerbi.md)的摘要報表
