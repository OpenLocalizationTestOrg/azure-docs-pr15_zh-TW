<properties
    pageTitle="自訂角色 Azure RBAC |Microsoft Azure"
    description="瞭解如何在 Azure 訂閱定義 Azure Role-Based 存取控制更精確地身分識別管理自訂角色。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="kgremban"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/25/2016"
    ms.author="kgremban"/>


# <a name="custom-roles-in-azure-rbac"></a>Azure RBAC 自訂角色


建立自訂角色 Azure Role-Based 存取控制 (RBAC) 如果的內建的角色都不符合您特定的存取權的需求。 使用[PowerShell 的 Azure](role-based-access-control-manage-access-powershell.md)、 [Azure 命令列介面](role-based-access-control-manage-access-azure-cli.md)(CLI) 及[REST API](role-based-access-control-manage-access-rest.md)可以建立自訂的角色。 就像內建的角色，可以自訂的角色指派給使用者、 群組和訂閱、 資源] 群組中，與資源範圍的應用程式。 自訂角色儲存 Azure AD 租用戶中，且可以使用 subsciption Azure AD 目錄該租用戶的所有訂閱之間共用。

以下是角色的自訂監視並重新啟動虛擬機器範例︰

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>動作
[**動作**] 屬性的自訂角色指定角色授與存取權的 Azure 作業。 它是識別 Azure 資源提供者的安全作業的操作字串的集合。 包含萬用字元的操作字串 (\*) 授與存取權比對作業字串的所有作業。 例如︰

-   `*/read`授與存取讀取所有 Azure 資源提供者的所有資源類型的作業。
-   `Microsoft.Network/*/read`授與存取讀取 Microsoft.Network 資源提供者中所有的資源類型，Azure 的作業。
-   `Microsoft.Compute/virtualMachines/*`授與存取權的虛擬機器和其子所有作業的資源類型。
-   `Microsoft.Web/sites/restart/Action`授與存取重新啟動網站。

使用`Get-AzureRmProviderOperation`（在 PowerShell) 或`azure provider operations show`（在 Azure CLI) Azure 資源提供者的清單作業。 若要確認作業字串是有效的並展開萬用字元作業字串，您也可以使用這些命令。

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![PowerShell 螢幕畫面-取得 AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action |FT 作業 OperationName](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure CLI 螢幕擷取畫面-azure 提供者作業顯示 「 Microsoft.Compute/virtualMachines/\*/action 」 ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
如果以排除限制的操作，更輕鬆地定義一組您要允許的作業，請使用**NotActions**屬性。 擁有自訂的角色的存取計算來**執行**作業的**NotActions**作業。

> [AZURE.NOTE] 如果使用者指派角色的排除**NotActions**中的作業，並指派授與存取權的相同作業的第二個角色，使用者將能執行的作業。 **NotActions**不是拒絕規則 – 只要方便時需要排除特定的作業，建立一組允許的作業。

## <a name="assignablescopes"></a>AssignableScopes
**AssignableScopes**屬性的自訂角色指定自訂的角色是可用的工作分派的範圍 （「 訂閱 」、 「 資源群組] 或 「 資源 」）。 您可以將自訂的角色工作分派在只有 [訂閱] 或 [資源群組的要求，並不待過濾郵件的 [訂閱] 或 [資源群組其餘部分中的使用者體驗。

有效的指派範圍的範例包括︰

-   「 / 訂閱/c276fc76-9cd4-44c9-99a7-4fd71546436e 「，」 / 訂閱/e91d47c4-76f3-4271-a796-21b4ecfe3624 」-讓角色適用於工作分派中的兩個訂閱。
-   「 / 訂閱/c276fc76-9cd4-44c9-99a7-4fd71546436e 」-讓角色工作分派在單一訂閱。
-  「 / 訂閱/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/網路 」-也能讓該角色適用於工作分派只在網路資源群組。

> [AZURE.NOTE] 您必須使用至少一個訂閱，資源] 群組中或資源識別碼。

## <a name="custom-roles-access-control"></a>自訂角色存取控制
自訂角色**AssignableScopes**屬性也會控制誰可以檢視、 修改及刪除角色。

- 誰可以建立自訂的角色？
    擁有者 （和使用者存取系統管理員） 的訂閱，資源群組與資源可以建立自訂角色使用中的範圍。
    建立該角色的使用者必須要能夠執行`Microsoft.Authorization/roleDefinition/write`所有**AssignableScopes**角色的作業。

- 誰可以修改自訂角色？
    擁有者 （和使用者存取系統管理員） 的訂閱，資源群組與資源可以修改自訂這些範圍中的角色。 使用者需要能夠執行`Microsoft.Authorization/roleDefinition/write`所有**AssignableScopes**自訂角色的作業。

- 誰可以檢視自訂角色？
    Azure RBAC 中的所有內建角色允許的角色所使用的工作分派的檢視。 使用者可以執行`Microsoft.Authorization/roleDefinition/read`範圍作業可 RBAC 角色所使用的工作分派在該範圍。

## <a name="see-also"></a>另請參閱
- [以存取控制項的角色](role-based-access-control-configure.md)︰ 快速入門 RBAC Azure 入口網站中。
- 瞭解如何管理存取︰
    - [PowerShell](role-based-access-control-manage-access-powershell.md)
    - [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
    - [REST API](role-based-access-control-manage-access-rest.md)
- [內建的角色](role-based-access-built-in-roles.md)︰ 取得標準 RBAC 中的角色的相關詳細資料。
