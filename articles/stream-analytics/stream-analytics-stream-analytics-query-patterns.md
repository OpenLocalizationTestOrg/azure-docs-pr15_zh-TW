<properties
    pageTitle="查詢的分析資料流中的一般使用模式範例 |Microsoft Azure"
    description="常見的 Azure 資料流分析查詢模式 "
    keywords="查詢範例"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>查詢一般資料流分析使用模式的範例

## <a name="introduction"></a>簡介

以類似的 SQL 查詢語言，[串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)指南中表示 Azure 資料流分析中的查詢。  本文概述幾個常見的查詢模式真實案例為基礎的解決方案。  它會進行中的工作，並會繼續以新的模式持續更新。

## <a name="query-example-data-type-conversions"></a>查詢範例︰ 資料類型轉換
**描述**︰ 輸入資料流中定義的內容類型。
例如，輸入資料流即將汽車寬度為字串，必須轉換成 INT 執行加總。

**輸入**︰

| 進行 | 時間 | 粗細 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | 「 2000 」 |

**成果**︰

| 進行 | 粗細 |
| --- | --- |
| Honda | 3000 |

**解決方案**︰

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**說明**︰ 使用 [寬度] 欄位的轉換陳述式，若要指定其類型 (請參閱支援的資料類型的清單[以下](https://msdn.microsoft.com/library/azure/dn835065.aspx))。


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>查詢範例︰ 使用類似/不想執行模式比對
**描述**︰ 檢查事件中的欄位值符合特定模式的範例中，傳回授權樣版的開頭，而且結尾 9

**輸入**︰

| 進行 | LicensePlate | 時間 |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| 豐田 | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**成果**︰

| 進行 | LicensePlate | 時間 |
| --- | --- | --- |
| 豐田 | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**解決方案**︰

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**說明**︰ 檢查 LicensePlate 欄位值的第一句然後有任何零或多個字元的字串，以及以 9 @ 使用 LIKE 陳述式。 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>查詢範例︰ 指定不同的情況下/值 （區分大小陳述式） 的邏輯
**描述**︰ 提供不同的計算，根據一些準則的欄位。
例如，提供多少汽車傳遞相同的讓特殊的大小寫的 1 的字串描述。

**輸入**︰

| 進行 | 時間 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| 豐田 | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 2015-01-01T00:00:03.0000000Z |

**成果**︰

| CarsPassed | 時間 |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**解決方案**︰

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**說明**︰ 案例子句可以讓我們提供不同的計算根據一些準則 （在本例汽車彙總的視窗中的計數）。

## <a name="query-example-send-data-to-multiple-outputs"></a>查詢範例︰ 傳送資料至多個輸出
**描述**︰ 將資料從單一工作傳送給多個輸出目標。
例如，分析資料的閥值為基礎的通知及封存所有事件至 blob 儲存體

**輸入**︰

| 進行 | 時間 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 2015-01-01T00:00:01.0000000Z |
| 豐田 | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| 進行 | 時間 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 2015-01-01T00:00:01.0000000Z |
| 豐田 | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| 進行 | 時間 | 字數統計 |
| --- | --- | --- |
| 豐田 | 2015-01-01T00:00:10.0000000Z | 3 |

**解決方案**︰

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**說明**︰ 將子句會告訴串流分析其資料寫此陳述式的輸出。
第一個查詢已通過我們接收到我們名為 ArchiveOutput 輸出資料。
第二個查詢會一些簡單的彙總篩選並將結果傳送到下游警示系統。
*注意*︰ 您也可以重複的結果 Cte （亦即使用陳述式） 使用多個輸出陳述式中 – 此已開啟 [輸入來源讀者較少的好處。
例如， 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>查詢範例︰ 計算唯一值
**描述**︰ 計算出現在 [時間範圍內的資料流中的唯一的欄位值的數目。
例如的汽車多少唯一讓通過免付費電話攤位 2 的第二個視窗中？

**輸入**︰

| 進行 | 時間 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 2015-01-01T00:00:01.0000000Z |
| 豐田 | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 2015-01-01T00:00:03.0000000Z |

**輸出︰**

| 字數統計 | 時間 |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**解決方案︰**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**說明︰**我們可使用其計算唯一會移到視窗上方初始彙總。
然後，我們會執行彙總的數會讓我們有 – 提供所有唯一值在視窗中的有相同的時間戳記，然後需要不彙總的第一個步驟的 2 的視窗最小第二個 [彙總] 視窗。

## <a name="query-example-determine-if-a-value-has-changed"></a>查詢範例︰ 判斷是否有變更的值#
**描述**︰ 查看先前的值來判斷是否與目前的值，例如是在免付費電話旅途中相同進行為目前的汽車先前的汽車不同？

**輸入**︰

| 進行 | 時間 |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| 豐田 | 2015-01-01T00:00:02.0000000Z |

**成果**︰

| 進行 | 時間 |
| --- | --- |
| 豐田 | 2015-01-01T00:00:02.0000000Z |

**解決方案**︰

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**說明**︰ 使用延隔時間，輸入檢視資料流回一個事件，且取得讓值。 相較於目前的事件設然後輸出事件，如果有不同。

## <a name="query-example-find-first-event-in-a-window"></a>查詢範例︰ 尋找在視窗中的第一個事件
**描述**︰ 尋找每 10 分鐘的時間間隔中的第一個車嗎？

**輸入**︰

| LicensePlate | 進行 | 時間 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | 二十 | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | 豐田 | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | 豐田 | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**成果**︰

| LicensePlate | 進行 | 時間 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**解決方案**︰

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

現在讓我們來變更特定問題，並尋找第一個汽車在每 10 分鐘的時間間隔。

| LicensePlate | 進行 | 時間 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | 二十 | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | 豐田 | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**解決方案**︰

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>查詢範例︰ 尋找在視窗中的最後一個事件
**描述**︰ 尋找每 10 分鐘的時間間隔中的最後一個車。

**輸入**︰

| LicensePlate | 進行 | 時間 |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | 二十 | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | 豐田 | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | 豐田 | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**成果**︰

| LicensePlate | 進行 | 時間 |
| --- | --- | --- |
| VFE 1616 | 豐田 | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**解決方案**︰

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**說明**︰ 在查詢中有兩個步驟 – 第一個 10 分鐘 windows 中尋找最新的時間戳記。 第二步聯結與原始資料流，若要尋找符合在每一個視窗中的最後一個時間戳記的事件的第一個查詢的結果。 

## <a name="query-example-detect-the-absence-of-events"></a>查詢範例︰ 偵測不存在的事件
**描述**︰ 核取 [串流有沒有符合特定準則的值。
例如，從相同讓 2 個連續的汽車輸入免付費電話道路 90 秒內嗎？

**輸入**︰

| 進行 | LicensePlate | 時間 |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| 豐田 | 定義 987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**成果**︰

| 進行 | 時間 | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA 999 | ABC 123 | 2015-01-01T00:00:01.0000000Z |

**解決方案**︰

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**說明**︰ 使用延隔時間，輸入檢視資料流回一個事件，且取得讓值。 相較於目前的事件設然後輸出事件，如果他們是相同，並使用延隔時間取得先前的汽車相關資料。

## <a name="query-example-detect-duration-between-events"></a>查詢範例︰ 偵測事件之間的工期
**描述**︰ 尋找指定事件的工期。 例如，給定的 web 之類判斷某項功能所花費的時間。

**輸入**︰  
  
| 使用者 | 功能 | 事件 | 時間 |
| --- | --- | --- | --- |
| user@location.com | RightMenu | 開始 | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | 結束 | 2015-01-01T00:00:08.0000000Z |
  
**成果**︰  
  
| 使用者 | 功能 | 持續時間 |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**解決方案**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**說明**︰ 使用的最後一個函數來擷取時事件類型的 [開始] 的最後一個時間值。 請注意表示結果的計算每個唯一的使用者，最後一個函數使用分割區的 [使用者]。  查詢會有 1 小時最大臨界值的 「 開始 」 和 「 停止 」 事件的時間差異，但可設定為需要 (限制 DURATION(hour, 1)。

## <a name="query-example-detect-duration-of-a-condition"></a>查詢範例︰ 偵測條件的持續時間
**描述**︰ 找的出多久條件發生。
例如，假設的錯誤而導致所有汽車有不正確的寬度 （上方 20000 磅）-我們想要計算錯誤的工期。

**輸入**︰

| 進行 | 時間 | 粗細 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| 豐田 | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| 豐田 | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| 豐田 | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| 豐田 | 2015-01-01T00:00:08.0000000Z | 2000 |

**成果**︰

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**解決方案**︰

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**說明**︰ 使用延隔時間 24 小時檢視輸入資料流，尋找 StartFault 和 StopFault 位置合併的執行個體 weight < 20000。

## <a name="query-example-fill-missing-values"></a>查詢範例︰ 填入遺漏值
**描述**︰ 的事件遺漏值的資料流，產生的事件定期間隔的資料流。
例如，產生的事件會報告最近指南過的資料點每 5 秒。

**輸入**︰

| t | 值 |
|--------------------------|-------|
| 「 2014年-01-01T06:01:00 」 | 1 |
| 「 2014年-01-01T06:01:05 」 | 2 |
| 「 2014年-01-01T06:01:10 」 | 3 |
| 「 2014年-01-01T06:01:15 」 | 4 |
| 「 2014年-01-01T06:01:30 」 | 5 |
| 「 2014年-01-01T06:01:35 」 | 6 |

**輸出 （前 10 個資料列）**︰

| windowend | lastevent.t | lastevent.value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**解決方案**︰

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**說明**︰ 此查詢會產生每個 5 的第二個事件，並且輸出收到前的最後一個事件。 [跳視窗](https://msdn.microsoft.com/library/dn835041.aspx "跳視窗-Azure 資料流分析")工期會決定正值後，若要尋找最新的事件 （在此範例中 300 秒） 看起來的查詢。


## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
