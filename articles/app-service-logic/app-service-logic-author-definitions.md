<properties 
    pageTitle="作者邏輯應用程式定義 |Microsoft Azure" 
    description="瞭解如何撰寫邏輯應用程式的 JSON 定義" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="author-logic-app-definitions"></a>作者邏輯應用程式定義
本主題示範如何使用[Azure 邏輯應用程式](app-service-logic-what-are-logic-apps.md)定義是簡單的宣告式 JSON 語言。 如果您尚未這麼做尚未，請先查看[如何建立新的邏輯應用程式](app-service-logic-create-a-logic-app.md)。 您也可以瞭解[完整的參考資料定義語言 MSDN 上](http://aka.ms/logicappsdocs)。

## <a name="several-steps-that-repeat-over-a-list"></a>重複清單上的幾個步驟

您可以利用[foreach 類型](app-service-logic-loops-and-scopes.md)在最多 10 k 陣列重複項目，並針對各個執行的動作。

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>如果發生錯誤的錯誤處理步驟

您經常要能夠寫入*補救步驟*，如果**，並且在**、 一或多個來電無法執行某些邏輯。 在此範例中，我們會從各種不同的位置]，取得資料但呼叫失敗，如果我想要讓我可以稍後追蹤該失敗位置張貼訊息︰  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

您可以進行的使用`runAfter`屬性，以指定`postToErrorMessageQueue`只應該執行`readData`會**失敗**。  也可能是清單的可能值，因此`runAfter`可能是`["Succeeded", "Failed"]`。

最後，您現在已經處理錯誤，因為我們不會再標示執行**失敗**。 您可以看到 [以下，執行此是**成功**即使其中一個步驟失敗，因為撰寫處理此失敗的步驟。

## <a name="two-or-more-steps-that-execute-in-parallel"></a>兩個 （或多個） 同時執行的步驟

平行，有多個動作執行`runAfter`屬性必須在執行階段相等。 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

您可以看到在上述兩範例`branch1`和`branch2`設定為執行`readData`。 如此一來，這兩個這些分支會以平行執行︰

![平行](./media/app-service-logic-author-definitions/parallel.png)

您可以看到兩個分支的時間戳記相同。 

## <a name="join-two-parallel-branches"></a>加入兩個平行分支

您可以加入設定為 [新增項目，以同時執行兩個動作`runAfter`類似於上方的屬性。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![平行](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>將清單中的項目對應至一些不同的設定

下一步，假設我們想要以取得完全不同的內容，根據屬性的值。 我們可以建立地圖的目的地的值做為參數︰  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

在此案例中，我們先取得文件的清單，然後的第二步查閱在地圖上，以定義做為參數的 URL，以取得的內容分類。 

請注意以下兩個項目︰[`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection)函數用來檢查類別符合其中一個已知定義的類別。 第二，一旦得到類別，我們可以擷取地圖使用方括號內的項目︰ `parameters[...]`。 

## <a name="working-with-strings"></a>使用 [字串

有不同的可用來管理字串的功能。 讓我們來看，我們有我們要將系統的字串，但我們不確定的字元編碼會處理正確的範例。 其中一個選項是 base64 編碼這個字串。 不過，若要避免在 URL 中逸出我們將會取代幾個字元。 

我們也想要的子字串的順序的名稱，因為無法使用，則第一次 5 個字元。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

從 inside out 工作︰

1. 取得[`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length)orderer 的名稱，這會傳回回的字元總數

2. 減去 5 （因為我們需要較短的字串）

3. 實際進行[`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring)。 我們開始索引`5`移字串的其餘部分。

4. 轉換到這個子字串[`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64)字串

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)所有`+`與字元`-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)所有`/`與字元`_`

## <a name="working-with-date-times"></a>使用 [日期時間

日期時間會有幫助，特別是當您嘗試從自然不支援**引動程序**的資料來源擷取資料。  您也可以使用日期時間，找出的時間長度不同的步驟。 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "actions" {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
                },
                "runAfter": {}
            }
            "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
        }
    },
    "outputs": {}
}
```

在此範例中，我們會擷取`startTime`的上一個步驟。 取得目前的時間，並在扣除一第二︰[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (您可以使用其他的時間單位，例如`minutes`或`hours`)。 最後，我們可以比較這兩個值。 如果第一個小於第二個，然後就表示超過一個的第二個經過因為順序是第一份放置。 

也請注意，我們可以使用字串格式子若要設定格式的日期︰ 使用查詢字串中[`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)取得 RFC1123。 所有日期[記載於 MSDN 上](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)的格式設定。 

## <a name="using-deployment-time-parameters-for-different-environments"></a>不同環境中使用部署時間參數

通常會有部署生命週期您有的開發環境、 暫存的環境，然後在生產環境。 在所有這些您可能會想要在相同的定義，但使用不同的資料庫，例如。 同樣地，您可能要跨多個不同區域的相同的定義用於高的顯示狀態，但想討論到該區域的資料庫的每個邏輯應用程式執行個體。 

請注意，這是不同採用不同的參數，在*執行階段*，，您應該使用`trigger()`函數為圖說文字上方。 

您可以開始與非常簡單定義如下所示︰

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

然後，在實際`PUT`邏輯應用程式要求您可以提供參數`uri`。 請注意，為已經沒有這個參數需要邏輯應用程式的內容中的預設值︰

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

在每個環境然後可以提供的不同值`connection`參數。 

請參閱建立及管理邏輯應用程式的選項的所有的[REST API 文件](https://msdn.microsoft.com/library/azure/mt643787.aspx)。 
