<properties
   pageTitle="記錄檔分析通知 REST API"
   description="記錄檔分析通知 REST API 可讓您建立及管理通知作業管理套件 (OMS)。  本文提供 API 和幾個範例的詳細資料執行不同的作業。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>記錄檔分析提醒 REST API

記錄檔分析通知 REST API 可讓您建立及管理通知作業管理套件 (OMS)。  本文提供 API 和幾個範例的詳細資料執行不同的作業。

記錄檔分析搜尋 REST API RESTful，而可透過 Azure 資源管理員 REST API 加以存取。 在這份文件中您會發現範例 API PowerShell 命令列使用[ARMClient](https://github.com/projectkudu/ARMClient)，可簡化叫用 Azure 資源管理員 API 開啟來源命令列工具存取的位置。 使用 ARMClient 及 PowerShell 是很多選項] 以存取記錄分析搜尋 API。 使用這些工具，您可以利用 RESTful Azure 資源管理員 API，以撥打 OMS 工作區，並執行中的 [搜尋] 命令。 API 會輸出 JSON 格式，您的搜尋結果，可讓您以程式設計方式使用許多不同方式的搜尋結果。

## <a name="prerequisites"></a>必要條件
目前，通知只能使用已儲存的搜尋記錄分析中建立。  您可以參考[記錄搜尋 REST API](log-analytics-log-search-api.md) ，如需詳細資訊。

## <a name="schedules"></a>排程
已儲存的搜尋可以有一或多個排程。 排程定義搜尋頻率是執行與準則識別出的時間間隔。
排程下表中包含的內容。

| 屬性  | 描述 |
|:--|:--|
| 間隔 | 執行搜尋的頻率。 以分鐘為單位。 |
| QueryTimeSpan | 準則進行評估的時間間隔。 必須等於或大於間隔。 以分鐘為單位。 |
| 版本 | 使用 API 版本。  目前這應該一律設為 1。 |

例如，請考慮事件查詢 15 分鐘的時間間隔與 30 分鐘的時段。 在此情況下，查詢會執行每 15 分鐘，並想要觸發警示，如果準則以解析為 true 留在上 30 分鐘之內。

### <a name="retrieving-schedules"></a>擷取排程
使用 [取得方法來擷取所有儲存的搜尋的排程。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

使用排程識別碼的取得方法，以擷取特定排程儲存的搜尋。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

以下是範例回應的排程。

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>建立排程
使用唯一的排程識別碼的放入方法來建立新的排程。  請注意兩個時間表不能有相同的識別碼，即使是與其他相關聯儲存的搜尋。  當您建立排程 OMS 主控台中時，為排程識別碼建立 GUID

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>編輯排程
若要修改的排程使用相同的儲存搜尋現有的排程識別碼的放置方式。  要求的主體必須包含 etag 的排程。

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>刪除排程
若要刪除排程使用排程識別碼刪除方法。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>動作
排程可以有多個動作。 也可能定義臨界值，決定時的搜尋結果符合某些條件或動作可能定義一個或多個程序，例如傳送郵件或啟動 runbook 中，執行。  某些動作會定義兩臨界值符合時所執行的程序。

所有巨集指令具有下列表格中的屬性。  不同類型的通知有不同的其他屬性的如下所述。

| 屬性 | 描述 |
|:--|:--|
| 類型 | 動作類型。  目前的可能值是提醒和 Webhook。 |
| 名稱 | 提醒的顯示名稱。 |
| 版本 | 使用 API 版本。  目前這應該一律設為 1。 |

### <a name="retrieving-actions"></a>擷取動作
使用 [取得方法來擷取排程中的所有動作。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

使用動作識別碼的取得方法以擷取特定的動作。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>建立或編輯動作
使用放入方法是唯一的排程，若要建立新巨集指令的巨集指令識別碼。  當您在 OMS 主控台中建立巨集指令時，GUID 是巨集指令 id。

若要修改的排程使用相同的儲存搜尋現有的巨集指令識別碼的放置方式。  要求的主體必須包含 etag 的排程。

要求的格式建立新的動作會因動作類型，以便在以下各節提供這些範例。

### <a name="deleting-actions"></a>刪除動作
使用動作識別碼的刪除方法若要刪除動作。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>通知的動作
排程應有只能有一個提醒的動作。  通知的動作有一或多個下表中的章節。  進一步請見下方說明每個。

| 區段 | 描述 |
|:--|:--|
| 閥值 | 巨集指令執行時的準則。 |  
| EmailNotification | 傳送郵件給多個收件者。 |
| 修復 | 啟動 Azure 自動化，以嘗試修正問題識別 runbook。 |

#### <a name="thresholds"></a>臨界值
通知動作應有只能有一個閥值。  當已儲存的搜尋結果符合動作，搜尋相關聯的臨界值時，會執行中的任何其他處理程序。  動作也可以包含閥值，以便使用其他類型不包含臨界值的動作。

臨界值會包含下列表格中的內容。

| 屬性 | 描述 |
|:--|:--|
| 運算子 | 閥值比較運算子。 <br> gt = 大於 <br> lt = 小於 |
| 值 | 臨界值的值。 |

例如，請考慮使用 15 分鐘、 30 分鐘的時段及大於 10 臨界值的時間間隔的事件查詢。 在此情況下，查詢會執行每 15 分鐘，並想要觸發警示，如果其傳回 10 個在 30 分鐘範圍內所建立的事件。

以下是範例回應的具有臨界值的動作。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

若要建立新的閥值動作的使用唯一動作識別碼的放置方式。  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

若要修改的閥值動作使用現有的巨集指令識別碼放入方法。  要求的主體必須包含 etag 的動作。

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>電子郵件通知
電子郵件通知給一或多個收件者傳送郵件。  他們下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| 收件者 | 郵件地址的清單。 |
| 主旨 | 郵件的主旨。 |
| 附件 | 附件目前不支援，因此這將會永遠獲得值為 [無]。 |

以下是範例回應電子郵件通知巨集指令具有臨界值。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

若要建立新的電子郵件動作的使用唯一動作識別碼的放置方式。  下列範例會建立電子郵件通知與臨界值的儲存的搜尋結果超過臨界值時，傳送郵件。

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

若要修改的電子郵件動作使用現有的巨集指令識別碼的放置方式。  要求的主體必須包含 etag 的動作。

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>修復動作
補救措施啟動 Azure 自動化嘗試修正問題的通知可由 runbook。  您必須建立的補救動作中使用 runbook webhook，，然後指定 WebhookUri] 屬性中的 [URI。  當您建立使用 OMS 主控台這個動作時，runbook 會自動建立新的 webhook。

補救措施包含下列表格中的內容。

| 屬性 | 描述 |
|:--|:--|
| RunbookName | Runbook 的名稱。 這必須符合 OMS 工作區中自動化方案中設定的自動化帳戶中的已發佈的 runbook。 |
| WebhookUri | Webhook URI。
| 到期 | 到期日期和時間的 webhook。  如果 webhook 沒有到期日，這可能是未來任何有效的日期。 |

以下是範例回應補救臨界值的動作。

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

若要建立新的補救動作的使用唯一動作識別碼的放置方式。  下列範例會建立補救與臨界值，runbook 啟動時已儲存的搜尋結果超過臨界值。

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

若要修改的補救動作使用現有的巨集指令識別碼放入方法。  要求的主體必須包含 etag 的動作。

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>範例
下列是完整的範例，若要建立新的電子郵件通知。  這樣會建立新的排程，以及包含臨界值和電子郵件的動作。

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Webhook 動作
Webhook 動作啟動程序呼叫 URL，您也可以提供要傳送的內容。  它們是補救動作類似，但它們是為了 webhooks 可能叫用 Azure 自動化 runbooks 以外的程序。  他們也會提供傳遞給遠端程序提供內容的其他選項。

Webhook 動作沒有閥值，但改為應該新增至排程，其具有臨界值的通知動作。  您可以新增的所有時執行臨界值符合多個 Webhook 動作。

Webhook 動作包括下列表格中的內容。

| 屬性 | 描述 |
|:--|:--|
| WebhookUri | 郵件的主旨。 |
| CustomPayload | 傳送到 webhook 的自訂內容。  格式而定 webhook 預期的人員。 |

以下是範例回應 webhook 巨集指令和具有臨界值的相關提醒動作。

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>建立或編輯 webhook 動作
若要建立新的 webhook 動作的使用動作唯一識別碼的放置方式。  下列範例會建立 Webhook 動作和通知的動作與閥值，好讓的已儲存的搜尋結果超過閾值時，將會觸發 webhook。

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

若要修改的工作 webhook 動作使用現有的巨集指令識別碼放入方法。  要求的主體必須包含 etag 的動作。

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>後續步驟

- 您可以使用 [[執行記錄搜尋 REST API](log-analytics-log-search-api.md)中記錄分析。
