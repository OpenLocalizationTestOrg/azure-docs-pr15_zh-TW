
<properties
    pageTitle="使用 Azure CLI 管理資源 |Microsoft Azure"
    description="使用 Azure 命令列介面 (CLI) 管理 Azure 資源及群組"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>使用 Azure CLI 管理 Azure 資源與資源群組


> [AZURE.SELECTOR]
- [入口網站](azure-portal/resource-group-portal.md) 
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST API](resource-manager-rest-api.md)


Azure 命令列介面 (Azure CLI) 是一種部署及管理資源與資源管理員，您可以使用數種工具。 本文會介紹在資源管理員模式中使用 Azure CLI 管理 Azure 資源與資源群組的常見方法。 瞭解如何使用 CLI 部署資源的資訊，請參閱[使用資源管理員範本和 Azure CLI 部署資源](resource-group-template-deploy-cli.md)。 如需 Azure 資源及資源管理員的背景，請造訪[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)。

>[AZURE.NOTE] 若要管理與 Azure CLI Azure 資源，您需要[安裝 Azure CLI](xplat-cli-install.md)及[登入 Azure](xplat-cli-connect.md)使用`azure login`] 命令。 請確定 CLI 資源管理員模式 (執行`azure config mode arm`)。 如果您已完成這些項目，請您準備好。



## <a name="get-resource-groups-and-resources"></a>取得資源群組與資源

### <a name="resource-groups"></a>資源群組

若要取得您的訂閱，其位置中的所有資源群組的清單，請執行此命令。

    azure group list
    

### <a name="resources"></a>資源
 若要列出所有資源在群組的名稱*testRG*，例如使用下列命令。

    azure resource list testRG

若要檢視] 群組中，在個別的資源，例如 VM 名為*MyUbuntuVM*，請使用下列命令。

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
請注意**Microsoft.Compute/virtualMachines**參數。 這個參數會指出您要求的資訊上的資源類型。
    
>[AZURE.NOTE]在使用 [**清單**] 命令以外的**azure 資源**命令時，您必須使用**command 和 o**參數來指定資源的 API 版本。 如果您不確定 API 版本，請參閱將範本檔案，並尋找資源 apiVersion 欄位。 如需關於的 API 版本資源管理員] 中，請參閱[資源管理員提供者、 區域、 API 版本和結構描述](resource-manager-supported-services.md)。

當您在資源檢視詳細資料，通常很有用使用`--json`參數。 這個參數使輸出更容易閱讀，因為某些值的巢狀的結構或集合。 下列範例會示範傳回的結果為 JSON 文件的 [**顯示**] 命令。

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] 如果您想，將儲存 JSON 資料檔案使用&gt;，將檔案輸出的字元。 例如︰
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>標記

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>管理資源


若要新增到資源群組的資源，例如儲存的帳戶，執行與類似的命令︰

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
除了指定的資源 API 版本和**command 和 o**參數，使用**-p**參數傳遞 JSON 格式字串包含任何必要或其他屬性。
    
    
若要刪除現有的資源，例如虛擬機器資源，請使用下列命令。

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

若要將現有的資源移到另一個資源群組或訂閱，使用**移動 azure 資源**命令。 下列範例會示範如何將 Redis 的快取移到新的資源群組。 **-I**參數中提供的資源識別碼逗點分隔清單的移動。


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>控制存取權的資源

您可以使用 Azure CLI 來建立和管理原則來控制 Azure 資源的存取權。 關於原則定義和資源指派原則的背景，請參閱[使用原則管理資源，並控制存取權](resource-manager-policy.md)。

例如，定義拒絕所有的要求，其中的位置不是西美國或北美美國中部，下列原則，然後儲存至原則定義檔案 policy.json:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

執行**原則定義建立**命令︰

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
這個命令顯示輸出類似下列。

    + 建立原則定義 MyPolicy 資料︰ 包含︰ MyPolicy 資料︰ PolicyDefinitionId: /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    資料︰ PolicyType︰ 自訂資料︰ 顯示名稱︰ 未定義資料︰ 描述︰ 未定義資料︰ PolicyRule︰ 欄位 = 位置，在 = [westus northcentralus]，效果 = 拒絕

 若要指定在您想要的範圍內的原則，使用**PolicyDefinitionId**所傳回的前一個命令。 在下列範例中，此範圍是訂閱，但您可以範圍資源群組或個別的資源︰

    azure 原則指派建立 MyPolicyAssignment-p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy-s /subscriptions/###-###-###-###-### /

您可以取得、 變更或移除原則定義使用**原則定義顯示**及**原則定義設定**，並**刪除原則定義**] 命令。

同樣地，您可以取得、 變更或移除原則作業使用**原則指派顯示**、**設定原則工作分派**和**原則指派刪除**] 命令。


## <a name="export-a-resource-group-as-a-template"></a>匯出另存為範本的 [資源群組

針對現有的資源群組，您可以檢視 [資源] 群組的資源管理員範本。 匯出範本提供兩個優點︰

1. 您可以輕鬆地自動化未來部署解決方案的因為所有的基礎結構定義在範本中。

2. 您可以熟悉範本語法，代表您的方案 JSON。

使用 Azure CLI，您可以匯出範本，表示目前狀態的資源的群組，或下載特定的部署所用的範本。

* **匯出資源群組的範本**-當您變更 [資源] 群組中，而且需要以擷取其目前狀態的 JSON 表示，這是很有幫助。 不過，產生的範本包含最小數字的參數和沒有變數。 大部分的範本中的值會硬式編碼。 部署之前產生的範本，您可能會想要將多個值轉換成參數，因此您可以自訂不同環境中部署。

    若要匯出至本機目錄的資源群組的範本，請執行`azure group export`命令，如下列範例所示。 （取代適合您作業系統環境的本機目錄）。

        azure group export testRG ~/azure/templates/

* **下載的範本在特定的部署**，當您需要檢視時所用部署資源的實際範本時，這是很有幫助。 範本包含所有參數與原始的部署定義的變數。 不過，如果您組織中的人員以外的範本中定義的 [資源] 群組中進行變更，此範本不代表資源群組的目前狀態。

    若要下載特定部署至本機目錄使用的範本，請執行`azure group deployment template download`] 命令。 例如︰

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] 範本匯出在 [預覽]，而不是所有的資源類型目前支援匯出範本。 嘗試匯出為範本，您可能會看到錯誤的一些資源未匯出。 如有需要以手動方式在範本中定義這些資源後下載。



## <a name="next-steps"></a>後續步驟

* 若要取得部署作業的詳細資料和 Azure CLI 部署錯誤的疑難排解，請參閱[Azure CLI 檢視部署作業](resource-manager-troubleshoot-deployments-cli.md)。
* 如果您想要使用 CLI 設定應用程式或指令碼存取資源，請參閱[使用 Azure CLI 來建立主要存取資源的服務](resource-group-authenticate-service-principal-cli.md)。


