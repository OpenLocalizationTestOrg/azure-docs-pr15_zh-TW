<properties
    pageTitle="記錄檔分析登搜尋 REST API |Microsoft Azure"
    description="本指南可提供基本教學課程說明如何使用記錄分析搜尋 REST API 作業管理套件 (OMS) 中，並提供範例說明如何使用的命令。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="log-analytics-log-search-rest-api"></a>記錄檔分析記錄搜尋 REST API

本指南可提供基本教學課程說明如何使用記錄分析搜尋 REST API 作業管理套件 (OMS) 中，並提供範例說明如何使用的命令。 本文中的範例部分參考操作的深入見解的是舊版的記錄檔分析的名稱。

## <a name="overview-of-the-log-search-rest-api"></a>記錄檔搜尋 REST API 的概觀

記錄檔分析搜尋 REST API RESTful，而可透過 Azure 資源管理員 API 加以存取。 在這份文件中您會發現範例 API 透過[ARMClient](https://github.com/projectkudu/ARMClient)，可簡化叫用 Azure 資源管理員 API 開啟來源命令列工具存取的位置。 使用 ARMClient 及 PowerShell 是很多選項] 以存取記錄分析搜尋 API。 另一個選項是使用 PowerShell 的 Azure 模組 OperationalInsights 包括適用於存取搜尋指令程式。 使用這些工具，您可以利用 RESTful Azure 資源管理員 API，以撥打 OMS 工作區，並執行中的 [搜尋] 命令。 API 會輸出 JSON 格式，您的搜尋結果，可讓您以程式設計方式使用許多不同方式的搜尋結果。

Azure 資源管理員可透過[.net 的文件庫](https://msdn.microsoft.com/library/azure/dn910477.aspx)，以及[REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx)。 檢閱若要深入瞭解連結的網頁。

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>基本記錄分析搜尋 REST API 教學課程

### <a name="to-use-the-arm-client"></a>若要使用 ARM 用戶端

1. 安裝[Chocolatey](https://chocolatey.org/)，也就是在 Windows 版開啟來源套件管理員。 開啟命令提示字元視窗以系統管理員身分，然後執行下列命令︰

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. 安裝 ARMClient 藉由執行下列命令︰

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>若要執行簡單的搜尋使用 ARMClient

1. 登入您的 Microsoft 或 OrgID 帳戶︰

    ```
    armclient login
    ```

    登入成功列出所有訂閱繫結至指定的帳戶︰

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. 取得作業管理套件工作區︰

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    成功取得通話想輸出繫結至訂閱的所有工作區︰

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. 建立您的搜尋變數︰

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. 使用新的搜尋變數搜尋︰

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>登入分析搜尋 REST API 參照的範例
下列範例會告訴您如何使用搜尋 API。

### <a name="search---actionread"></a>搜尋-動作/已讀取

**範例 Url:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**要求︰**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
下表描述可供使用的內容。

|**屬性**|**描述**|
|---|---|
|頁首|要傳回的結果數目上限。|
|醒目提示|包含前和文章的參數，通常是用來醒目提示相符的欄位|
|前|指定的字串比對欄位的開頭。|
|文章|加入您相符的欄位指定的字串。|
|查詢|搜尋所用的查詢來收集及傳回的結果。|
|開始|您想要尋找的結果的時間範圍的開頭。|
|結束|您想要尋找的結果的 [時間] 視窗的結尾。|


**回應︰**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>搜尋 / {識別碼}-動作/已讀取

**要求儲存搜尋的內容︰**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] 如果搜尋傳回狀態為 「 已擱置 」，然後投票，以更新的結果可以透過完成此 API。 後 6 的最小值] 的搜尋結果會從快取卸除並將傳回 HTTP 消失。 如果初始搜尋要求立即傳回 「 成功 」 的狀態，它不會新增至快取造成此 API 傳回 HTTP 消失查詢。 HTTP 200 結果的內容就會在初始搜尋要求只使用更新的值相同的格式。

### <a name="saved-searches---rest-only"></a>儲存的搜尋-僅限其餘

**要求已儲存的搜尋的清單︰**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

支援的方法︰ GET 放置刪除

支援集合方法︰ 取得

下表描述可供使用的內容。

|屬性|描述|
|---|---|
|識別碼|唯一識別碼。|
|Etag|**修補程式所需**。 更新伺服器上每個寫入。 值必須是目前儲存的值等於或 ' *' 更新。 為舊版無效值傳回 409。|
|properties.query|**所需**。 搜尋查詢。|
|properties.displayName|**所需**。 使用者定義的顯示查詢的名稱。 如果化為 Azure 的資源，這就是標籤。|
|properties.category|**所需**。 查詢的 [使用者定義] 類別。 如果模型當作 Azure 資源這是標籤。|

>[AZURE.NOTE] 記錄檔分析搜尋 API 目前傳回使用者建立並儲存的搜尋時輪詢儲存工作區中的搜尋。 API 不會傳回已儲存的搜尋解決方案提供這一次。 此功能會新增稍後的日期。

### <a name="create-saved-searches"></a>建立已儲存的搜尋

**要求︰**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>刪除儲存搜尋

**要求︰**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>更新已儲存的搜尋

 **要求︰**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>中繼資料-僅限 JSON

以下是可以用來查看您的工作區中收集的資料的所有記錄類型的欄位。 例如，如果您想知道事件類型是否具有名為電腦的欄位，然後這是查詢，並確認其中一個方法。

**欄位的要求︰**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**回應︰**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

下表描述可供使用的內容。

|**屬性**|**描述**|
|---|---|
|名稱|欄位名稱。|
|顯示名稱|顯示欄位的名稱。|
|類型|欄位值的類型。|
|facetable|目前的 「 索引 」 的組合 ' 儲存 」 和 「 多面向' 屬性。|
|顯示|目前的 [顯示] 屬性。 如果是在搜尋中顯示的欄位，則為 true。|
|ownerType|減少屬於 onboarded IP 的類型。|


## <a name="optional-parameters"></a>選用的參數
下列資訊說明可用的選擇性參數。

### <a name="highlighting"></a>醒目提示

「 醒目提示 [這是選用的參數，您可能會要求搜尋子系統使用其回應中包含標記的一組。

這些標記指出開始和結束醒目提示的文字，符合您的搜尋查詢中提供的條款。
您可能會指定要使用搜尋來包裝醒目提示的字詞的開始和結束標記。

**搜尋查詢範例**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**範例結果︰**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

請注意上述結果，包含已加上並附加的錯誤訊息。

## <a name="computer-groups"></a>電腦群組

電腦群組是特殊已儲存的搜尋會傳回一組的電腦。  若要限制搜尋結果] 群組中的電腦，您可以在其他查詢中使用電腦群組。  電腦群組被當作已儲存的搜尋與群組標記有電腦的值。

以下是範例回應電腦群組。

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>擷取電腦群組

使用群組識別碼的取得方式來擷取電腦群組。

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>建立或更新電腦群組

使用放入方法以單一儲存搜尋 ID 若要建立新的電腦群組。 如果您使用現有的電腦群組識別碼，然後將修改一個。 當您在 OMS 主控台建立電腦群組時，識別碼會建立的群組及名稱。

群組定義所用的查詢必須傳回一組正常群組的電腦。  建議您結束查詢的*|不同的電腦*確保傳回正確的資料。

已儲存的搜尋的定義必須包含值為搜尋電腦會被歸類為電腦群組名稱] 群組中的標籤。

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>刪除電腦群組

若要刪除的電腦群組使用群組識別碼刪除方法。

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>後續步驟

- 瞭解[記錄搜尋](log-analytics-log-searches.md)建置查詢準則中使用自訂欄位。
