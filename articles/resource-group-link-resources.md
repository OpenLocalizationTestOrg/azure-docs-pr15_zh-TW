<properties 
    pageTitle="連結中 Azure 資源管理員資源 |Microsoft Azure" 
    description="建立不同的資源群組 Azure 資源管理員] 中的相關資源之間的連結。" 
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
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>連結中 Azure 資源管理員資源

部署期間，而且可以標示為相依於其他資源的資源，但該生命週期結尾部署。 部署之後，沒有識別的關係之間的相關資源。 資源管理員提供稱為資源連結至常設之間建立關聯資源的功能。

使用資源的連結，您可以文件橫跨資源群組的關聯。 例如，是常見自己生命週期位於某個資源] 群組中，若要使用的資料庫及不同的生命週期與應用程式位於不同的資源群組。 應用程式連線至資料庫，因此您要標示應用程式和資料庫之間的連結。 

所有連結的資源必須屬於相同的訂閱。 每個資源可以連結至 50 其他資源。 若要查詢相關的資源的唯一方法是透過 REST API。 任何連結的資源已被刪除或移動，如果連結擁有者必須清理剩餘的連結。 您是**不**收到警告何時刪除連結至其他資源的資源。

## <a name="linking-in-templates"></a>在範本中的連結

若要定義在範本中的連結，您可以包含結合資源者命名空間的資源類型]，然後輸入的來源將資源與**/providers/links**。 名稱必須包含來源資源的名稱。 您提供目標資源的資源的識別碼。 下列範例會建立網站和儲存帳戶之間的連結。

    {
      "type": "Microsoft.Web/sites/providers/links",
      "apiVersion": "2015-01-01",
      "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
      "dependsOn": [ "[variables('siteName')]" ],
      "properties": {
        "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
        "notes": "This web site uses the storage account to store user information."
      }
    }


如需範本格式的完整描述，請參閱[資源連結-範本結構描述](resource-manager-template-links.md)。

## <a name="linking-with-rest-api"></a>連結以 REST API

若要定義部署資源之間的連結，請執行︰

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

{訂閱識別碼} 換成您訂閱的識別碼。 取代 {資源-群組}，{者命名空間、 {資源類型、}，{資源名稱} 識別連結中的第一個資源的值。 若要建立連結的名稱取代 {連結名稱}。 使用 2015年-01-01 api 版本。

在邀請中，包括定義第二個資源的連結物件︰

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

內容項目包含第二個資源的識別碼。

您可以在您的訂閱，與查詢的連結︰

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

如需更多範例，包括如何擷取連結的相關資訊，請參閱[連結的資源](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

## <a name="next-steps"></a>後續步驟

- 您也可以組織您的資源標籤。 若要瞭解標記的資源，請參閱[使用標籤以組織您的資源](resource-group-using-tags.md)。
- 如需如何建立範本，並定義部署資源的說明，請參閱[撰寫範本](resource-group-authoring-templates.md)。
