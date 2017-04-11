<properties
   pageTitle="邏輯應用程式迴圈、 領域及 Debatching |Microsoft Azure"
   description="邏輯應用程式循環播放、 範圍及 debatching 概念"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>邏輯應用程式迴圈、 領域及 Debatching
  
>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2016年 04-01-預覽的結構描述及更新版本。  概念類似的較舊的結構描述，但範圍只適用於此結構描述及更新版本。
  
## <a name="foreach-loop-and-arrays"></a>ForEach 迴圈和陣列
  
邏輯應用程式可讓您針對一組資料的循環播放，並針對每個項目執行動作。  這是可能透過`foreach`巨集指令。  在設計工具中，您可以指定要新增的每個迴圈。  選取您想要重複的陣列之後，就可以開始新增動作。  目前您受限於 foreach 迴圈，每一個動作，但這項限制將會消除在未來幾週。  一次在循環播放，您可以開始指定什麼應該出現在每個值的陣列。

如果使用的程式碼檢視，您可以指定的每個迴圈下方。  這是範例的每個迴圈，傳送電子郵件包含 「 microsoft.com 」 的每個電子郵件地址︰

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A`foreach`動作可以持續留在上陣列多達 5000 個資料列。  可能需要將郵件新增至佇列中，視流量控制平行，可以執行每次。
  
## <a name="until-loop"></a>循環播放直到
  
  直到條件符合時，您可以執行的巨集指令或系列的動作。  最常見的案例呼叫結束點，直到您得到您要尋找的回應。  在設計工具中，您可以指定將循環播放直到。  新增動作內循環播放之後，您可以設定 [結束] 條件，以及循環播放限制。  循環週期之間有 1 分鐘的延遲。
  
  如果使用的程式碼檢視，您可以指定像下面的循環播放，直到。  這是呼叫 HTTP 結束點，直到回應內容有值 「 完成 」 的範例。  它會完成何時有 
  
  * HTTP 回應具有 「 完成 」 的狀態
  * 它具有嘗試 （1 小時）
  * 它已迴圈 100 的時間
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn 和 Debatching

有時觸發程序可能會收到您想要 debatch 開始每個項目的工作流程的項目陣列。  這可以完成透過`spliton`] 命令。  根據預設，如果您的觸發程序 swagger 指定內容的陣列，`spliton`便會新增並開始執行每個項目。  SplitOn 只能加入觸發程序。  這可以手動設定或中定義的程式碼檢視覆寫。  SplitOn 可以 debatch 目前陣列多達 5000 個項目。  您不能`spliton`及也實作 syncronous 回應模式。  有一部分的任何工作流程`response`巨集指令，除了`spliton`執行 asyncronously，傳送立即`202 Accepted`回應。  

SplitOn 可以指定程式碼檢視中，如下列範例。  這將收到的項目陣列和 debatches 上每個資料列。

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>範圍

若要將一系列的動作一起使用範圍可能是。  這是很有用實作例外處理。  在設計工具，可以新增新的範圍，並開始新增其任何動作。  您可以定義範圍中的程式碼檢視，如下所示︰


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
