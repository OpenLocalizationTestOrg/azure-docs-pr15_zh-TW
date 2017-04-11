<properties
   pageTitle="邏輯應用程式案例︰ 建立 Azure 函數服務匯流排觸發程序 |Microsoft Azure"
   description="若要建立之邏輯應用程式服務匯流排觸發程序使用 Azure 函數"
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
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>邏輯應用程式案例︰ 使用 Azure 函數來建立 Azure 服務匯流排觸發程序

若要建立邏輯應用程式的觸發程序，當您需要部署長接聽或任務時，您可以使用 Azure 函數。 例如，您可以建立會接聽佇列中，並立即啟動的推入觸發程序的邏輯應用程式的函數。

## <a name="build-the-logic-app"></a>建立邏輯應用程式

在此範例中，您有要觸發需要的每一個邏輯應用程式中執行的函數。 首先，建立具有 HTTP 要求的觸發程序的邏輯應用程式。 函數呼叫的端點時收到的佇列中的郵件。  

1. 建立新的邏輯應用程式。選取 [**手動]-當 HTTP 要求收到**的觸發程序。  
   或者，您可以指定使用佇列中的訊息，請使用的工具，例如[jsonschema.net](http://jsonschema.net)JSON 結構描述。 貼上觸發程序的結構描述。 如此有助於了解資料的圖形的設計工具方向，更輕鬆地透過工作流程的屬性。
1. 新增任何其他您想要收到的佇列中郵件之後，會發生的步驟。 例如，傳送透過 Office 365 電子郵件。  
1. 儲存邏輯應用程式來產生此邏輯應用程式的觸發程序的回撥 URL。 URL 會顯示觸發程序卡片上。

![觸發程序卡片上顯示的回撥 URL][1]

## <a name="build-the-function"></a>建立函數

接下來，您需要建立將作為 [觸發程序，並聽取佇列中的函數。

1. 在[Azure 函數入口網站](https://functions.azure.com/signin)中，選取**新的函數**，然後按一下**ServiceBusQueueTrigger-C#**範本。

    ![Azure 函數入口網站][2]

2. 設定服務匯流排佇列中的連線 (這會使用 Azure 服務匯流排 SDK`OnMessageReceive()`接聽)。
3. 撰寫簡單函式使用佇列中郵件的觸發程序呼叫 （建立較舊版本） 的邏輯應用程式端點。 以下是完整函數的範例。 此範例使用`application/json`訊息的內容類型，但您可以視需要變更此。

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

若要測試，新增佇列中訊息透過等[服務匯流排檔案總管](https://github.com/paolosalvatori/ServiceBusExplorer)的工具。 請參閱啟動函數會收到訊息後，立即邏輯應用程式。

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG
