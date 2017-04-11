<properties
    pageTitle="邏輯應用程式中新增週期觸發程序 |Microsoft Azure"
    description="循環觸發程序，以及如何使用 Azure 邏輯應用程式的概觀。"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-recurrence-trigger"></a>開始使用週期性觸發程序

藉由使用週期性觸發程序，您可以在雲端建立功能強大的工作流程。

例如，您可以︰

- 排程執行 SQL 預存程序每天的工作流程。
- 以電子郵件傳送所有傳出 tweets 的摘要一週的最後一個有關特定湊標籤。

若要開始使用邏輯應用程式中的循環觸發程序，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="use-a-recurrence-trigger"></a>使用週期性觸發程序

觸發程序是可以用來開始工作流程中的邏輯應用程式所定義的事件。 [深入瞭解引動程序](connectors-overview.md)。

以下是如何設定週期性觸發程序邏輯應用程式中的範例順序︰

1. 新增**週期**觸發程序邏輯應用程式中的第一個步驟。
2. 填入循環間隔的參數。

邏輯應用程式啟動後每個時間間隔的執行]。

![HTTP 觸發程序](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>觸發程序的詳細資料

循環觸發程序有下列屬性，您可以設定。

它會邏輯應用程式啟動後指定的時間間隔。
A * 表示它是必要的欄位。

|顯示名稱|屬性名稱|描述|
|---|---|---|
|Frequency *|頻率|The unit of time: `Second`, `Minute`, `Hour`, `Day`, or `Year`.|
|間隔 *|間隔|循環的間隔的指定的頻率。|
|時區|時區|如果而 UTC 位移不提供開始時間，則會使用這個時區。|
|開始時間|開始時間|[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)開始時間。|
<br>


## <a name="next-steps"></a>後續步驟

現在，請試試看的平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。 您可以查看我們的[Api 清單](apis-list.md)來探索邏輯應用程式中其他可用的連接器。
