<properties
   pageTitle="為可呼叫端點邏輯應用程式"
   description="如何建立及設定觸發結束點，並用於 Azure 應用程式服務中的邏輯應用程式"
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


# <a name="logic-apps-as-callable-endpoints"></a>為可呼叫端點邏輯應用程式

原生邏輯應用程式可以公開同步 HTTP 端點的觸發程序。  您也可以使用可呼叫的結束點的模式以巢狀的工作流程，透過邏輯應用程式中的 [工作流程 」 動作呼叫邏輯應用程式。

有 3 種可以收到要求的觸發程序︰

* 要求
* ApiConnectionWebhook
* HttpWebhook

文件的其餘部分，我們將使用**邀請**做為範例，但所有的原則套用相同引動程序的其他 2 類型。

## <a name="adding-a-trigger-to-your-definition"></a>新增至您定義的觸發程序
第一個步驟是將觸發程序新增至您的邏輯應用程式定義可接收內送的要求。  您可以搜尋在設計工具中的 「 HTTP 要求 「 若要新增的觸發程序卡片。 您可以定義要求主體 JSON 結構描述和設計工具會產生的權杖，可協助您剖析，並將資料從透過工作流程手動觸發程序傳送。  建議您使用的工具，例如[jsonschema.net](http://jsonschema.net)範例本文內容從產生 JSON 結構描述。

![要求觸發程序卡片][2]

儲存您的邏輯應用程式定義後，回撥 URL 會產生類似此項目︰
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

此 URL 會包含驗證所用的查詢參數的 SA 鍵。

您也可以取得這個端點 Azure 入口網站中︰

![][1]

或，則可電話︰

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>觸發程序 url 的安全性

邏輯應用程式回撥 Url 就會產生安全地使用共用的 Access 簽章。  簽章經過做為查詢參數，，並啟動邏輯應用程式之前必須進行驗證。  它會產生透過唯一私密金鑰每個邏輯應用程式、 觸發程序名稱，以及執行的作業的組合。  具有私人邏輯應用程式碼的存取權，除非它們會無法產生有效簽章。

## <a name="calling-the-logic-app-triggers-endpoint"></a>呼叫邏輯應用程式觸發程序的端點

當您建立端點的觸發程序之後時，您可以觸發它透過`POST`的完整 url。 您可以在本文中包含其他的標頭及任何內容。

如果是內容類型`application/json`然後您可以從邀請內的參考屬性。 否則，它將會視為一個二進位單位，可以傳送至其他 Api，但無法參考工作流程，而不需轉換內容。  例如，如果您將`application/xml`您可以使用內容`@xpath()`執行 xpath 擷取，或`@json()`將從 XML 轉換成 JSON。  使用內容的詳細資訊類型[可在這裡找到](app-service-logic-content-type.md)

此外，您可以指定 JSON 結構描述定義中。 這會使設計工具，以產生的權杖，然後您可以傳遞到步驟。  例如會將下列`title`和`name`權杖設計工具中使用︰

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>參照要求的內容

`@triggerOutputs()`函數會輸出傳入要求的內容。 例如，如下所示︰

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

您可以使用`@triggerBody()`快速鍵若要存取`body`屬性特別。 

## <a name="responding-to-the-request"></a>回應要求

啟動邏輯應用程式的一些要求，您可能要回應部分內容與來電者。 有新的動作類型，稱為**回應**可以用來建構狀態碼、 本文和標題您的回應。 請注意，如果沒有**回應**的圖形，邏輯應用程式端點會*立即*回應**202 接受**使用。

![HTTP 回應巨集指令][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

回覆有下列動作︰

| 屬性 | 描述 |
| -------- | ----------- |
| statusCode | 回應傳入要求 HTTP 狀態碼。 可以是第一句是 2xx]、 [4xx 或 [5xx 的任何有效的狀態碼。 不允許 3xx 狀態碼。 | 
| 本文 | 字串本文物件、 JSON 物件或甚至二進位內容參照從先前的步驟。 | 
| 頁首 | 您可以定義標題在回應中所包含的任何數字 | 

中的所有步驟邏輯應用程式所需的回應都必須完成接收回應**工作流程以巢狀的邏輯應用程式呼叫除非**原始邀請的*60 秒*內。 如果沒有回應動作達到內 60 秒，然後連入要求將 [逾時，並接收**408 用戶端逾時**HTTP 回應。  巢狀邏輯應用程式，上層邏輯應用程式會繼續等待回應等到完成後，無論的時間量所需。

## <a name="advanced-endpoint-configuration"></a>進階的端點設定

邏輯應用程式中直接存取端點的支援已經建立與使用`POST`開始執行的邏輯應用程式的方法。 **HTTP 接聽**API 應用程式先前也支援變更 URL 區段和 HTTP 方法。 您可能會額外的安全性的偶數設定或自訂的網域新增至 API 應用程式主機 （Web 應用程式裝載的 API 應用程式）。 

此功能可透過**API 管理**︰
* [變更要求的方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [變更要求的 URL 區段](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 設定您在 [傳統] Azure 入口網站中的 [**設定**] 索引標籤上的 API 管理網域
* 設定原則，以檢查的基本驗證 （**需要連結**）

## <a name="summary-of-migration-from-2014-12-01-preview"></a>從 2014年-12-01-預覽移轉的摘要

|  2014-12-01-預覽 | 2016-06-01 |
|---------------------|--------------------|
| 按一下 [在**HTTP 接聽**API 應用程式 | 按一下 [**手動觸發程序**（必要不到 API 應用程式） |
| 設定 「*自動傳送回應*」 的 HTTP 接聽程式 | 包含**回應**動作或不在工作流程定義 |
| 設定基本或 OAuth 驗證 | 透過 API 管理 |
| 設定 HTTP 方法 | 透過 API 管理 |
| 設定相對路徑 | 透過 API 管理 |
| 參照透過連入本文`@triggerOutputs().body.Content` | 透過參照`@triggerOutputs().body` |
| 在 HTTP 接聽程式**傳送 HTTP 回應**巨集指令 | 按一下**回應 HTTP 要求**（必要不到 API 應用程式）


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
