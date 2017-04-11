<properties
    pageTitle="邏輯應用程式限制及設定 |Microsoft Azure"
    description="服務限制，不適用於邏輯應用程式的設定值的概觀。"
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>邏輯應用程式限制及設定

以下是在目前的限制及設定詳細資料的 Azure 邏輯應用程式的資訊。

## <a name="limits"></a>限制

### <a name="http-request-limits"></a>HTTP 要求限制

這些是單一 HTTP 要求及/或連接器呼叫的限制

#### <a name="timeout"></a>逾時

|名稱|限制|備忘稿|
|----|----|----|
|要求逾時|1 分鐘|視需要可以賠償[非同步圖樣](app-service-logic-create-api-app.md)或[循環播放直到](app-service-logic-loops-and-scopes.md)|

#### <a name="message-size"></a>郵件大小

|名稱|限制|備忘稿|
|----|----|----|
|郵件大小|50 MB|某些連接器與 Api 可能不支援 50 MB。  要求觸發程序支援最多 25 MB|
|評估運算式的限制|131,072 字元|`@concat()``@base64()`，`string`不能超過此|

#### <a name="retry-policy"></a>再試一次原則

|名稱|限制|備忘稿|
|----|----|----|
|嘗試再試一次|4|可以使用[原則參數再試一次](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)設定|
|重試最大的延遲|1 小時|可以使用[原則參數再試一次](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)設定|
|重試 min 延遲|20 最小值|可以使用[原則參數再試一次](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)設定|

### <a name="run-duration-and-retention"></a>執行的持續時間] 及 [保留

這些是執行之單一邏輯應用程式的限制。

|名稱|限制|備忘稿|
|----|----|----|
|執行 [持續時間|90 天||
|儲存保留|90 天|這是執行的開始時間|
|最小值循環間隔|15 秒||
|最大值循環間隔|500 天||


### <a name="looping-and-debatching-limits"></a>迴圈和 debatching 限制

這些是執行之單一邏輯應用程式的限制。

|名稱|限制|備忘稿|
|----|----|----|
|ForEach 項目|5000|您可以使用[查詢動作](../connectors/connectors-native-query.md)，篩選較大的陣列，視需要|
|直到高次數|10000||
|SplitOn 項目|5000||
|ForEach 平行|20|您可以藉由新增將為連續 foreach`"operationOptions": "Sequential"`至`foreach`巨集指令|


### <a name="throughput-limits"></a>處理限制

這些是單一邏輯應用程式執行個體的限制。 

|名稱|限制|備忘稿|
|----|----|----|
|每秒的觸發程序|100|如有需要可以發佈工作流程跨多個應用程式|

### <a name="definition-limits"></a>定義限制

這些是單一邏輯應用程式定義的限制。

|名稱|限制|備忘稿|
|----|----|----|
|ForEach 中的動作|1|您可以新增，視需要將此延伸巢狀工作流程|
|每個工作流程動作|60|您可以新增，視需要將此延伸巢狀工作流程|
|允許指令巢狀深度|5|您可以新增，視需要將此延伸巢狀工作流程|
|每個地區每個訂閱的流程|1000||
|每個工作流程的觸發程序|10||
|每個運算式的最大字元數|8192||
|最大值`trackedProperties`大小以字元為單位|16000|
|`action`/`trigger`名稱限制|80||
|`description`長度限制|256||
|`parameters`限制|50||
|`outputs`限制|10||

## <a name="configuration"></a>設定

### <a name="ip-address"></a>IP 位址

從[連接器](../connectors/apis-list.md)進行通話會從下方所指定的 IP 位址。

從邏輯應用程式直接 （也就是透過[HTTP](../connectors/connectors-native-http.md)或[HTTP + Swagger](../connectors/connectors-native-http-swagger.md)） 進行通話可能來自任一[Azure 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=41653)。

|邏輯應用程式區域|輸出 IP|
|-----|----|
|澳大利亞東亞|40.126.251.213|
|澳大利亞 Southeast|40.127.80.34|
|巴西南部|191.232.38.129|
|中央印度|104.211.98.164|
|美國中部|40.122.49.51|
|中式地址|23.99.116.181|
|設定適用於美國|191.237.41.52|
|設定適用於美國 2|104.208.233.100|
|日本東亞|40.115.186.96|
|日本西部|40.74.130.77|
|北美美國中部|65.52.218.230|
|北美歐洲|104.45.93.9|
|美國中部南美洲|104.214.70.191|
|東南亞|13.76.231.68|
|南美洲印度|104.211.227.225|
|西歐|40.115.50.13|
|西印度|104.211.161.203|
|西美制]。|104.40.51.248|


## <a name="next-steps"></a>後續步驟  

- 若要開始使用邏輯應用程式，請依照[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)的教學課程。  
- [檢視一般範例與案例](app-service-logic-examples-and-scenarios.md)
- [您可以自動化商務程序，以邏輯應用程式](http://channel9.msdn.com/Events/Build/2016/T694) 
- [瞭解如何將您的系統整合邏輯應用程式](http://channel9.msdn.com/Events/Build/2016/P462)