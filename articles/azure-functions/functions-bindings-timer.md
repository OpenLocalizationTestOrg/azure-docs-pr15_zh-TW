<properties
    pageTitle="Azure 函數計時器觸發程序 |Microsoft Azure"
    description="瞭解如何使用計時器引動程序中 Azure 函數。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數，如函數、 事件處理，動態計算，無架構"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Azure 函數計時器觸發程序

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定計時器引動程序中 Azure 函數。 計時器觸發根據排程、 一次，或設為重複發生的通話函數。  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>function.json 計時器觸發程序

*Function.json*檔案提供的排程運算式。 例如，下列排程執行函數每分鐘︰

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

計時器觸發程序自動處理多個執行個體延展︰ 單一執行個體的特定計時器函數會執行所有執行個體。

## <a name="format-of-schedule-expression"></a>排程運算式的格式

排程運算式是[CRON 運算式](http://en.wikipedia.org/wiki/Cron#CRON_expression)，其中包含 6 個欄位︰ `{second} {minute} {hour} {day} {month} {day of the week}`。 

許多您尋找線上 cron 運算式，如果您複製其中您將需要調整的額外的欄位，讓省略 {第二個} 功能變數，請注意。 

以下是一些其他排程運算式範例︰

若要一次每 5 分鐘觸發程序︰

```json
"schedule": "0 */5 * * * *"
```

若要頂端的 [每小時一次觸發程序︰

```json
"schedule": "0 0 * * * *",
```

若要一次每兩個小時觸發程序︰

```json
"schedule": "0 0 */2 * * *",
```

若要觸發每小時從 9 AM 至 5 PM:

```json
"schedule": "0 0 9-17 * * *",
```

若要觸發在 9:30 AM 每日︰

```json
"schedule": "0 30 9 * * *",
```

若要觸發在 9:30 AM 每個工作天︰

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>計時器觸發程序 C# 程式碼範例

這個 C# 程式碼範例將單一記錄每次觸發函數。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
