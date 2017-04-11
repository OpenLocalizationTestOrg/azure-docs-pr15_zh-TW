<properties 
    pageTitle="鎖定資源與資源管理員 |Microsoft Azure" 
    description="防止使用者更新或刪除某些資源套用至所有使用者與角色的限制。" 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>鎖定資源與 Azure 資源管理員

身為管理員，您可能需要鎖定以防止其他使用者不小心刪除或修改重要的資源，您組織中的訂閱，資源] 群組中或資源。 您可以設定鎖定層級**CanNotDelete**或**唯讀**。 

- **CanNotDelete**表示授權的使用者仍然可以讀取及修改資源，但無法將其刪除。 
- **唯讀**表示授權的使用者可讀取資源，但是他們無法將它刪除，或執行任何動作。 資源的權限是**讀者**限制。 

因為看起來像某些操作讀取作業實際需要的其他動作套用**唯讀**可能導致非預期的結果。 例如，放在儲存帳戶的**唯讀**鎖定防止所有使用者列出按鍵。 清單鍵作業透過文章要求因為傳回的鍵可供撰寫作業。 另一個範例中，在應用程式服務資源放置**唯讀**鎖定防止 Visual Studio 伺服器總管顯示資源的檔案，因為互動需要寫入存取權。

與不同的角色型存取控制，您可以使用 [管理鎖定來將限制套用到所有的使用者與角色。 若要瞭解如何設定的使用者與角色的權限，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

當您套用在上層範圍鎖定時，所有的子資源繼承相同鎖定。 即使您稍後新增的資源從父項繼承鎖定。 繼承中的最為嚴格鎖定的優先順序。

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>誰可以建立或刪除您組織中的 [鎖定嗎

若要建立或刪除管理鎖定，您必須存取**Microsoft.Authorization/\***或**Microsoft.Authorization/locks/\***動作。 內建的角色，只有**擁有者**和**使用者存取系統管理員**會授與這些動作。

## <a name="creating-a-lock-through-the-portal"></a>建立鎖定透過入口網站

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>建立範本中的 [鎖定

下列範例會顯示在儲存帳戶建立鎖定的範本。 要套用鎖定的儲存空間帳戶提供做為參數。 鎖定的名稱會建立串連**/Microsoft.Authorization/**的資源名稱] 和 [鎖定，在此案例**myLock**中的名稱。

提供的類型是特定的資源類型。 儲存空間，類型設為 「 Microsoft.Storage/storageaccounts/providers/locks 」。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>建立 REST API 鎖定

您可以鎖定和[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)部署的資源。 REST API 可讓您建立及刪除鎖定，並擷取現有鎖定的相關資訊。

若要建立鎖定，請執行︰

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

範圍可能是訂閱、 資源] 群組中或資源。 鎖定名稱是您要撥打鎖定上述程序。 如需 api 版本，請使用**2015年-01-01**。

在邀請中，包括 JSON 指定的物件，鎖定的屬性。

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

如需範例，請參閱[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)。

## <a name="creating-a-lock-with-azure-powershell"></a>使用 PowerShell 的 Azure 建立鎖定

您可以鎖定使用 PowerShell 的 Azure 部署的資源使用**新增 AzureRmResourceLock** ，如下列範例所示。

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell 提供使用鎖定，例如**設定 AzureRmResourceLock**更新鎖定和**移除 AzureRmResourceLock**刪除鎖定其他命令。

## <a name="next-steps"></a>後續步驟

- 如需有關如何使用資源鎖定的詳細資訊，請參閱[鎖定向下您 Azure 資源](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- 若要瞭解如何以邏輯方式組織您的資源，請參閱[使用標籤以組織您的資源](resource-group-using-tags.md)
- 若要變更資源位於哪個資源群組，請參閱[移動到新的資源群組的資源](resource-group-move-resources.md)
- 您可以透過您的訂閱，以自訂的原則套用限制和慣例。 如需詳細資訊，請參閱[使用原則管理資源，並控制存取權](resource-manager-policy.md)。
