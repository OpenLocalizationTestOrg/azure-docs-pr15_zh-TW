<properties
   pageTitle="整合與作業管理套件 (OMS) |Microsoft Azure"
   description="除了使用 OMS 的標準功能，您可以將它整合與其他管理應用程式和服務，提供混合式管理環境，提供您的環境專屬的自訂管理案例或提供自訂的管理您的客戶體驗。  本文提供不同的選項整合 OMS 與提供技術的詳細的資訊的文件連結的概觀。"
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>整合與作業管理套件 (OMS)

作業管理套件是 Microsoft 的雲端 IT 管理解決方案，可協助您管理及保護您的內部部署與雲端基礎結構。  除了使用 OMS 的標準功能，您可以將它整合與其他管理應用程式和服務，提供混合式管理環境，提供您的環境專屬的自訂管理案例或提供自訂的管理您的客戶體驗。  本文提供不同的選項整合 OMS 服務與提供技術的詳細的資訊的文件連結的概觀。 



## <a name="log-analytics"></a>記錄狀況分析
管理收集記錄分析的資料會儲存於儲存機制會裝載於 Azure。  記錄檔搜尋跨太大量的資料提供快速分析中使用儲存在存放庫中的所有資料都。  您整合需求可能會以新的資料提供的分析，填入存放庫或擷取提供新的視覺效果，或與其他管理工具整合存放庫中的資料。

每一筆存放庫中的資料會儲存為記錄。  當您填入存放庫時，您應該提供使用者使用您的方案的記錄類型和其內容的描述。  當您擷取資料時，您會需要您使用的資料的相關資訊。

![填入 OMS 存放庫](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>填入記錄分析存放庫
有多種方法填入 OMS 存放庫。  您使用的方式取決於來源資料的所在位置、 的資料，及格式等因素需要支援的用戶端。  當資料儲存在存放庫中時，且不會收集方式。

下列各節說明填入 OMS 存放庫不同的選項。

#### <a name="connected-sources-and-data-sources"></a>連線的來源與資料來源 
連線的來源是 OMS 存放庫可以擷取資料的位置。  資料來源解決方案連線來源上執行，並定義會收集的特定資料。  如果您的應用程式寫入到其中一個這些資料來源的資料，然後您可以收集其設定的資料來源。  例如，如果您的應用程式建立系統事件，然後他們可以收集系統的資料來源 Linux 代理程式。

- [記錄檔分析的資料來源](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>解決方案

解決方案延伸 OMS 的功能。  解決方案可能會收集資料來源的連線，或它可能已收集存放庫中的記錄上執行分析。  每個 Microsoft 提供的方案有提供詳細資料，它會收集的資料的個別文件。

- [在記錄檔分析的解決方案](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>HTTP 資料收集 API

記錄檔分析 HTTP 資料收集 API 是 REST API，可讓您新增記錄分析存放庫 JSON 資料。  當您不提供資料的其中一個其他資料來源或解決方案的應用程式時，您可以利用這個 API。  可以用於填入從任何用戶端，可以撥打 API，並不需要任何資料來源或解決方案的集合排程存放庫。

- [登入分析 HTTP 資料收集 API](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>擷取記錄分析存放庫中的資料

有多種方法從 OMS 存放庫擷取資料。  您可能會想要擷取資料使用 OMS console 的使用者，並向他們提供不同類型的視覺效果和分析。  您也可以從外部處理程序，例如另一種管理解決方案擷取資料。

#### <a name="log-searches"></a>記錄檔的搜尋

儲存在 OMS 存放庫中的所有資料都是透過記錄搜尋。  使用者可能會在 OMS 主控台執行自己臨機操作的分析，或使用特定的記錄檔搜尋的視覺效果中建立儀表板。  解決方案可以包含自訂檢視，以根據預先定義搜尋的視覺效果。  您可以使用記錄搜尋 API 存取 OMS 存放庫中的資料，從外部應用程式或管理工具。  

- [在記錄檔分析記錄搜尋](../log-analytics/log-analytics-log-searches.md)
- [記錄檔分析記錄搜尋 REST API](../log-analytics/log-analytics-log-search-api.md)
- [登入分析 cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>自訂檢視 
檢視設計工具可讓您在 OMS 主控台提供使用者在您的方案中的資料分析與視覺效果中建立自訂檢視。  每個檢視包含主控台及會根據您所定義記錄搜尋的視覺效果任何的部分數字的主頁面上所顯示的方塊。
  
- [記錄檔分析檢視設計工具](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Power BI

記錄檔分析可以自動將資料匯出 OMS 存放庫到 Power BI，您可以利用其視覺效果和分析工具。  執行此匯出的排程，資料會保留最新狀態。 

- [將記錄分析資料匯出至 Power BI](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>自動化

OMS 可以自動化程序來回應收集的資料，或執行其他管理功能。  它可能從您的應用程式收集資料，並將它插入 OMS 存放庫中，或您可能會自動回覆給存放庫中找到的資料中的已知問題的修正。 

![自動化](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks

在 Azure 自動化 Runbooks 執行 Azure 雲端中的 PowerShell 指令碼與工作流程。  您可以使用這些管理資源 Azure 或雲端可存取的任何其他資源。  Runbooks 也可以執行使用混合式 Runbook 工作者本機資料中心。  從 Azure 入口網站，或從外部使用多種方法，例如 PowerShell 或自動化 API 的處理程序，您就可以開始 runbook。

- [啟動 Azure 自動化 runbook](../automation/automation-starting-a-runbook.md)
- [Azure 自動化 cmdlet](https://msdn.microsoft.com/library/dn690262.aspx)
- [自動化 REST API](https://msdn.microsoft.com/library/mt662285.aspx)
- [自動化.NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>通知

自動執行根據排程的記錄檔搜尋提醒的規則。  如果結果符合特定準則所產生的提醒可以啟動 Azure 自動化 runbook，或連絡 webhook 可以啟動外部的程序。  這些回應其中可以包含提醒包括在記錄搜尋傳回的資料的詳細資料。

- [記錄檔分析的警示](../log-analytics/log-analytics-alerts.md)
- [記錄檔分析通知 API](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>備份及復原網站

Azure 備份及網站修復提供的服務保護您的企業資料，以及如何確保的伺服器和應用程式。  您可以利用這些服務，才能執行這類案例為提供備份您的應用程式的服務或初始虛擬機器的容錯移轉。

- [Azure 備份 cmdlet](https://msdn.microsoft.com/library/mt619253.aspx)
- [Azure 網站復原 REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Azure 網站復原 Cmdlet](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>自訂解決方案

您可以將整合邏輯封裝執行的工作區或客戶的工作區中的自訂解決方案。  您的方案可以包含任何整合方法除了其他資源可提供完整的管理案例本文中。  移除方案之後，所有它所建立的資源移除 OMS 工作區和 Azure 訂閱的封裝方案中的資源。

例如，您的方案包括收集並處理資料和然後填入使用 HTTP 資料收集 API 記錄分析存放庫自動化 runbook。  您也可以包含自訂檢視的顯示，並會收集的資料。  

- 建立自訂解決方案 （即將推出）    

## <a name="next-steps"></a>後續步驟
- 參照對自動化 OMS 服務相關技術資訊[OMS SDK](operations-management-suite-sdk.md) 。  
