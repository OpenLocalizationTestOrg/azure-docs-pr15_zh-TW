<properties
    pageTitle="Azure 資源管理員原則 |Microsoft Azure"
    description="說明如何使用 Azure 資源管理員原則防止衝突在不同的範圍，例如訂閱、 資源群組或個別的資源。"
    services="azure-resource-manager"
    documentationCenter="na"
    authors="ravbhatnagar"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="gauravbh;tomfitz"/>

# <a name="use-policy-to-manage-resources-and-control-access"></a>使用原則管理資源，並控制存取權

Azure 資源管理員現在可讓您控制透過自訂原則的存取權。 使用原則，您可以防止使用者從中斷管理貴組織的資源所需的慣例您組織中。 

您建立原則定義描述動作或特別被拒絕的資源。 您指定所要的範圍，例如訂閱、 資源群組或個別的資源，這些原則定義。 

本文中，我們會說明您可以使用來建立原則原則定義語言的基本結構。 然後，我們會說明如何將主版頁面套用不同的範圍，這些原則。

## <a name="how-is-it-different-from-rbac"></a>如何很 RBAC 不同？

原則和角色型存取控制之間的一些主要差異，但若要瞭解的第一個項目是原則和 RBAC 共同合作。 若要使用原則，必須經過您 RBAC。 不同於 RBAC，原則的是預設允許與明確拒絕系統。 

RBAC 著重於**使用者**可以在不同的範圍中執行的動作。 例如，特定使用者會新增至所要的範圍，資源群組的參與者角色，讓使用者可以進行變更，與該資源群組。 

原則著重在不同範圍的**資源**動作。 例如，透過原則，您可以控制可以佈建後，或限制的資源可以佈建位置的資源的類型。

## <a name="common-scenarios"></a>常見的案例

需要 chargeback 用途部門標籤為一個常見的情況。 組織可能會想要只有相關; 適當的成本中心時，只允許作業否則，他們拒絕要求。 此原則可協助他們收費適當的成本中心執行的作業。

其他常見的案例是組織可能會想要控制會建立資源的位置。 或他們可能會想要允許特定類型的資源，佈建來控制存取資源。

同樣地，組織可以控制服務目錄或強制執行的資源所要的命名慣例。

使用原則，這些案例，可以輕鬆地達成。

## <a name="policy-definition-structure"></a>原則定義的結構

使用 JSON 建立原則定義。 其中包含一或多個條件/邏輯運算子定義的動作，並符合條件情況效果告知的內容。 結構描述刊登在[http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json)。 

基本上，原則會包含下列元素︰

**條件/邏輯運算子︰**一組可透過一組邏輯運算子的操作的條件。

**效果︰**滿足條件 – 時，會發生什麼情況 [拒絕] 或 [稽核。 稽核效果會發出警告事件服務記錄檔。 例如，系統管理員可以建立原則會使稽核事件，如果任何人都建立大型 VM。 系統管理員可以稍後再檢閱的記錄。

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }
    
## <a name="policy-evaluation"></a>原則評估

建立資源時，會評估原則。 若範本部署原則會評估期間建立範本中的每個資源。 

> [AZURE.NOTE] 目前，原則不會評估標籤類型，與位置，例如 Microsoft.Resources/deployments 資源類型不支援的資源類型。 支援將在未來的時間。 若要避免回溯相容性問題，必須明確指定類型時撰寫原則。 例如，不指定類型標籤原則套用所有類型。 在此情況下，不支援標籤巢狀的資源時，可能會失敗範本部署及原則評估已加入部署資源類型。 

## <a name="logical-operators"></a>邏輯運算子

支援的邏輯運算子，以及語法是︰

| 運算子名稱     | 語法         |
| :------------- | :------------- |
| 不            | "not": {&lt;條件或運算子&gt;}             |
| 與           | 「 載 」: [{&lt;條件或運算子&gt;}，{&lt;條件或運算子&gt;}] |
| 或                         | 「 anyOf 」: [{&lt;條件或運算子&gt;}，{&lt;條件或運算子&gt;}] |

資源管理員可讓您透過巢狀運算子原則中指定複雜的邏輯。 例如，您可以拒絕中指定的資源類型的特定位置的資源建立。 巢狀運算子的範例如下所示。

## <a name="conditions"></a>條件

條件評估是否**欄位**或**來源**符合特定準則。 支援的條件名稱和語法是︰

| 條件名稱 | 語法                |
| :------------- | :------------- |
| 等於             | 「 等於 」: 「&lt;值&gt;」               |
| 例如                  | 「 喜歡 」: 「&lt;值&gt;」                   |
| 包含          | 「 包含 」: 「&lt;值&gt;」|
| 在                        | 「 中 」: [」&lt;value1&gt;」、 「&lt;value2&gt;」]|
| ContainsKey    | 「 containsKey 」: 「&lt;keyName&gt;」 |
| 存在     | 「 存在 」: 「&lt;bool&gt;」 |

### <a name="fields"></a>欄位

條件是使用欄位與來源格式。 功能變數代表資源要求裝載的用來描述資源的狀態中的內容。 來源代表要求本身的特性。 

支援下列功能變數和來源︰

欄位︰**名稱**、**類型**、**類型**、**位置**、**標籤****標記。***, and **屬性別名**。 

### <a name="property-aliases"></a>屬性別名 
屬性別名是可以原則定義中用來存取資源類型特定屬性，例如設定和 sku 的名稱。 其運作方式會在這些屬性存在的 API 版本。 可以使用示 (將在未來加入支援 Powershell) REST API 來擷取別名︰

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
    
Alias （別名） 的定義如下所示。 如您所見，別名定義路徑中不同的 API 版本，甚至屬性名稱變更時，才能。 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

目前，是受支援的別名︰

| Alias （別名） 名稱 | 描述 |
| ---------- | ----------- |
| {resourceType}/sku.name | 支援的資源類型︰ Microsoft.Compute/virtualMachines，<br />Microsoft.Storage/storageAccounts，<br />Microsoft.Web/serverFarms，<br /> Microsoft.Scheduler/jobcollections，<br />Microsoft.DocumentDB/databaseAccounts，<br />Microsoft.Cache/Redis，<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family | 支援的資源類型是 Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | 支援的資源類型是 Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher |  |
| Microsoft.Compute/virtualMachines/imageOffer  |  |
| Microsoft.Compute/virtualMachines/imageSku  |  |
| Microsoft.Compute/virtualMachines/imageVersion  |  |
| Microsoft.Cache/Redis/enableNonSslPort |  |
| Microsoft.Cache/Redis/shardCount |  |
| Microsoft.SQL/servers/version |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName |  |
| Microsoft.SQL/servers/databases/edition |  |
| Microsoft.SQL/servers/databases/elasticPoolName |  |
| Microsoft.SQL/servers/elasticPools/dtu |  |
| Microsoft.SQL/servers/elasticPools/edition |  |

目前，原則只能在放入要求。 

## <a name="effect"></a>效果
原則支援三種類型的效果-**拒絕**、**稽核**與**附加**。 

- 拒絕產生稽核記錄中的事件，且無法要求
- 稽核稽核記錄檔中產生的事件，但不會失敗要求
- 附加邀請加入的已定義之的欄位集 

**附加**，您必須提供的下列詳細資料︰

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

字串或物件 JSON 格式，可以是值。 

## <a name="policy-definition-examples"></a>原則定義範例

現在讓我們來看看我們如何定義達到上述情況原則。

### <a name="chargeback-require-departmental-tags"></a>Chargeback︰ 需要部門的標籤

下列原則拒絕要求，沒有標籤包含 「 costCenter 」 鍵。

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

無標記出現時，下列原則會附加 costCenter 標籤預先定義的值。 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }
    
下列原則 costCenter 標籤不存在，但是其他標記簡報時，會將 costCenter 標籤預先定義的值。 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]
    
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>地理規範︰ 確保資源位置

下列範例會顯示其中位置不是北美歐洲或西歐原則，以拒絕要求。

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>服務 Curation︰ 選取服務目錄

下列範例顯示的原則，只在服務類型 Microsoft.Resources/ 的幾個動作\*，Microsoft.Compute/\*，Microsoft.Storage/\*，Microsoft.Network/\*允許。 拒絕其他項目。

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>使用已核准的 Sku

下列範例顯示屬性 alias （別名) 來限制 sku 皆可的使用。 在範例中，只有 Standard_LRS 和 Standard_GRS 被核准的儲存空間帳戶。

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
    

### <a name="naming-convention"></a>命名慣例

下列範例會顯示使用萬用字元，支援對說 「 讚 」 的條件。 如果名稱符合所述的圖樣條件狀態的 (namePrefix\*nameSuffix) 然後拒絕要求。

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### <a name="tag-requirement-just-for-storage-resources"></a>標記需求只儲存資源

下列範例會示範如何巢狀嵌入為只儲存資源需要的應用程式標記的邏輯運算子。

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="policy-assignment"></a>原則設定

原則會套用至不同的範圍，例如訂閱、 資源群組]，與個別的資源。 所有的子資源的繼承原則。 因此，如果資源群組原則，這是適用於該資源] 群組中的所有資源。

## <a name="creating-a-policy"></a>建立原則

本節提供原則可如何建立使用 REST API 詳細資料。

### <a name="create-policy-definition-with-rest-api"></a>以 REST API 建立原則定義

您可以建立[的原則定義 REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx)原則。 REST API 可讓您建立及刪除原則定義，並取得現有的定義的相關資訊。

若要建立原則，請執行︰

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

具有邀請本文類似下面的範例︰

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


如需 api 版本使用*2016年-04-01*。 範例及更多詳細資料，請參閱[REST API 原則定義](https://msdn.microsoft.com/library/azure/mt588471.aspx)。

### <a name="create-policy-definition-using-powershell"></a>建立使用 PowerShell 原則定義

您可以建立的原則定義，使用 [新增 AzureRmPolicyDefinition 指令程式。 下列範例會建立的原則，允許在北美歐洲及西歐中的資源。

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{  
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'          

執行的輸出 $policy 物件中已儲存，並可供未來原則工作分派。 原則參數，包含原則.json 檔案路徑也可提供，而不是指定原則內嵌。

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain    regions" -Policy "path-to-policy-json-on-disk"

### <a name="create-policy-definition-using-azure-cli"></a>建立使用 Azure CLI 原則定義

您可以建立使用原則定義] 命令，如下所示的 azure CLI 的原則定義。 下列範例會建立的原則，允許在北美歐洲及西歐中的資源。

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{   
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    
    

若要指定包含的原則，而不是指定原則內嵌的如下所示的.json 檔案路徑可能是。

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"


## <a name="applying-a-policy"></a>套用原則

### <a name="policy-assignment-with-rest-api"></a>以 REST API 原則工作分派

您可以套用原則定義在透過[原則工作分派的 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx)所要的範圍。 REST API 可讓您建立及刪除原則指派]，並取得現有的工作分派的相關資訊。

若要建立原則指派，請執行︰

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{原則-工作分派} 是原則工作分派的名稱。 如需 api 版本使用*2016年-04-01*。 

具有邀請本文類似下面的範例︰

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

範例及更多詳細資料，請參閱[原則工作分派的 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx)。

### <a name="policy-assignment-using-powershell"></a>使用 PowerShell 原則工作分派

您可以套用所建立的原則上方透過 PowerShell 所要的範圍以使用新 AzureRmPolicyAssignment 指令程式︰

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
以下 $policy 是傳回當做執行新增 AzureRmPolicyDefinition cmdlet 如上所示的原則物件。 此處的範圍是您所指定的 [資源] 群組的名稱。

如果您想要移除上述原則指派，您可以如下執行︰

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

您可以取得、 變更或移除原則定義取得 AzureRmPolicyDefinition、 設定 AzureRmPolicyDefinition，並移除 AzureRmPolicyDefinition cmdlet 分別。

同樣地，您可以取得、 變更或移除原則指派取得 AzureRmPolicyAssignment、 設定 AzureRmPolicyAssignment，並移除 AzureRmPolicyAssignment cmdlet 分別。

### <a name="policy-assignment-using-azure-cli"></a>使用 Azure CLI 原則工作分派

您可以套用所建立的原則上方透過 Azure CLI 所要的範圍內使用原則工作分派] 命令︰

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
此處的範圍是您所指定的 [資源] 群組的名稱。 如果無法辨識的參數原則定義-識別碼值，就可以取得透過 Azure CLI，如下所示︰ 

    azure policy definition show <policy-name>

如果您想要移除上述原則指派，您可以如下執行︰

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

您可以取得、 變更或移除的原則定義透過原則定義顯示，請設定，以及分別刪除命令。

同樣地，您可以取得、 變更或移除的原則透過原則工作分派的工作分派顯示和分別刪除命令。

##<a name="policy-audit-events"></a>原則稽核的事件

您已套用原則之後，您將開始請參閱原則相關的事件。 您可以有執行入口網站，以取得此資料使用 PowerShell 或 Azure CLI。 

### <a name="policy-audit-events-using-powershell"></a>使用 PowerShell 原則稽核的事件

若要檢視所有事件的相關拒絕效果，您可以使用下列 PowerShell 命令︰

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

若要檢視稽核效果的相關的所有事件，您可以使用下列命令︰

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="policy-audit-events-using-azure-cli"></a>使用 Azure CLI 原則稽核的事件

若要檢視所有資源的事件群組的相關拒絕效果，您可以使用下列 CLI 命令︰

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

若要檢視稽核效果的相關的所有事件，您可以使用下列 CLI 命令︰

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"
