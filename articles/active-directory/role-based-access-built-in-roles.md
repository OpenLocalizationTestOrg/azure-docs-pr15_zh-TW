<properties
    pageTitle="RBAC︰ 內建的角色 |Microsoft Azure"
    description="本主題說明內建的角色型存取控制 (RBAC) 的角色。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC︰ 內建的角色

Azure 角色型存取控制 (RBAC) 隨附下列可以指派給使用者、 群組和服務的內建角色。 您無法修改定義的內建的角色。 不過，您可以建立[自訂角色 Azure RBAC](role-based-access-control-custom-roles.md)以符合特定貴組織的需求。

## <a name="roles-in-azure"></a>Azure 中的角色

下表提供的內建的角色的簡短描述。 按一下 [角色名稱即可查看**動作**和**notactions**角色的詳細的清單。 [**動作**] 屬性指定 Azure 資源允許的動作。 巨集指令字串可以使用萬用字元。 **Notactions**屬性會指定排除在允許的動作的動作。

>[AZURE.NOTE] Azure 角色定義持續不斷發展的。 這份文件保持為最新狀態，但您隨時都可以 PowerShell 的 Azure 尋找最新的角色定義。 使用 cmdlet`(get-azurermroledefinition "<role name>").actions`或`(get-azurermroledefinition "<role name>").notactions`視。

| 角色名稱 | 描述 |
| --------- | ----------- |
| [API 管理服務參與者](#api-management-service-contributor) | 可管理的 API 管理服務 |
| [應用程式的深入見解元件參與者](#application-insights-component-contributor) | 管理應用程式的深入見解元件 |
| [自動化運算子](#automation-operator) | 啟動、 停止、 暫停，並繼續工作 |
| [BizTalk 參與者](#biztalk-contributor) | 管理 BizTalk 服務 |
| [參與者 ClearDB MySQL 資料庫](#cleardb-mysql-db-contributor) | 管理 ClearDB MySQL 資料庫 |
| [參與者](#contributor) | 管理 access 以外的所有內容。 |
| [資料工廠參與者](#data-factory-contributor) | 可以建立及管理資料工廠和子資源，其內部。 |
| [DevTest 實驗室使用者](#devtest-labs-user) | 檢視所有項目和連線，開始、 重新開機及關閉虛擬機器 |
| [DNS 區域的參與者](#dns-zone-contributor) | 管理 DNS 區域和記錄 |
| [DocumentDB 帳戶參與者](#documentdb-account-contributor) | 可以管理 DocumentDB 帳戶 |
| [智慧系統帳戶參與者](#intelligent-systems-account-contributor) | 管理智慧系統帳戶 |
| [網路參與者](#network-contributor) | 管理所有的網路資源 |
| [新的聖器 APM 帳戶參與者](#new-relic-apm-account-contributor) | 管理新聖器應用程式效能管理帳戶和應用程式 |
| [擁有者](#owner) | 管理所有項目，包括存取 |
| [閱讀程式](#reader) | 檢視所有項目，但無法進行變更 |
| [Redis 快取參與者](#redis-cache-contributor) | 管理意指快取 |
| [排程器工作集合參與者](#scheduler-job-collections-contributor) | 可以管理工作排程器的集合 |
| [搜尋服務參與者](#search-service-contributor) | 管理搜尋服務 |
| [安全性管理員](#security-manager) | 可以管理安全性元件、 安全性原則及虛擬機器 |
| [SQL DB 參與者](#sql-db-contributor) | 管理 SQL 資料庫]，但不是其相關的安全性原則 |
| [SQL 安全性管理員](#sql-security-manager) | 可以管理相關的安全性原則的 SQL server 與資料庫 |
| [SQL Server 參與者](#sql-server-contributor) | SQL server 資料庫，但不是其相關的安全性原則管理 |
| [傳統的儲存空間帳戶參與者](#classic-storage-account-contributor) | 可以管理傳統的儲存空間帳戶 |
| [儲存帳戶參與者](#storage-account-contributor) | 管理儲存空間帳戶 |
| [使用者存取系統管理員](#user-access-administrator) | 管理使用者存取 Azure 資源 |
| [傳統的虛擬機器參與者](#classic-virtual-machine-contributor) | 傳統的虛擬機器，但不是在虛擬網路] 或 [儲存帳戶連線到管理 |
| [虛擬機器參與者](#virtual-machine-contributor) | 虛擬機器，但不是在虛擬網路] 或 [儲存帳戶連線到管理 |
| [傳統網路參與者](#classic-network-contributor) | 管理傳統的虛擬網路和保留的 IPs |
| [Web 計劃參與者](#web-plan-contributor) | 管理 web 計劃 |
| [網站參與者](#website-contributor) | 管理網站，但無法連線到 web 方案 |

## <a name="role-permissions"></a>角色的權限
下表說明特定提供給每個角色的權限。 這可以包含的**動作**，授與權限，並**NotActions**，這些限制。

### <a name="api-management-service-contributor"></a>API 管理服務參與者
可管理的 API 管理服務

| **動作** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | 建立及管理 API 管理服務 |
| Microsoft.Authorization/*/read | 閱讀授權 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 了解的角色和角色指派 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="application-insights-component-contributor"></a>應用程式的深入見解元件參與者
管理應用程式的深入見解元件

| **動作** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.Insights/components/* | 建立及管理獲得深入見解元件 |
| Microsoft.Insights/webtests/* | 建立及管理 web 測試 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="automation-operator"></a>自動化運算子
啟動、 停止、 暫停，並繼續工作

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和角色指派 |
| Microsoft.Automation/automationAccounts/jobs/read | 閱讀自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/resume/action | 繼續自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | 停止自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/streams/read | 讀取自動化帳戶工作資料流 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | 暫停自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/write | 撰寫自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobSchedules/read | 閱讀自動化帳戶作業排程 |
| Microsoft.Automation/automationAccounts/jobSchedules/write | 閱讀自動化帳戶作業排程 |
| Microsoft.Automation/automationAccounts/read | 閱讀自動化帳戶 |
| Microsoft.Automation/automationAccounts/runbooks/read | 閱讀自動化 runbooks |
| Microsoft.Automation/automationAccounts/schedules/read | 閱讀自動化帳戶排程 |
| Microsoft.Automation/automationAccounts/schedules/write | 撰寫自動化帳戶排程 |
| Microsoft.Insights/components/* | 建立及管理獲得深入見解元件 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="biztalk-contributor"></a>BizTalk 參與者
管理 BizTalk 服務

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和角色指派 |
| Microsoft.BizTalkServices/BizTalk/* | 建立及管理 BizTalk 服務 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="cleardb-mysql-db-contributor"></a>參與者 ClearDB MySQL 資料庫
管理 ClearDB MySQL 資料庫

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和角色指派 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |
| successbricks.cleardb/databases/* | 建立及管理 ClearDB MySQL 資料庫 |

### <a name="contributor"></a>參與者
管理 access 以外的所有內容

| **動作** ||
| ------- | ------ |
| * | 建立及管理所有類型的資源 |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | 無法刪除角色和角色指派 |  
| Microsoft.Authorization/*/Write | 無法建立角色和角色指派 |

### <a name="data-factory-contributor"></a>資料工廠參與者
建立及管理資料 factory 和子資源，其內部。

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.DataFactory/dataFactories/* | 建立及管理資料 factory 和子資源，其內部。 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="devtest-labs-user"></a>DevTest 實驗室使用者
檢視所有項目和連線，開始、 重新開機及關閉虛擬機器

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Compute/availabilitySets/read | 讀取可用性組的內容 |
| Microsoft.Compute/virtualMachines/*/read | 讀取虛擬機器 （VM 大小、 執行階段狀態、 VM 副檔名等） 的內容 |
| Microsoft.Compute/virtualMachines/deallocate/action | 解除虛擬機器 |
| Microsoft.Compute/virtualMachines/read | 讀取虛擬機器中的內容 |
| Microsoft.Compute/virtualMachines/restart/action | 重新啟動虛擬機器 |
| Microsoft.Compute/virtualMachines/start/action | 啟動虛擬機器 |
| Microsoft.DevTestLab/*/read | 讀取實驗室的內容 |
| Microsoft.DevTestLab/labs/createEnvironment/action | 建立一個實驗室環境 |
| Microsoft.DevTestLab/labs/formulas/delete | 刪除公式 |
| Microsoft.DevTestLab/labs/formulas/read | 閱讀公式 |
| Microsoft.DevTestLab/labs/formulas/write | 新增或修改公式 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | 評估實驗室原則 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入的 NAT 規則 |
| Microsoft.Network/networkInterfaces/*/read | 讀取網路介面 （例如，所有負載平衡器的網路介面的一部分） 的內容 |
| Microsoft.Network/networkInterfaces/join/action | 加入虛擬機器網路介面 |
| Microsoft.Network/networkInterfaces/read | 閱讀網路介面 |
| Microsoft.Network/networkInterfaces/write | 撰寫網路介面 |
| Microsoft.Network/publicIPAddresses/*/read | 讀取的公用 IP 位址的內容 |
| Microsoft.Network/publicIPAddresses/join/action | 加入的公用 IP 位址 |
| Microsoft.Network/publicIPAddresses/read | 閱讀網路的公用 IP 位址 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路 |
| Microsoft.Resources/deployments/operations/read | 讀取部署作業 |
| Microsoft.Resources/deployments/read | 閱讀部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Storage/storageAccounts/listKeys/action | 清單中儲存的帳戶金鑰 |

### <a name="dns-zone-contributor"></a>DNS 區域的參與者
管理 DNS 區域和記錄。

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/\*/讀取 | 了解的角色和角色指派 |
| Microsoft.Insights/alertRules/\* | 建立及管理提醒的規則 |
| Microsoft.Network/dnsZones/\* | 建立及管理 DNS 區域記錄 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/\* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/\* | 建立及管理支援票證 |


### <a name="documentdb-account-contributor"></a>DocumentDB 帳戶參與者
可以管理 DocumentDB 帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.DocumentDb/databaseAccounts/* | 建立及管理 DocumentDB 帳戶 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="intelligent-systems-account-contributor"></a>智慧系統帳戶參與者
管理智慧系統帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.IntelligentSystems/accounts/* | 建立及管理智慧系統帳戶 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="network-contributor"></a>網路參與者
管理所有的網路資源

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.Network/* | 建立和管理網路 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="new-relic-apm-account-contributor"></a>新的聖器 APM 帳戶參與者
管理新聖器應用程式效能管理帳戶和應用程式

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |
| NewRelic.APM/accounts/* | 建立及管理新聖器應用程式效能管理帳戶 |

### <a name="owner"></a>擁有者
管理所有項目，包括存取

| **動作** ||
| ------- | ------ |
| * | 建立及管理所有類型的資源 |

### <a name="reader"></a>閱讀程式
檢視所有項目，但無法進行變更

| **動作** ||
| ------- | ------ |
| * / 已讀取 | 閱讀的機密資料以外的所有類型的資源。 |

### <a name="redis-cache-contributor"></a>Redis 快取參與者
管理意指快取

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Cache/redis/* | 建立及管理意指快取 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="scheduler-job-collections-contributor"></a>排程器工作集合參與者
可以管理工作排程器的集合

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Scheduler/jobcollections/* | 建立及管理工作集合 |
| Microsoft.Support/* | 建立及管理支援票證  |

### <a name="search-service-contributor"></a>搜尋服務參與者
管理搜尋服務

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Search/searchServices/* | 建立及管理搜尋服務 |
| Microsoft.Support/* | 建立及管理支援票證  |

### <a name="security-manager"></a>安全性管理員
可以管理安全性元件、 安全性原則及虛擬機器

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.ClassicCompute/*/read | 讀取傳統計算虛擬機器的設定資訊 |
| Microsoft.ClassicCompute/virtualMachines/*/write | 撰寫虛擬機器的設定 |
| Microsoft.ClassicNetwork/*/read | 閱讀關於傳統網路的設定資訊  |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Security/* | 建立及管理安全性元件與原則 |
| Microsoft.Support/* | 建立及管理支援票證  |

### <a name="sql-db-contributor"></a>SQL DB 參與者
管理 SQL 資料庫，但不是其相關的安全性原則

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 了解的角色和工作分派的角色 |
| Microsoft.Insights/alertRules/* | 建立及管理提醒的規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Sql/servers/databases/* | 建立及管理 SQL 資料庫 |
| Microsoft.Sql/servers/read | 閱讀 SQL Server |
| Microsoft.Support/* | 建立及管理支援票證 |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 無法編輯稽核原則 |
| Microsoft.Sql/servers/databases/auditingSettings/* | 無法編輯的稽核設定 |
| Microsoft.Sql/servers/databases/auditRecords/read  | 無法讀取稽核記錄 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 無法編輯連線原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 無法編輯 masking 原則的資料 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | 無法編輯安全性警訊原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 無法編輯安全性指標 |

### <a name="sql-security-manager"></a>SQL 安全性管理員
可以管理相關的安全性原則的 SQL server 與資料庫

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀 Microsoft 授權 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Sql/servers/auditingPolicies/* | 建立及管理 SQL server 稽核原則 |
| Microsoft.Sql/servers/auditingSettings/* | 建立及管理 SQL server 稽核設定 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 建立及管理 SQL server 資料庫稽核原則 |
| Microsoft.Sql/servers/databases/auditingSettings/* | 建立及管理 SQL server 資料庫的稽核設定 |
| Microsoft.Sql/servers/databases/auditRecords/read | 閱讀稽核記錄 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 建立及管理 SQL server 資料庫連線原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 建立及管理 masking 原則的 SQL server 資料庫資料 |
| Microsoft.Sql/servers/databases/read | 閱讀 SQL 資料庫 |
| Microsoft.Sql/servers/databases/schemas/read | 閱讀 SQL server 資料庫結構描述 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | 閱讀 SQL server 資料庫資料表資料行 |
| Microsoft.Sql/servers/databases/schemas/tables/read | 閱讀 SQL server 資料庫表格 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | 建立及管理 SQL server 資料庫安全性警訊原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 建立及管理 SQL server 資料庫安全性指標 |
| Microsoft.Sql/servers/read | 閱讀 SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | 建立及管理 SQL server 安全性警訊原則 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="sql-server-contributor"></a>SQL Server 參與者
SQL server 資料庫但不是其相關的安全性原則管理

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀授權|
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Sql/servers/* | 建立及管理 SQL server |
| Microsoft.Support/* | 建立及管理支援票證 |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | 無法編輯 SQL server 稽核原則 |
| Microsoft.Sql/servers/auditingSettings/* | 無法編輯 SQL server 稽核設定 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 無法編輯 SQL server 資料庫稽核原則 |
| Microsoft.Sql/servers/databases/auditingSettings/* | 無法編輯 SQL server 資料庫的稽核設定 |
| Microsoft.Sql/servers/databases/auditRecords/read  | 無法讀取稽核記錄 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 無法編輯 SQL server 資料庫連線原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 無法編輯 SQL server 資料庫資料 masking 原則 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | 無法編輯 SQL server 資料庫安全性警訊原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 無法編輯 SQL server 資料庫安全性指標 |
| Microsoft.Sql/servers/securityAlertPolicies/* | 無法編輯 SQL server 安全性警訊原則 |

### <a name="classic-storage-account-contributor"></a>傳統的儲存空間帳戶參與者
可以管理傳統的儲存空間帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀授權 |
| Microsoft.ClassicStorage/storageAccounts/* | 建立及管理儲存空間帳戶 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="storage-account-contributor"></a>儲存帳戶參與者
可以管理儲存空間帳戶，但不是會存取它們。

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 讀取所有的授權 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.Insights/diagnosticSettings/* | 管理診斷設定 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Storage/storageAccounts/* | 建立及管理儲存空間帳戶 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="user-access-administrator"></a>使用者存取系統管理員
管理使用者存取 Azure 資源

| **動作** ||
| ------- | ------ |
| * / 已讀取 | 閱讀的機密資料以外的所有類型的資源。 |
| Microsoft.Authorization/* | 管理授權 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="classic-virtual-machine-contributor"></a>傳統的虛擬機器參與者
傳統的虛擬機器但不是在虛擬網路] 或 [儲存帳戶連線到管理

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀授權 |
| Microsoft.ClassicCompute/domainNames/* | 建立及管理傳統計算網域名稱 |
| Microsoft.ClassicCompute/virtualMachines/* | 建立及管理虛擬機器 |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | 加入網路安全性群組 |
| Microsoft.ClassicNetwork/reservedIps/link/action | 連結保留的 IPs |
| Microsoft.ClassicNetwork/reservedIps/read | 閱讀保留 IP 位址 |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虛擬網路 |
| Microsoft.ClassicNetwork/virtualNetworks/read | 閱讀虛擬網路 |
| Microsoft.ClassicStorage/storageAccounts/disks/read | 讀取儲存帳戶磁碟 |
| Microsoft.ClassicStorage/storageAccounts/images/read | 讀取儲存帳戶圖像 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | 清單中儲存的帳戶金鑰 |
| Microsoft.ClassicStorage/storageAccounts/read | 閱讀傳統的儲存空間帳戶 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="virtual-machine-contributor"></a>虛擬機器參與者
虛擬機器但不是在虛擬網路] 或 [儲存帳戶連線到管理

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀授權 |
| Microsoft.Compute/availabilitySets/* | 建立及管理計算可用性組 |
| Microsoft.Compute/locations/* | 建立及管理計算位置 |
| Microsoft.Compute/virtualMachines/* | 建立及管理虛擬機器 |
| Microsoft.Compute/virtualMachineScaleSets/* | 建立及管理虛擬機器縮放設定 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入網路應用程式閘道器後端地址集區 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入負載平衡器連入 NAT 集區 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器連入 NAT 規則 |
| Microsoft.Network/loadBalancers/read | 閱讀負載平衡器 |
| Microsoft.Network/locations/* | 建立和管理網路位置 |
| Microsoft.Network/networkInterfaces/* | 建立和管理網路介面 |
| Microsoft.Network/networkSecurityGroups/join/action | 加入網路安全性群組 |
| Microsoft.Network/networkSecurityGroups/read | 閱讀網路安全性群組 |
| Microsoft.Network/publicIPAddresses/join/action | 加入網路的公用 IP 位址 |
| Microsoft.Network/publicIPAddresses/read | 閱讀網路的公用 IP 位址 |
| Microsoft.Network/virtualNetworks/read | 閱讀虛擬網路 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路的子網路 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Storage/storageAccounts/listKeys/action | 清單中儲存的帳戶金鑰 |
| Microsoft.Storage/storageAccounts/read | 讀取儲存帳戶 |
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="classic-network-contributor"></a>傳統網路參與者
管理傳統的虛擬網路和保留的 IPs

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀授權 |
| Microsoft.ClassicNetwork/* | 建立及管理傳統的網路 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Support/* | 建立及管理支援票證 |

### <a name="web-plan-contributor"></a>Web 計劃參與者
管理 web 計劃

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀授權 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Support/* | 建立及管理支援票證 |
| Microsoft.Web/serverFarms/* | 建立及管理伺服器陣列 |

### <a name="website-contributor"></a>網站參與者
管理網站，但無法連線到 web 方案

| **動作** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 閱讀授權 |
| Microsoft.Insights/alertRules/* | 建立及管理獲得深入見解通知規則 |
| Microsoft.Insights/components/* | 建立及管理獲得深入見解元件 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 閱讀資源的狀況 |
| Microsoft.Resources/deployments/* | 建立及管理資源群組部署 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 閱讀資源群組 |  
| Microsoft.Support/* | 建立及管理支援票證 |
| Microsoft.Web/certificates/* | 建立及管理網站的憑證 |
| Microsoft.Web/listSitesAssignedToHostName/read | 讀取指派到主機名稱的網站 |
| Microsoft.Web/serverFarms/join/action | 加入伺服器陣列 |
| Microsoft.Web/serverFarms/read | 閱讀伺服器陣列 |
| Microsoft.Web/sites/* | 建立及管理網站 |

## <a name="see-also"></a>另請參閱
- [角色型存取控制](role-based-access-control-configure.md)︰ 快速入門 RBAC Azure 入口網站中。
- [Azure RBAC 自訂角色](role-based-access-control-custom-roles.md)︰ 了解如何建立自訂的角色，以符合您的 access 需求。
- [建立 access 變更記錄報告](role-based-access-control-access-change-history-report.md)︰ 追蹤的變更 RBAC 中的角色指派。
- [疑難排解角色型存取控制](role-based-access-control-troubleshooting.md)︰ 取得建議的修正常見的問題。
