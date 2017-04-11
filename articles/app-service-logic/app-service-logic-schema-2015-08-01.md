<properties 
    pageTitle="新結構描述版本 2015年-08-01-預覽" 
    description="瞭解如何撰寫邏輯應用程式的最新版本的 JSON 定義" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>新結構描述版本 2015年-08-01-預覽

新的結構描述和 API 邏輯應用程式的版本有哪些改善可靠性改良功能的數字和輕鬆使用邏輯應用程式。 有 4 的主要差異︰

1. **APIApp**動作類型已更新為新的**APIConnection**動作類型。
2. 重新命名**重複**至**Foreach**。
3. 不再需要的**HTTP 接聽**API 應用程式。
4. 呼叫子工作流程使用新的結構描述。

## <a name="1-moving-to-api-connections"></a>1.前往 API 連線

最大的變更是您不再需要將 API 應用程式部署到使用 API 的 Azure 訂閱。 有 2 的方式，您可以使用 Api:
* 受管理的 API
* 您自訂的 Web API

因為其管理及裝載模型不同，這些被處理方式稍有不同。 此模型的優點之一是您不再資源群組中受限制部署的資源。 

### <a name="managed-apis"></a>受管理的 Api

有數個 API 的所管理的 Microsoft 代表您，例如 Office 365、 Salesforce、 Twitter、 FTP 等... 這些受管理的 API 部分可做為-，例如 Bing 翻譯，而有些則需要設定。 此設定稱為*連線*。

例如，當您使用 Office 365，您需要建立包含您的 Office 365 登入憑證的連線。 此 token 加安全地儲存和重新整理，因此邏輯應用程式隨時都可以呼叫 Office 365 API。 或者，如果您要連線至 SQL 或 FTP 伺服器時，您需要建立連線字串的連線。 

這些動作會呼叫內定義`APIConnection`。 以下是連線的範例打電話給 Office 365 來傳送電子郵件:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

輸入部分的唯一 API 連線是`host`物件。 此頁面包含兩個部分︰`api`和`connection`。

`api`具有執行階段存放位置管理的 API 的 URL。 您可以為您中看到所有使用受管理的 api，呼叫`GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`。

當您使用的 API 時，可能或可能沒有定義的任何**連線參數**。 如果沒有**連線**不是必要。 如果是這樣，您會有建立的連線。 當您建立的連線，它會有您所選擇的名稱，然後您參考在`connection`內的物件`host`物件。 若要建立資源群組中的連線，請連絡︰

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

使用下列內容︰


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>部署 Azure 資源管理員範本中管理的 Api

只要互動式登入您不需要，您可以在 ARM 範本中建立完整的應用程式。 如果需要登入，您可以設定所有項目 ARM 範本，但仍會有造訪授權連線入口網站。 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

您可以在此範例中的連線會只是標準的資源的資源群組中的 [即時看到。 他們參考 managedAPIs 提供給您在您的訂閱。

### <a name="your-custom-web-apis"></a>您的自訂網頁 Api

如果您是使用您自己的 API 的 （特別是不受 Microsoft 管理文件），然後應該使用內建的**HTTP**動作來撥打電話給。 才有理想的體驗，您應該針對您的 API 公開 swagger 結束點。 這樣會啟用邏輯應用程式設計工具，針對您的 API 呈現輸入和輸出。 不 swagger，設計工具將只能顯示輸入和輸出為不透明 JSON 物件。

以下是範例顯示新`metadata.apiDefinitionUrl`屬性︰
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

如果您主控您網站的 API**應用程式**服務，然後將會自動顯示在清單中的設計工具中可用的動作。 如果沒有出現，您必須在 URL 中直接貼上。 必須未經驗證 swagger 端點，才有可用的邏輯應用程式設計工具內 （雖然您可能會保護與任何方法支援 Swagger 本身的 API）。

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>您已經部署的 API 應用程式使用 2015年-08-01-預覽

如果您先前部署 API 應用程式，您可以透過**HTTP**動作來進行通話。

例如，如果您使用 Dropbox 清單檔案時，您可能像這樣在**2014年-12-01-預覽**結構描述版本定義中︰

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

您可以建立 （參數] 區段下方的邏輯應用程式定義仍維持不變） 等相等 HTTP 動作︰

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

說明這些屬性一個接一個︰

| 動作屬性 |  描述 |
| --------------- | -----------  |
| `type` | `Http`而不是`APIapp` |
| `metadata.apiDefinitionUrl` | 如果您想要使用此動作邏輯應用程式設計工具] 中，您會想要包含的中繼資料端點。 這是從建構︰`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | 這是從建構︰`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | 永遠`POST` |
| `inputs.body` | 相同的 api 應用程式的參數 | 
| `inputs.authentication` | 相同 api 應用程式驗證 |

此方法應該適用於所有 API 應用程式的動作。 不過，請記住這些先前的 API 應用程式已不再支援，，您應該移至其中一個兩個其他選項上方 （受管理的 API 或裝載您的自訂網頁 API）。

## <a name="2-repeat-renamed-to-foreach"></a>2.重複重新命名為 Foreach

結構描述舊版我們收到許多客戶意見反應**重複**令人混淆，未適當地擷取真的是針對每個迴圈。 如此一來，我們已重新命名該**Foreach**。 例如︰

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

現在想要寫成︰

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

先前函數`@repeatItem()`用來參照目前正在一直重複的項目。 只要已經簡化了此`@item()`。 

### <a name="referencing-the-outputs-of-the-foreach"></a>參照 Foreach 的輸出
若要進一步簡化， **Foreach**動作的輸出不會包裝在稱為**repeatItems**物件。 這表示，而上述重複的輸出是︰

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

現在會是︰

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

當參考這些輸出，若要移至本文的動作您必須執行︰

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

現在您可以改為:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

使用這些變更，函數`@repeatItem()`，`@repeatBody()`和`@repeatOutputs()`將會移除。

## <a name="3-native-http-listener"></a>3.原生 HTTP 接聽程式 
HTTP 接聽程式功能現在內建，所以您不再需要部署 HTTP 接聽 API 應用程式。 瞭解[如何讓您可呼叫的邏輯應用程式端點以下的完整詳細資料](app-service-logic-http-endpoint.md)。 

使用這些變更，此函數`@accessKeys()`會移除，被取代`@listCallbackURL()`函數的快速端點 （如果有需要）。 此外，您現在必須至少有一個觸發程序中定義邏輯應用程式現在。 如果您想要`/run`工作流程，必須具備下列其中一項`manual`，`apiConnectionWebhook`或`httpWebhook`引動程序。 

## <a name="4-calling-child-workflows"></a>4.電話子工作流程

之前，呼叫子工作流程，您必須移至該工作流程與快速存取權杖，然後貼在您要撥打的子邏輯應用程式的定義。 使用新的結構描述版本，邏輯應用程式引擎會自動產生 SA 子流程，這表示您沒有任何機密貼上定義的階段。  以下是範例︰

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

第二個改進是我們將會讓子工作流程的完整存取要求。 也就是說，您可以將傳遞的參數*標題*物件和 [*查詢*] 區段中，您可以完全定義整個本文。

最後，有子流程必要的變更。 而之前您可能只子流程直接撥打;現在，您需要撥打上層的工作流程中定義的觸發程序端點。 一般而言，這表示您將會新增觸發程序的類型**手動**，然後上層定義中使用的。 請注意，`host`屬性特別是有`triggerName`，因為您指定的必須是您所叫用的觸發程序。

## <a name="other-changes"></a>其他變更

### <a name="new-queries-property"></a>新的查詢屬性
所有動作類型，現在都支援稱為**查詢**的新輸入。 這可能是結構化的物件，而不是您不必手動組合字串。

### <a name="parse-function-renamed"></a>重新命名的 parse() 函數
我們會立即新增更多內容類型，`parse()`函數重新命名為`json()`。

## <a name="coming-soon-enterprise-integration-apis"></a>即將推出︰ 企業整合 Api
在目前，我們請不要，但有受管理的企業整合 Api （例如 AS2)，您可以使用的版本。 這些會被即將推出如下所述[藍圖](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)。 同時，您可以使用您現有的部署的 BizTalk Api 透過 HTTP 動作]，如上述的 「 使用您已經部署的 API 應用程式。 」
