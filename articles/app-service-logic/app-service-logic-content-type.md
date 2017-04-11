<properties
   pageTitle="邏輯應用程式內容輸入處理 |Microsoft Azure"
   description="了解邏輯應用程式的處理方式在 [設計] 及 [執行階段內容類型"
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
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>邏輯應用程式內容輸入處理

有許多不同類型的內容，可以透過邏輯應用程式-包括 JSON、 XML、 一般檔案，以及二進位資料流動。  同時支援所有內容類型，部分原本理解邏輯應用程式引擎，而且其他人可能需要轉換或轉換視。  下列文章會描述引擎處理不同的內容類型的方式，以及如何正確處理視。

## <a name="content-type-header"></a>內容類型的標頭

若要開始簡單，我們來看看這兩個`Content-Types`，不需要任何轉換或轉換成使用中的邏輯應用程式-`application/json`和`text/plain`。

### <a name="applicationjson"></a>應用程式/json

工作流程引擎依賴`Content-Type`頁首 HTTP 呼叫決定適當的處理方式。  與內容類型的任何要求`application/json`會儲存及處理 JSON 物件。  此外，JSON 內容可以依預設剖析，而不需要任何轉換。  因此，有的內容類型標頭的要求`application/json `像這樣︰

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

無法剖析的運算式，例如工作流程中`@body('myAction')['foo'][0]`取得值 (在此情況下， `bar`)。  需要沒有其他轉換。  如果您正在使用的是 JSON，但沒有頁首指定資料，您可以手動將它轉換為使用 JSON`@json()`函數 (例如︰ `@json(triggerBody())['foo']`)。

### <a name="textplain"></a>純文字 /

類似`application/json`，收到 HTTP 訊息`Content-Type`標題`text/plain`會儲存在其原始格式。  此外，如果包含在接下來的動作，沒有任何轉換要求會移與`Content-Type`:`text/plain`頁首。  例如，如果使用一般檔案，您可能會收到下列 HTTP 內容︰

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  在下一個動作您傳送其為另一個邀請內文 (`@body('flatfile')`)，要求必須`text/plain`內容類型的標頭。  如果您正在使用的是純文字，但沒有頁首指定資料，您可以手動將它轉換為文字使用`@string()`函數 (例如︰ `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>應用程式/xml 資料流應用程式/8 位元和轉換程式函數

邏輯應用程式引擎一律會保留`Content-Type`的已收到 HTTP 要求或回應。  這表示如果將內容可能會收到`Content-Type`的`application/octet-stream`，包括的後續的巨集指令，沒有轉換會導致的外寄的要求的`Content-Type`: `application/octet-stream`。  引擎這種方式可以 guaruntee 資料將不會遺失移動整個工作流程。  不過，動作狀態 （輸入與輸出） 會儲存在 JSON 物件，如跨越整個工作流程。  這表示以保留某些資料類型、 引擎會轉換成二進位 base64 編碼字串使用適當保留兩者的中繼資料的內容`$content`和`$content-type`-這會自動轉換。  您可以手動將轉換內容類型之間使用內建的轉換程式函數︰

* `@json()`-轉換的資料`application/json`
* `@xml()`-轉換的資料`application/xml`
* `@binary()`-轉換的資料`application/octet-stream`
* `@string()`-轉換的資料`text/plain`
* `@base64()`-轉換 base64 字串中的內容
* `@base64toString()`-轉換至 base64 編碼字串`text/plain`
* `@base64toBinary()`-轉換至 base64 編碼字串`application/octet-stream`
* `@encodeDataUri()`-編碼的字串做為 dataUri 位元組陣列
* `@decodeDataUri()`-將 dataUri 位元組陣列中

例如，如果您收到的 HTTP 要求的`Content-Type`:`application/xml`的︰

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

我無法執行的投票，並在稍後使用類似`@xml(triggerBody())`，或函數，例如`@xpath(xml(triggerBody()), '/CustomerName')`。

### <a name="other-content-types"></a>其他內容類型

其他類型的內容受到支援，並可搭配邏輯應用程式，但可能會要求您手動解碼擷取郵件本文`$content`。  例如，如果我已關閉的觸發`application/x-www-url-formencoded`要求看起來如下︰

```
CustomerName=Frank&Address=123+Avenue
```

由於此不是純文字或 JSON 它會儲存在的動作︰

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

位置`$content`會編碼為 base64 字串以保留所有資料的內容。  目前沒有原生函數的表單資料，因為我仍然可以使用此工作流程中的資料，以手動方式存取函數，例如資料`@string(body('formdataAction'))`。  如果我想要也有我外寄要求`application/x-www-url-formencoded`內容類型的標頭，我無法只將其新增至動作本文不等任何轉換`@body('formdataAction')`。  不過，只能使用本文中的唯一參數時`body`輸入。  如果您嘗試執行`@body('formdataAction')`內的`application/json`要求您會收到執行階段錯誤，它將會傳送編碼的本文。
