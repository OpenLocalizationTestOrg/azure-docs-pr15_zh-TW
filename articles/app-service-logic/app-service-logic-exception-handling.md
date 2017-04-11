<properties
   pageTitle="邏輯應用程式的例外處理 |Microsoft Azure"
   description="瞭解錯誤，以及處理 Azure 邏輯應用程式的例外狀況"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>邏輯應用程式錯誤和例外處理

邏輯應用程式提供豐富的工具，以確保您整合模式強大和彈性失敗。  使用任何整合架構的挑戰確保正確地處理停機時間或相依系統的問題。  邏輯應用程式中，可讓您處理的錯誤第一本課程的體驗，可讓您在 [例外狀況和工作流程中的錯誤所需的工具。

## <a name="retry-policies"></a>再試一次原則

最基本的例外狀況和錯誤處理類型是重試原則。  如果動作應該重試如果初始要求逾時，或失敗，此原則可定義 (導致 429 任何要求或 5xx 回應)。  根據預設，所有巨集指令，請透過 20 秒的時間間隔重新 4 次。  因此，如果收到的第一個要求`500 Internal Server Error`回應時，工作流程引擎暫停 20 的秒數，並嘗試要求一次。  如果所有重試後回應仍然例外或失敗，工作流程會繼續，且標示為動作狀態`Failed`。

您可以設定重試原則中**輸入**的特定動作。  若要嘗試為 4 的時間超過 1 小時的時間間隔，可以設定重試原則。  輸入屬性的完整詳細資料可[找到 msdn][retryPolicyMSDN]。

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

如果您想要重試 4 次，並等待 10 分鐘之間每一次嘗試 HTTP 動作您有下列定義︰

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

如需支援的語法的詳細資訊，請檢視[MSDN 上的 [重試原則] 區段][retryPolicyMSDN]。

## <a name="runafter-property-to-catch-failures"></a>若要找出失敗 RunAfter 屬性

每個邏輯應用程式動作宣告哪些動作需要先完成動作會開始。  您可以將此為您的工作流程中的步驟順序。  此順序就是`runAfter`動作定義中的屬性。  為描述的動作]，[動作狀態就會執行動作的物件。  根據預設，透過設計工具所新增的所有巨集指令設定為`runAfter`上一個步驟的上一個步驟是如果`Succeeded`。  不過，您可以自訂這個值前一個動作時，啟動動作`Failed`， `Skipped`，或可能的一組這些值。  如果您想要新增項目指定服務匯流排主題，在特定的動作後`Insert_Row`失敗，您可以使用下列`runAfter`設定︰

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

請注意`runAfter`屬性設定為啟動`Insert_Row`動作是`Failed`。  若要執行巨集指令，如果動作狀態是 [ `Succeeded`， `Failed`，或`Skipped`語法為︰

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] 執行之後失敗的上一個動作，且順利完成，動作會標示為`Succeeded`。  這種行為代表如果您已成功攔截所有失敗的工作流程執行本身會標示為`Succeeded`。

## <a name="scopes-and-results-to-evaluate-actions"></a>評估動作的範圍和結果

類似於您可以執行之後個別的動作，您也可以做為動作的邏輯群組的群組動作內[範圍](app-service-logic-loops-and-scopes.md)-在一起。  範圍可組織您邏輯應用程式的動作，並執行彙總的評估範圍的狀態有幫助的。  範圍本身完成某個範圍內的所有動作後，將會收到狀態。  如果在執行分支中的最後一個動作是範圍狀態決定使用相同的準則，以執行-`Failed`或`Aborted`的狀態是 [ `Failed`。

您可以`runAfter`範圍已被標示`Failed`啟動所發生的範圍內的任何失敗的特定動作。  執行範圍失敗後，可讓您建立一項動作來攔截失敗，如果範圍內的*任何*動作會失敗。

### <a name="getting-the-context-of-failures-with-results"></a>取得結果失敗的內容

攔截失敗從範圍是非常實用，但您也可能會想要了解完全無法，哪些動作的內容，以及任何錯誤或狀態碼傳回。  `@result()`工作流程函數提供內容到某個範圍內的所有巨集指令的結果。

`@result()`使用單一參數的範圍名稱，然後傳回陣列的所有動作會從該範圍內。  這些動作物件包含與相同的屬性`@actions()`輸出物件，包括動作開始時間、 動作結束時間、 動作狀態、 巨集指令輸入、 動作相互關聯識別碼和動作。  您可以輕鬆地配對`@result()`函數與`runAfter`傳送某個範圍內的任何失敗的動作的內容。

如果您想要執行的巨集指令的*每個*動作範圍內的`Failed`，您可以配對`@result()`**[篩選陣列](../connectors/connectors-native-query.md)**動作與**[ForEach](app-service-logic-loops-and-scopes.md)**循環播放。  這個選項可讓您篩選的結果失敗的動作陣列。  您可以採取的篩選的結果的陣列，使用 [ **ForEach**循環播放每次失敗的執行的動作。  以下是範例所示，後面接著的詳細說明。  此範例會傳送 HTTP POST 要求與回應主體無法任何動作的範圍內`My_Scope`。

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

以下是發生了什麼的詳細逐步解說︰

1. 若要篩選的**篩選陣列**動作`@result('My_Scope')`取得內的所有動作的結果`My_Scope`
1. **篩選矩陣**的條件是任何`@result()`等於狀態的項目`Failed`。  這會篩選的所有動作結果的陣列`My_Scope`只失敗的動作結果的陣列。
1. **每個**上執行動作的**篩選陣列**輸出。  這會執行動作*的每個*失敗動作結果我們篩選上方。
    - 如果發生失敗中的動作的範圍中的單一動作`foreach`一次只能執行。  許多失敗的動作會造成錯誤每一個動作。
1. 上的 [傳送 HTTP 張貼`foreach`項目回應內容或`@item()['outputs']['body']`。  `@result()`項目] 圖形是相同`@actions()`重塑及可剖析相同的方式。
1. 也包含兩個自訂的標頭失敗的動作名稱`@item()['name']`及失敗執行追蹤識別碼的用戶端`@item()['clientTrackingId']`。

參照的範例如下的單一`@result()`項目。  您可以看到`name`， `body`，及`clientTrackingId`剖析上述範例中的內容。  請注意外的`foreach`，`@result()`傳回陣列的物件。

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

您可以使用上述運算式執行其他處理圖樣的例外狀況。  您可以選擇來執行單一處理動作，接受失敗和移除的整個篩選的陣列的範圍之外的例外狀況`foreach`。  您也可以包含其他有用的內容從`@result()`如上所示的回應。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診斷與遙測

上述圖樣的絕佳方式處理的錯誤和例外狀況中執行，但您也可以找出並回應獨立執行本身的錯誤。  [Azure 診斷](app-service-logic-monitor-your-logic-apps.md)提供簡單的方法，若要傳送所有的工作流程事件 （包括所有執行和動作狀態） Azure 儲存體帳戶或 Azure 事件中心。  您可以監視記錄及指標，或發佈給將您想要評估的執行的狀態任何監視工具。  將[資料流分析](https://azure.microsoft.com/services/stream-analytics/)串流透過 Azure 事件中樞的所有事件為其中一個可能的選項。  在 [串流分析您可以從診斷記錄撰寫人員任何異常、 平均或失敗的即時查詢。  佇列、 主題、 SQL、 DocumentDB，Power BI 等其他資料來源，可以輕鬆地輸出資料流分析。

## <a name="next-steps"></a>後續步驟
- [請參閱如何一位客戶建置強大的錯誤處理邏輯應用程式](app-service-logic-scenario-error-and-exception-handling.md)
- [尋找更多範例邏輯應用程式與案例](app-service-logic-examples-and-scenarios.md)
- [瞭解如何建立自動的部署邏輯應用程式](app-service-logic-create-deploy-template.md)
- [設計和部署 Visual Studio 邏輯應用程式](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9