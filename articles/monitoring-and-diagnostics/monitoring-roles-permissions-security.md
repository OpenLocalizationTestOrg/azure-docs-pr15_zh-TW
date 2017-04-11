<properties
    pageTitle="開始使用角色與權限，Azure 監視器的安全性 |Microsoft Azure"
    description="瞭解如何使用 Azure 監視器的內建的角色和權限限制存取監視資源。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>開始使用角色與權限，Azure 監視器的安全性

許多小組需要嚴格管理存取權監控資料和設定。 例如，如果您有以獨佔模式處理監控 （支援工程師，devops 工程師） 的小組成員，或如果您使用受管理的服務提供者，您可能會想將只時限制能夠建立監視資料的存取權授予，修改或刪除資源。 本文將示範如何快速套用內建的監控 RBAC 角色 Azure 中的使用者，或建立您自己自訂的角色，需要限制監控權限的使用者。 接著討論 Azure 監視器相關資源，與您可以在如何限制存取包含資料的安全性考量。

## <a name="built-in-monitoring-roles"></a>內建的監控角色

Azure 監視器的內建的角色設計用來協助限制存取訂閱時仍啟用負責監控基礎結構，以取得並設定所需的資料中的資源。 Azure 監視提供兩種--現成的角色︰ 監控讀取和監視的參與者。

### <a name="monitoring-reader"></a>監控閱讀程式

指派監視的讀取者角色的人員可以將訂閱中檢視監控的所有資料，但無法修改任何資源或編輯監視資源相關的任何設定。 此角色適用於組織，例如支援人員] 或 [作業工程師，需要能夠中的使用者︰

- 在入口網站檢視監控儀表板，並建立自己私人監控儀表板。
- 使用[Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx)、 [PowerShell cmdlet](insights-powershell-samples.md)或[跨平台 CLI](insights-cli-samples.md)眼的查詢。
- 查詢使用入口網站、 Azure 監視器 REST API、 PowerShell cmdlet 或跨平台 CLI 活動記錄。
- 檢視資源的[診斷設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)。
- 檢視訂閱的[記錄檔的設定檔](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles)。
- 檢視自動調整大小的設定。
- 檢視提醒的活動和設定。
- 存取應用程式的深入見解資料，並檢視中 AI 分析的資料。
- 搜尋包括工作區的使用情況資料的記錄檔分析 (OMS) 工作區資料。
- 檢視記錄檔分析 (OMS) 管理群組]。
- 擷取記錄分析 (OMS) 搜尋結構描述。
- 列出記錄分析 (OMS) 智慧套件。
- 擷取及執行記錄分析 (OMS) 儲存的搜尋。
- 擷取記錄分析 (OMS) 儲存設定。

> [AZURE.NOTE] 此角色不提供記錄資料串流到事件集線器或儲存在儲存帳戶的讀取權限。 [請參閱下面的](#security-considerations-for-monitoring-data)設定存取這些資源的資訊。

### <a name="monitoring-contributor"></a>監控參與者

監控參與者角色指派的人員可以在訂閱中檢視監控的所有資料並建立或修改監視設定，但無法修改任何其他的資源。 此角色的監控讀者角色，並適用於組織的監控小組或受管理的服務提供者，除了上述的權限也必須要能夠的成員︰

- [發佈成共用儀表板監控儀表板。
- 設定[診斷設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)resource.*
- 設定[記錄的設定檔](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles)的 subscription.*
- 設定提醒的活動和設定。
- 建立應用程式的深入見解 web 測試和元件。
- 清單記錄分析 (OMS) 共用工作區的索引鍵。
- 啟用或停用日誌分析 (OMS) 智慧套件。
- 建立及刪除，以及執行記錄分析 (OMS) 儲存的搜尋。
- 建立及刪除記錄分析 (OMS) 儲存設定。

* 必須也分別授與使用者目標資源 （儲存帳戶] 或 [事件] 中心命名空間） 上設定記錄的設定檔] 或 [診斷設定的 ListKeys 權限。

> [AZURE.NOTE] 此角色不提供記錄資料串流到事件集線器或儲存在儲存帳戶的讀取權限。 [請參閱下面的](#security-considerations-for-monitoring-data)設定存取這些資源的資訊。

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>監控自訂 RBAC 角色與權限
如果上述的內建角色不符合您的小組的確切需求，您可以[建立自訂 RBAC 角色](../active-directory/role-based-access-control-custom-roles.md)具有微調權限。 以下是與他們描述的常見 Azure 監視器 RBAC 作業。

| 作業                                                   | 描述                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Microsoft.Insights/AlertRules/[Read，寫入、 刪除]         | 讀/寫刪除通知的規則。                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | 通知規則清單事件 （通知規則觸發的歷程記錄）。 這只適用於入口網站。                                              |
| Microsoft.Insights/AutoscaleSettings/[Read，寫入、 刪除]  | 讀/寫刪除自動調整大小的設定。                                                                                                                     |
| Microsoft.Insights/DiagnosticSettings/[Read，寫入、 刪除] | 讀/寫刪除診斷設定。                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | 此權限，則需要針對需要透過入口網站活動記錄檔權限的使用者。                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | 清單中訂閱的活動記錄事件 （管理事件）。 此權限適用於程式設計和入口網站的存取權活動記錄。 |
| Microsoft.Insights/LogDefinitions/Read                      | 此權限，則需要針對需要透過入口網站活動記錄檔權限的使用者。                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | 閱讀公制定義 （資源可用公制類型的清單）。                                                                                  |
| Microsoft.Insights/Metrics/Read                             | 閱讀資源的指標。                                                                                                                              |

> [AZURE.NOTE] 存取提醒、 診斷設定]，與指標資源需要使用者擁有的資源類型與該資源的範圍的讀取權限。 建立 （「 撰寫 」） 診斷設定或記錄的設定檔儲存帳戶或串流事件集線器封存需要使用者也擁有目標資源上的 [ListKeys 權限。

例如，使用上述的表格，您可以建立自訂的 RBAC 角色的 「 活動記錄讀取 」 像這樣︰

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>監視資料的安全性考量
監視資料，特別是記錄檔，最多可以包含機密資訊，例如 IP 位址或使用者名稱。 監視 azure 資料有三個基本的表單︰
1. 說明中的所有控制項平面動作 Azure 訂閱活動記錄。
2. 診斷記錄]，也就是資源所發出的記錄。
3. 指標，資源所發出。

三個這些資料類型可以儲存在儲存帳戶，或傳送至 [事件] 中心內，兩者都是用途 Azure 資源。 由於已經用途的資源，建立、 刪除和存取這些是有權限的操作，通常是系統管理員保留。 我們建議您使用下列作法監控相關資源，以防止不正確地使用︰

- 監視資料使用單一的專用的儲存空間的帳戶。 如果您需要監視資料分成多個儲存帳戶，永遠不會共用之間監控儲存帳戶的使用狀況及非監控的資料，因為這可能不小心提供者只需要存取監視資料 （例如。 第三方 SIEM) 存取非監控資料。
- 使用單一的專用服務匯流排或事件集線器命名空間過所有診斷設定，上述相同的原因。
- 限制存取監控相關的儲存空間帳戶或事件集線器將其保留在個別的資源] 群組中，並[使用範圍](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure)監控的角色，限制只有該資源群組存取。
- 永遠不會當使用者只需要監視資料的存取權授與儲存帳戶或事件集線器訂閱範圍的 ListKeys 權限。 相反地，授與這些權限的使用者在資源或資源群組 （如果您有專用的監控資源群組） 範圍。

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>限制存取監控相關的儲存空間帳戶
當使用者或應用程式需要存取監視儲存帳戶中的資料時，您應該包含與服務等級唯讀存取 blob 儲存體監控資料的儲存空間帳戶的 [[產生帳戶 SA](https://msdn.microsoft.com/library/azure/mt584140.aspx) 。 在 PowerShell，這可能會像這樣︰

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

您可以在需要讀取該儲存帳戶]，然後將其可以清單，以及從所有 blob 儲存體帳戶中讀取然後給權杖的實體。

或者，如果您需要控制 RBAC 與此權限，您可以授與的實體特定儲存帳戶的 Microsoft.Storage/storageAccounts/listkeys/action 權限。 這是必要的使用者必須能夠登儲存帳戶的設定檔以封存或設定診斷設定。 例如，您可以建立下列自訂 RBAC 的角色的使用者或只需要讀取儲存帳戶的應用程式︰

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] ListKeys 權限可讓使用者清單的主要和次要儲存帳戶金鑰。 這些按鍵授與使用者所有已簽署的權限 （讀取、 撰寫、 建立二進位大型物件及刪除 blob 等） 所有登入儲存帳戶中的服務 （blob、 佇列、 表格、 檔案）。 我們建議使用上述盡可能帳戶 SA。

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>限制存取到監控相關的事件
類似模式可以追蹤與事件集線器，但您必須先建立專屬的接聽授權規則。 如果您想要授與存取權的應用程式，只需要聆聽監控相關事件集線器，請執行下列動作︰

1. 建立資料流監控資料僅接聽宣告事件 hub(s) 上建立共用的 access 原則。 如此可以在入口網站。 例如，您可能會呼叫該 「 monitoringReadOnly 」。 如果可能的話，您會要授予該金鑰直接消費者略過的下一個步驟。
2. 如果消費者需要能夠取得金鑰臨機操作，授與使用者的事件中樞的 ListKeys 動作。 這也是必要的使用者需要就可以將 [診斷設定或串流到事件記錄的設定檔。 例如，您可能會建立 RBAC 規則︰

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>後續步驟
- [進一步瞭解 RBAC，並在 [資源管理員的權限](../active-directory/role-based-access-control-what-is.md)
- [閱讀的監控 Azure 概觀](monitoring-overview.md)
