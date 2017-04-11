<properties
   pageTitle="Azure 函數使用邏輯應用程式 |Microsoft Azure"
   description="瞭解如何使用邏輯應用程式使用 Azure 函數"
   services="logic-apps,functions"
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

# <a name="using-azure-functions-with-logic-apps"></a>使用邏輯應用程式中的 Azure 函數

您可以使用從 Azure 函數中的邏輯應用程式執行 C# 或 node.js 自訂程式碼片段。  [Azure 函數](../azure-functions/functions-overview.md)提供 Microsoft Azure 中無伺服器運算。 這是很適合用於執行下列工作︰

* 進階格式設定] 或 [計算邏輯應用程式中的欄位
* 執行工作流程中的計算
* 擴充邏輯應用程式的功能與 C# 或 node.js 中支援的函數

## <a name="create-a-function-for-logic-apps"></a>邏輯應用程式建立的函數

我們建議您在 Azure 函數入口網站中建立新的函數，使用**一般 Webhook-節點**或**一般 Webhook-C#**範本。 此自動-填入範本可接受`application/json`從邏輯應用程式。  如果您選取 [**整合**] 索引標籤中 Azure 函數應該**模式**設為**Webhook**與**一般 JSON** **Webhook 類型**。  自動發現及列下邏輯應用程式設計工具中使用這些範本的函數**Azure 函數，在 [我的區域。**

Webhook 函數接受要求，並將其傳遞到透過方法`data`變數。 您可以使用點標記法，例如，以存取您內容的屬性`data.foo`。  例如，將 DateTime 值轉換成日期字串的簡單 JavaScript 函數看起來像下列範例中︰

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>從邏輯應用程式的通話 Azure 函數

在設計工具中，如果您按一下 [**動作**] 功能表中，您可以選取**我區域中的 Azure 函數**。  這會列出您的訂閱中的容器，並可讓您選擇您要撥打的函數。  

選取函數] 後，會提示您指定的輸入的內容物件。 這是邏輯應用程式傳送給函數的訊息，它必須 JSON 物件。 比方說，如果您想要從 Salesforce 觸發程序傳遞**上次修改**日期，函數裝載看起來可能像這樣︰

![最後一個 modfied 日期][1]

## <a name="trigger-logic-apps-from-a-function"></a>函數的觸發程序邏輯應用程式

您也可觸發從函數中的邏輯應用程式。  若要這麼做，只要建立手動的觸發程序的邏輯應用程式。 如需詳細資訊，請參閱[為可呼叫端點邏輯應用程式](app-service-logic-http-endpoint.md)。  然後，在您的函數，產生 HTTP 張貼至您想要傳送給邏輯應用程式的內容與手動觸發程序 URL。

### <a name="create-a-function-from-the-designer"></a>從設計工具建立函數

您也可以建立從 node.js webhook 函數，在設計工具。 首先，請選取 [**我的區域] 中的 Azure 函數**，然後選擇 [容器您函數。  如果您還沒有容器，必須先建立一個從[Azure 函數入口網站](https://functions.azure.com/signin)。 然後選取 [**建立新檔案**]。  

若要產生根據您想要計算之資料的範本，指定您打算將傳遞給函數的物件。 這必須是 JSON 物件。 比方說，如果您從 FTP 動作傳入檔案內容的內容裝載看起來像這樣︰

![內容的內容][2]

>[AZURE.NOTE] 此物件未轉換為字串，因為內容會直接加入至 JSON 內容。 不過，如果不是 JSON 權杖 （也就是字串或 JSON 物件/陣列） 會出的錯誤。 將它轉換為字串，只要將加上引號本文中的第一個圖例所示。

設計工具然後產生函數範本，您可以建立內嵌。 變數預先建立您打算將傳遞給函數的內容。




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png
