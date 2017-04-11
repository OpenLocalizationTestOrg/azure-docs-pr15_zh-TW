<properties
   pageTitle="資源管理員支援服務 |Microsoft Azure"
   description="說明支援資源管理員、 其結構描述和可用的 API 版本，並將銷售區域可裝載資源的資源提供者。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>資源管理員提供者、 區域、 API 版本和結構描述

Azure 資源管理員提供您的部署及管理服務應用程式的新方法。 大部分的但非全部服務支援資源管理員與某些服務支援資源管理員只有部分。 本主題提供的支援的資源提供者的 Azure 資源管理員。

在部署您的資源時，您也必須知道哪些地區支援這些資源，以及哪些 API 版本可供資源。 區段[支援區域](#supported-regions)會顯示您如何找出您的訂閱和資源的哪些區域工作。 [支援的 API 版本](#supported-api-versions)] 區段會顯示如何判斷您可以使用哪些 API 版本。

Azure 入口網站和傳統入口網站中支援哪些服務，請參閱[Azure 入口網站的可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)。 若要查看哪些服務支援移動的資源，請參閱[移動到新的資源群組或訂閱的資源](resource-group-move-resources.md)。

下表列出的 Microsoft 服務的支援部署及管理資源管理員，哪些不透過。 [**快速入門範本**] 欄中的連結將查詢傳送到 Azure 快速入門範本存放庫以指定的資源提供者。 快速入門範本會新增，並經常更新。 連結某個服務的目前狀態不一定表示此查詢會傳回從存放庫的範本。 此外，還有許多協力廠商資源提供者提供的支援資源管理員。 您瞭解如何查看[資源提供者與類型](#resource-providers-and-types)] 區段中的所有資源提供者。


## <a name="compute"></a>計算

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------------------------ |-------- | ------ | ------ |
| 批次   | [是]     | [批次其餘](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|容器 | [是] | [容器服務其餘](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Dynamics 生命週期服務 | [是]  |      |        |  |
| 縮放設定 | [是] | [縮放比例設定其餘](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| 服務布料的轉印圖樣 | [是]  | [服務布料的轉印圖樣其餘](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| 虛擬機器 | [是] | [VM 其餘](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| 虛擬機器 （傳統） | 限制 | - | - | - |
| 遠端應用程式 | 無      | -  | - | - |
| 雲端服務 （傳統） | 限制 （如下所示） | - | - | - |

虛擬機器 （傳統） 指的是透過傳統部署模型，而不是部署透過資源管理員部署模型的資源。 一般而言，這些資源不支援資源管理員作業，但已啟用某些作業。 如需有關這些部署模型的詳細資訊，請參閱[瞭解資源管理員部署及傳統部署](resource-manager-deployment-model.md)。 

可利用其他傳統的資源; 使用雲端服務 （傳統）不過，傳統的資源不會利用的資源管理員的所有功能，而且不是很好的選項，供日後的解決方案。 不過，請考慮變更您的應用程式基礎結構，以使用 Microsoft.Compute Microsoft.Storage，與 Microsoft.Network 的命名空間中的資源。


## <a name="networking"></a>網路

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | -------  | -------- | ------ | ------ |
| 應用程式的閘道器 | [是] | [應用程式閘道其餘](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | [是] | [DNS 其餘](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016 04 01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | [是]  | [其餘 ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| 負載平衡器 | [是]  | [其餘的負載平衡器](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| 流量管理員 | [是] | [流量管理員其餘](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| 虛擬網路 | [是]| [虛擬網路其餘](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| VPN 閘道器 | [是] | [閘道器其餘的網路](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[連線](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>儲存空間

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------- | ------ |
| 儲存空間 | [是]  | [儲存其他](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [儲存帳戶](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | [是]  |         |        |  |

## <a name="databases"></a>資料庫

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | [是]  | [DocumentDB 其餘](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis 快取 | [是] |   | [2016 04 01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| SQL 資料庫 | [是] | [其餘的 SQL 資料庫](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014 04-01-預覽](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse | [是] |   |      |


## <a name="web--mobile"></a>網頁及 Mobile

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------ |
| API 應用程式 | [是] |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [API 應用程式](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| API 管理 | [是]  | [API 管理其餘](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016 07 07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| 仲裁者的內容 | [是] |   |   |   |
| 函數應用程式 | [是] |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| 邏輯應用程式 | [是]   | [工作流程服務 REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| 行動應用程式 | [是] |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| 行動裝置的互動 | [是]  |  [行動互動其餘](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| 搜尋 | [是]  | [搜尋其餘](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Web 應用程式 | [是] |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>分析

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | -------  | -------- | ------ | ------ |
| 資料目錄 | [是]  | [資料目錄其餘](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| 資料工廠 | [是] | [資料工廠其餘](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| 資料湖狀況分析 | [是] |   |   [2015-10-01-預覽](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| 資料湖存放區 | [是]  | [資料湖存放其餘](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [2015-10-01-預覽](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | [是] | [HDInsights 其餘](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| 電腦學習 | [是] | [學習其餘的電腦](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [2016 05-01-預覽](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| 資料流狀況分析 | [是]  | [資料流分析其餘](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Power BI | [是] | [Power BI 內嵌其餘](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [2016-01-29](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>智慧

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------ |
| 認知服務 | [是] |  | [2016 02-01-預覽](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet 的項目

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------ |
| 事件中心 | [是]  | [事件中心其餘](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | [是] | [IoT 中心其餘](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| 通知集線器 | [是] | [通知中心其餘](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015 04 01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>媒體及 CDN

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------ |
| CDN | [是] | [CDN 到底其餘](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [2016-04-02](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| 媒體服務 | [是] | [媒體服務其餘](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>混合式整合

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------ |
| BizTalk 服務 | [是] |          | [2014 04 01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| 修復服務 | [是] | [網站復原其餘](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| 服務匯流排 | [是] | [服務匯流排其餘](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>身分識別與管理存取 

Azure Active Directory 運作方式與資源管理員可以啟用角色型存取控制您的訂閱。 若要瞭解如何使用角色型存取控制] 和 [作用中的目錄，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

## <a name="developer-services"></a>開發人員服務 

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------ |
| 應用程式的深入見解 | [是]  | [其餘的深入見解](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014 04 01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing 地圖服務 | [是]  |          |        |  |
| DevTest 實驗室 | [是] |   | [2016 05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Visual Studio 帳戶 | [是]   |          | [2014-02 26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>管理與安全性

| 服務 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | ------ | ------ |
| 自動化 | [是] | [自動化其餘](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| 索引鍵保存庫 | [是] | [索引鍵保存庫其餘](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [索引鍵保存庫](resource-manager-template-keyvault.md)<br />[索引鍵保存庫密碼](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| 操作的深入見解 | [是] |          |        |  |
| 排程器 | [是]  | [排程器其餘](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016 03 01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| 安全性 （預覽版本） | [是] | [其他安全性](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>資源管理員

| 功能 | 資源管理員已啟用 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| 授權 | [是] | [管理鎖定](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[角色型存取控制](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [資源鎖定](resource-manager-template-lock.md)<br />[角色指派](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| 資源 | [是] | [連結的資源](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [資源的連結](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>資源提供者與類型

在部署資源時，您經常要擷取類型與資源提供者的相關資訊。 您可以擷取透過 REST API、 PowerShell 的 Azure 或 Azure CLI 這項資訊。

若要使用的資源提供者，該資源提供者必須向您的帳戶。 根據預設，許多資源提供者會自動為您註冊;不過，您可能需要手動註冊某些資源提供者。 下列範例顯示如何取得資源提供者的登錄狀態，如有需要註冊資源提供者。

### <a name="portal"></a>入口網站

您可以輕鬆地查看的支援的資源提供者，執行下列步驟︰

1. 選取 [**我的權限**]。

    ![我的權限](./media/resource-manager-supported-services/my-permissions.png)

2. 選取**資源提供者狀態**

    ![資源提供者狀態](./media/resource-manager-supported-services/resource-provider-status.png)

3. 您會看到您的訂閱的資源提供者的完整清單。

    ![清單資源提供者](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>REST API

若要取得所有可用的資源的提供者，包括其類型、 位置、 API 版本，以及登錄狀態，會使用的[所有資源的提供者] 都清單中](https://msdn.microsoft.com/library/azure/dn790524.aspx)的作業。 如果您需要註冊資源提供者，請參閱[註冊資源提供者的訂閱](https://msdn.microsoft.com/library/azure/dn790548.aspx)。

### <a name="powershell"></a>PowerShell

下列範例會示範如何取得所有可用的資源提供者。

    Get-AzureRmResourceProvider -ListAvailable
    

下一個範例會示範如何取得特定資源提供者的資源類型。

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
輸出非常類似︰

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
若要註冊資源提供者，提供命名空間︰

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Azure CLI

下列範例會示範如何取得所有可用的資源提供者。

    azure provider list
    
輸出非常類似︰

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

您可以使用下列命令檔案儲存特定的資源提供者的資訊。

    azure provider show Microsoft.Web -vv --json > c:\temp.json

若要註冊資源提供者，提供命名空間︰

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>支援的區域

在部署資源時，您通常要指定資源的區域。 資源管理員支援在所有區域中，但您部署資源可能不支援在所有區域內。 此外，可能會限制您的訂閱，無法使用某些區域的支援資源。 這些限制可能與稅您常用國家/地區的問題有關或原則的結果放入您的訂閱系統管理員，使用特定的區域。 

所有受支援的區域，所有的 Azure 服務的完整清單，請參閱[依地區服務](https://azure.microsoft.com/regions/#services);不過，這份清單，可能會包含您的訂閱不支援的區域。 您可以判斷您的訂閱支援透過網站、 REST API、 PowerShell 或 Azure CLI 特定的資源類型的區域。

### <a name="portal"></a>入口網站

您可以看到支援的區域的資源類型執行下列步驟︰

1. 選取 [**更多服務** > **資源檔案總管**。

    ![資源檔案總管](./media/resource-manager-supported-services/select-resource-explorer.png)

2. 開啟**提供者**節點。

    ![選取 [提供者](./media/resource-manager-supported-services/select-providers.png)

3. 選取資源提供者，並檢視的受支援的區域及 API 版本。

    ![檢視提供者](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST API

要找出哪一個區域可供您的訂閱中的特定的資源類型，使用的[所有資源的提供者] 都清單中](https://msdn.microsoft.com/library/azure/dn790524.aspx)的作業。 

### <a name="powershell"></a>PowerShell

下列範例會示範如何取得網站的受支援的區域。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
輸出非常類似︰

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>Azure CLI

下列範例會傳回所有的每個資源類型的支援位置。

    azure location list

您也可以篩選 JSON 公用程式，例如[jq](https://stedolan.github.io/jq/)位置的結果。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

傳回的︰

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>支援的 API 版本

當您部署範本時，您必須指定要用來建立的每個資源的 API 版本。 API 版本對應的 REST API 作業資源提供者所發行版本。 為資源提供者啟用新功能，請將其版本 REST API 的新版本。 因此，您指定在範本中的 api 版本會影響您可以指定在範本中的屬性。 一般而言，您會想要建立範本時，請選取最新的 API 版本。 現有的範本，您可以決定您是否要繼續使用舊版的 API 版本或更新您的範本，以善用新功能的最新版本。

### <a name="portal"></a>入口網站

您可以決定所支援的 API 版本以相同的方式，您決定支援區域 （先前所示）。

### <a name="rest-api"></a>REST API

若要探索 API 版本可供使用的資源類型、 使用的[所有資源的提供者] 都清單中](https://msdn.microsoft.com/library/azure/dn790524.aspx)的作業。 

### <a name="powershell"></a>PowerShell

下列範例會示範如何針對特定的資源類型取得可用的 API 版本。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
輸出非常類似︰
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>Azure CLI

您可以使用下列命令檔案的資源提供者儲存 （包括可用的 API 版本） 的資訊。

    azure provider show Microsoft.Web -vv --json > c:\temp.json

您可以開啟檔案，並尋找**apiVersions**的項目

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立資源管理員範本，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 若要瞭解部署資源，請參閱[部署具有 Azure 資源管理員範本的應用程式](resource-group-template-deploy.md)。
