<properties
    pageTitle="記錄檔分析搜尋參考 |Microsoft Azure"
    description="記錄檔分析搜尋參考說明搜尋語言，並提供的一般查詢語法選項，您可以使用時機搜尋資料，並篩選運算式以協助您縮小搜尋範圍。"
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
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="log-analytics-search-reference"></a>登入分析搜尋參考

瞭解搜尋語言下列參考 > 一節說明您可以使用時機的一般查詢語法選項搜尋資料，並篩選運算式以協助您縮小搜尋範圍。 同時也會說明您可以採取行動擷取的資料使用的命令。

您可以瞭解搜尋及協助您 dill facet 中傳回的欄位-成類似類別的[[搜尋] 欄位和多面向參照一節](#search-field-and-facet-reference)中的資料。

## <a name="general-query-syntax"></a>一般查詢語法

語法︰

```
filterExpression | command1 | command2 …
```

篩選運算式 (`filterExpression`) 定義查詢條件的 [位置]。 命令套用至查詢所傳回的結果。 多個命令必須以直立線符號字元 (|) 分隔。

### <a name="general-syntax-examples"></a>一般的語法範例

範例︰

```
system
```

此查詢會傳回結果包含文字的 「 系統 」 中任何具有完整的文字或字詞搜尋已編製索引的欄位。

>[AZURE.NOTE] 並非所有的欄位已確實建立索引如此一來，但通常是最常見的文字欄位 （例如描述和名稱）。

```
system error
```

此查詢會傳回包含文字*系統*和*錯誤*的結果。

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

此查詢會傳回包含文字*系統*和*錯誤*的結果。 然後會排序結果*ManagementGroupName*中的欄位 （遞增順序），然後*TimeGenerated* （以遞減順序）。 需要先 10 的結果。

>[AZURE.IMPORTANT] 所有的欄位名稱及字串] 和 [文字欄位的值會區分大小寫。

## <a name="filter-expression"></a>篩選運算式

下面子區段說明篩選運算式。

### <a name="string-literals"></a>字串串連

常值字串是無法剖析器識別為關鍵字或預先定義的資料類型 （例如數字或日期） 的任何字串。

範例︰

```
These all are string literals
```

此查詢會搜尋結果包含所有的五個單字的項目。 若要執行複雜的字串搜尋，將字串文字引號括住，例如︰

```
" Windows Server"
```

這只會傳回 「 Windows server 「 完全相符的結果。

### <a name="numbers"></a>數字

剖析器支援數字欄位的小數位數的整數和浮點數數字的語法。

範例︰

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="datetime"></a>日期/時間

每一份系統中的資料有*TimeGenerated*屬性，這表示原始的日期和時間的記錄。 此外，某些類型的資料可以有多個日期/時間欄位 (例如，*上次修改*)。

時刻表圖表/時間選擇器記錄分析中的顯示的結果的通訊群組，一段時間 （根據目前的查詢執行），根據*TimeGenerated*欄位。 日期/時間欄位具有特定的字串格式，可以在查詢中用來限制特定時間範圍內的查詢。 您也可以使用語法參照相對之間的時間間隔 （例如，「 3 天之內和 2 個小時之內 」）。

語法︰

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


範例︰

```
TimeGenerated:2013-10-01T12:20
```

[上一個] 命令只會傳回記錄*TimeGenerated*值為完全 12:20 在 2013 年 10 月 1 日。 不太，也會提供任何結果，但您瞭解概念。

剖析器也現在支援 mnemonics 日期/時間值。


同樣地，不太，這樣就會產生任何結果，因為資料不會使時就能在系統。

這些範例是使用相對和絕對日期的建置組塊。 三個小節，我們會說明如何使用這些範例使用相對日期範圍的更多進階篩選器中。

### <a name="datetime-math"></a>日期/時間數學

您可以使用 [日期/時間數學運算子位移，或使用簡單的日期/時間計算四捨五入日期/時間值。

語法︰

```
datetime/unit
```

```
datetime[+|-]count unit
```

|運算子|描述|
|---|---|
|/|將日期/時間四捨五入到指定的單位。 範例︰ 現在 / 日將目前為目前日期的午夜日期/時間。|
|+ 或-|位移日期/時間，以指定的單位。 範例︰ 現在 + 1 小時位移目前的日期/時間，以一小時 ahead.2013-10-01T12:00-10 天位移傳回以 10 天的日期值。|



您可以鏈結日期/時間數學運算子在一起，例如︰

```
NOW+1HOUR-10MONTHS/MINUTE
```

下表列出支援的日期/時間單位。

日期/時間單位|描述
---|---
年的年份|將目前的年份，或依指定的年數的位移。
月、 月數|將目前的月份，或依所指定月份數的位移。
天、，日期|將無條件捨位至目前日期的月或位移以指定的天數。
小時、 小時|將目前的小時，或以指定的時間位移。
分鐘，通話分鐘方案|將捨入至目前這一分鐘或依指定的分鐘數的位移。
第二，秒|第二個，將目前或位移所指定的秒數。
毫秒，毫秒 MILLI MILLIS|將目前的毫秒數，或依指定的毫秒數的位移。


### <a name="field-facets"></a>功能變數 facet

藉由使用功能變數 facet，您可以指定特定欄位和其確切的值，而非整個索引的各種規定撰寫 「 免費文字 」 查詢的搜尋條件。 我們已在前一段落中的多個範例中使用此語法。 在這裡，我們會提供更複雜的範例。

**語法**

```
field:value
```

```
field=value
```

**描述**

搜尋特定值的欄位。 值可以是字串常值、 數值或日期/時間。

範例︰

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**語法**

*功能變數 > 值*

*功能變數 < 值*

*功能變數 > = 值*

*功能變數 < = 值*

*功能變數 ！ = 值*

**描述**

提供的比較。

範例︰

```
TimeGenerated>NOW+2HOURS
```

**語法**

```
field:[from..to]
```

**描述**

提供範圍 faceting。

範例︰

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="logical-operators"></a>邏輯運算子

查詢語言支援的邏輯運算子 （*與*、*或*和*不*） 和其 C 樣式別名 (*&&*， *||*，及*！*) 分別。 您可以將這些運算子使用括號括住。

範例︰

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

您可以略過的最上層的篩選引數的邏輯運算子。 在此情況下，則假設與運算子。


篩選運算式|等於
---|---
系統錯誤|系統與錯誤
系統 「 Windows 伺服器 」 或嚴重性︰ 1|系統和 (「 Windows 伺服器 」 或嚴重性︰ 1)

### <a name="wildcarding"></a>萬用字元

查詢語言支援使用 (*\*) 來代表查詢中的值的一個或多個字元的字元。

範例︰

 尋找 「 SQL 」 的所有電腦的名稱，例如 「 雷蒙市 SQL 」。

```
Type=Event Computer=*SQL*
```

>[AZURE.NOTE] 無法在引號內今天使用萬用字元。 郵件 =`"*This text*"`會考慮 (\*) 用來做為常值 (\*) 字元。
## <a name="commands"></a>命令

命令套用至查詢所傳回的結果。 使用管道字元 (|) 用於擷取結果中的命令。 多個命令來分隔管道字元。

>[AZURE.NOTE] 大寫或小寫，與不同的欄位名稱與資料可以撰寫命令名稱。

### <a name="sort"></a>排序

語法︰

    sort field1 asc|desc, field2 asc|desc, …

排序特定欄位的結果。 遞增/遞減前置詞為選用步驟。 如果省略，則假設*asc*排序順序。 如果查詢不明確地使用 [*排序*] 命令，排序**TimeGenerated**遞減的預設行為，而第一次它會永遠傳回最新的結果。

### <a name="toplimit"></a>頂端/限制

語法︰

    top number


    limit number
限制的前 n 個結果的回應。

範例︰

    Type:Alert errors detected | top 10

會傳回前 10 個相符的結果。

### <a name="skip"></a>略過

語法︰

    skip number

略過列出的結果數目。

範例︰

    Type:Alert errors detected | top 10 | skip 200

會傳回結果 200 開始的上方 10 的相符結果。

### <a name="select"></a>選取

語法︰

    select field1, field2, ...

結果限制為您選擇的欄位。

範例︰

    Type:Alert errors detected | select Name, Severity

限制傳回的結果要的欄位*名稱*] 和 [*重要性*。

### <a name="measure"></a>量值

[*量值*] 命令會用於未經處理的搜尋結果的套用統計函數。 這是非常實用，以取得資料的*分組*檢視。 當您使用 [*量值*] 命令時，記錄分析搜尋會顯示彙總結果的資料表。

語法︰

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



彙總結果*groupField* ，並使用*aggregatedField*計算彙總的量值的值。


|量值統計函數|描述|
|---|---|
|*aggregateFunction*|彙總函式 （不區分大小寫） 的名稱。 支援下列彙總函式︰ 計算最大可 MIN 加總平均標準差 COUNTDISTINCT 百分位數 # # 或 Percent # # (# # 1 到 99 間的任何數字)|
|*aggregatedField*|彙總欄位。 此欄位並非，計算彙總函數，但必須與現有的數字欄位的加總、 MAX、 MIN、 平均標準差，或百分位數 # # 或 Percent # # (# # 1 到 99 間的任何數字)。 AggregatedField 也可以是任何支援的擴充功能。|
|*fieldAlias*|（選擇性） 別名的計算彙總的值。 如果未指定的欄位名稱會 AggregatedValue。|
|*groupField*|結果集欄位的名稱會由分組。|
|*間隔*|格式的時間間隔︰**nnnNAME**位置︰ nnn 是正整數數目。 **名稱**是間隔名稱。 支援的間隔名稱包含 （區分大小寫）: [S] 毫秒第二個 [S] MINUTE [S] 小時 [S] 天 [S] 月 [S] 年 [S]|


間隔] 選項僅用於群組日期/時間] 欄位 （例如*TimeGenerated*和*TimeCreated*）。 目前不強制執行服務，但不會傳送到後端的日期/時間] 欄位會導致執行階段錯誤。 實作結構描述驗證後，服務 API 拒絕間隔彙總的使用不日期/時間欄位的查詢。 目前的*量值*實作支援彙總函式的分組區間。

如果 BY 子句會被省略了，但是 （作為第二個的語法） 指定時間間隔， *TimeGenerated*欄位也會依預設會引用本身。

範例︰

**範例 1**

    Type:Alert | measure count() as Count by ObjectId

*說明*

分組的*ObjectID*通知，並計算的每個群組的通知。 *計算*欄位 （別名） 會傳回彙的總值。

**範例 2**

    Type:Alert | measure count() interval 1HOUR

*說明*

群組通知 1 小時的時間間隔，使用*TimeGenerated* ] 欄位中，並傳回警示數量，在每個間隔。

**範例 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*說明*

與相同前一個範例中，但彙總的欄位別名 (*AlertsPerHour*)。

**範例 4**

    * |測量 TimeCreated 間隔 5DAYS count （）

*說明*

使用*TimeCreated* ] 欄位中，將結果分組 5 天的時間間隔，並傳回的結果數目中每個間隔。

**範例 5**

    Type:Alert | measure max(Severity) by WorkflowName

*說明*

群組通知工作負載的名稱，並傳回每一個工作流程的最大的警示嚴重性值。

**範例 6**

    Type:Alert | measure min(Severity) by WorkflowName

*說明*

與相同前一個範例中，但*Min*彙總函數。

**範例 7**

    Type:Perf | measure avg(CounterValue) by Computer

*說明*

分組電腦效能，並計算平均值 （平均）。

**範例 8**

    Type:Perf | measure sum(CounterValue) by Computer

*說明*

相同前一個範例中，但使用*加總*。

**範例 9**

    Type:Perf | measure stddev(CounterValue) by Computer

*說明*

相同前一個範例中，但會使用*標準差*。

**範例 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

*說明*

相同前一個範例中，但使用*PERCENTILE70*。

**範例 11**

    Type:Perf | measure pct70(CounterValue) by Computer

*說明*

相同前一個範例中，但使用*PCT70*。 請注意， *Percent # #* *百分位數 # #*函數的別名。

**範例 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

*說明*

第一次群組電腦的效能，然後 CounterName，計算平均值 （平均）。

**範例 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*說明*

取得警示的最大的數字的上方五個工作流程。

**範例 14**

    * |依類型的量值 countdistinct(Computer)

*說明*

報表每一種類型的唯一電腦的個數。

**範例 15**

    * |測量 countdistinct(Computer) 間隔 1 小時

*說明*

會計算每小時的報告的唯一電腦數目。

**範例 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*說明*

群組 %的電腦的處理器時間，傳回平均每 1 小時。

**範例 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*說明*

群組 W3CIISLog 方法，並傳回最大值的每個 5 分鐘。

**範例 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*說明*

群組 %處理器時間的電腦，並傳回的最小值、 平均值、 75 個百分位數和最大值的每個 1 小時。

**範例 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

*說明*

群組 %處理器時間第一次的電腦，再執行個體名稱，並傳回的最小值、 平均值、 75 個百分位數和最大值的每個 1 小時

**範例 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

*說明*

會計算每分鐘的每個磁碟撰寫您電腦上的磁碟的最大值

### <a name="where"></a>位置

語法︰

```
**where** AggregatedValue>20
```

僅能在*量值*命令後進一步篩選*測量*彙總函式具有所產生的彙總的結果。

範例︰

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### <a name="in"></a>在

語法︰

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

描述︰ 這個語法可讓您建立一個彙總及外觀的事件，這些值的另一個外部 （主要） 搜尋到的彙總摘要值的清單。 括號括號內搜尋，並提供其結果為外部使用 IN 運算子的搜尋欄位的可能值執行此動作。

內部查詢範例︰ 使用下列的彙總查詢的 [*目前遺漏電腦的安全性更新*︰

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

尋找*目前遺漏的安全性更新的電腦上所有的 Windows 事件*最終查詢就會類似︰

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="dedup"></a>Dedup

**語法**

    Dedup FieldName

**描述**傳回發現指定欄位的每個唯一值的第一份文件。

**範例**

    Type=Event | sort TimeGenerated DESC | Dedup EventID

上述範例會傳回 EventID 每一個事件 （最新的因為我們 TimeGenerated 上使用 [遞減]）


### <a name="extend"></a>延伸

**描述**可讓您在查詢中建立執行階段欄位。 您也可以使用後將延伸量值] 命令，如果您想要執行彙總。

**範例 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
顯示 SQL 評估建議加權的建議分數

**範例 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
顯示 [計數器值，而不是位元組 Kb

**範例 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
經過的所有結果都介於 0 到 100，不按比例縮放 WireData TotalBytes 的值。

**範例 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
標記為 [高的效能計數器值小於 50 %las 低和其他人

**範例 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
會計算每分鐘的每個磁碟撰寫您電腦上的磁碟的最大值

**支援的函數**


| 函數 | 描述 | 語法範例 |
|---------|---------|---------|
| abs | 傳回指定的值或函數的絕對值。 | `abs(x)` <br> `abs(-5)` |
| acos | 傳回餘弦值或函數 | `acos(x)` |
| 與 | 只有在所有運算元的評估為 true，則傳回 true 的值。 | `and(not(exists(popularity)),exists(price))` |
| asin | 傳回的值或函數的弧線正弦值 | `asin(x)` |
| atan | 傳回的值或函數的弧形正切值 | `atan(x)` |
| atan2 |  傳回所產生的矩形座標轉換角度 x、 極座標 y | `atan2(x,y)` |
| cbrt | Cube 根 |  `cbrt(x)` |
| ceil | 進位成整數 | `ceil(x)`  <br> `ceil(5.6)`-傳回 6 |
| cos | 傳回的角度的餘弦值 | `cos(x)` |
| cosh | 傳回某個角度的雙曲餘弦值 | `cosh(x)` |
| 定義 | 定義是簡短的預設值。 傳回值的 「 欄位 」，或如果欄位不存在，會傳回指定的預設值，並產生的第一個值位置︰ `exists()==true`。 | `def(rating,5)`-此 def() 函數傳回評等，或如果文件、 指定沒有評等會傳回 5 <br> `def(myfield, 1.0)`-等於`if(exists(myfield),myfield,1.0)` |
| 度 | 將弧度轉換成度數 |  `deg(x)` |
| div | `div(x,y)`會將 y 的 x。 | `div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| 分散式 | 傳回之間的距離兩個向量，（點），在 n 維度的空間。 取得 power，加上兩個或多個，ValueSource 執行個體，並計算兩個向量之間的距離。 每個 ValueSource 必須是數字。 必須偶數 ValueSource 傳入的執行個體，並方法假設的前半代表的第一個向量，並在半代表第二個向量。  | `dist(2, x, y, 0, 0)`-計算 Euclidean 距離 between,(0,0) 和 x （y） 每份文件。 <br> `dist(1, x, y, 0, 0)`-計算 Manhattan (taxicab)，(0，0) 之間的距離和 (x，y) 每份文件。 <br> `dist(2,,x,y,z,0,0,0)`-Euclidean (0,0,0) 之間的距離和 x、 y (z） 每份文件。<br>`dist(1,x,y,z,e,f,g)`-Manhattan 間的距離 x、 y (z） 和 （e f、 g），其中每個字母是欄位名稱。 |
| 存在 | 有會傳回 TRUE，如果有任何成員的欄位。 | `exists(author)`-會傳回 TRUE 任何文件中的 「 作者 」 欄位的值。<br>`exists(query(price:5.00))`-會傳回 TRUE 如果比對 「 價格 「，」 5.00 」。 |
| exp | 傳回歐拉數字乘冪 x | `exp(x)` |
| 會場 | 捨去 | `floor(x)`  <br> `floor(5.6)`-傳回 5 |
| hypo | 傳回 sqrt(sum(pow(x,2),pow(y,2))) 沒有中繼溢位或反向溢位 | `hypo(x,y)`  <br> ` |
| 如果 | 可讓查詢條件的函數。 在 [ `if(test,value1,value2)` -測試或指的是邏輯值或運算式會傳回邏輯值 （TRUE 或 FALSE）。  `value1`是測試結果為 TRUE 時，函數所傳回的值。 `value2`是測試結果為 FALSE 時，函數所傳回的值。 運算式可以是任何函數輸出，則為 true 的值，或甚至函數傳回的數值的大小寫值 0 會解譯為 false 或字串，在這種情況空字串會解譯為 false。 | `if(termfreq(cat,'electronics'),popularity,42)`-此函數會檢查每個文件以查看其包含字詞 「 電子 」 貓] 欄位中。 如果是這樣，則會傳回常用性欄位的值，否則 42 的值。 |
| 線性 | 實作`m*x+c`其中 m 及 c 是常數，而 x 是任意的函數。 這是相當於`sum(product(m,x),c)`，但稍微更有效率，為它實作單一函數。 | `linear(x,m,c) linear(x,2,4)`會傳回`2*x+4` |
| ln| 傳回指定函數的自然記錄檔 |  `ln(x)` |
| 記錄檔 | 傳回記錄指定函數的底數 10。 | `log(x)   log(sum(x,100))` |
| 地圖 | 地圖 x 輸入任何的函數至指定的目標落 min 及 max 所含的值。 引數的最小值和最大值必須是常數。 引數目標和預設可常數或函數。 如果未 x 的值落最小值與最大值之間，然後傳回 x 的值，或如果指定為第 5 個引數，會傳回的預設值。 |  `map(x,min,max,target) map(x,0,0,1)`-變更 0 到 1 的任何值。 這可以用來處理預設 0 值。<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`-0 與 1 到 100 之間的任何值和變更所有其他的值-1。<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`-0 與 100 x + 599，所有其他的值之間的任何值變更為字詞 'solr' 在文字] 欄位的頻率。 |
| 最大值 | 傳回的最大數值的多個巢狀的函數或常數，指定為引數︰ `max(x,y,...)`。 Max 函數也會有幫助的 「 bottoming 「 另一個函數，或在某些欄位指定常數。  使用`field(myfield,max)`語法，選取一個多重值欄位的最大值。  | `max(myfield,myotherfield,0)` |
| 最小值 | 傳回的最小的數值常數，指定為引數的多個巢狀函數︰ `min(x,y,...)`。 Min 函數也可以適用於使用常數函數提供 「 上限 」。 使用`field(myfield,min)`語法，選取一個多重值欄位的最小值。 | `min(myfield,myotherfield,0)` |
| mod | 計算函數 y 函數 x 的模數。 |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))`   |
| ms | 傳回引數之間的差異的毫秒數。 日期為相對於 Unix 或 POSIX 時間期間、 午夜 1970 年 1 月 1 日 UTC。 引數可以是索引的 TrieDateField 或根據常數的日期的日期計算的名稱，或現在。 `ms()`相當於`ms(NOW)`、 自期間的毫秒為單位的數字。 `ms(a)`自引數代表期間傳回毫秒的數。 `ms(a,b)`傳回的毫秒數 b 之前，這是`a - b`。| `ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| 不 | 邏輯相反換函數的值。 | `not(exists(author))`為 TRUE 時才`exists(author)`為 false。 |
| 或 | 邏輯分離。 | `or(value1,value2)`-如果 value1 或 value2 為真，則為 TRUE。 |
| pow | 會引發指定的底數之指定乘冪。 `pow(x,y)`會引發的 y 的 x。 |  `pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`-相同 sqrt。 |
| 產品 | 傳回或函數，以逗號分隔清單指定多個值的乘積。 `mul(...)`也可用於為別名此函數。 | `product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| 收件者 | 執行交互功能與`recip(x,m,a,b)`實作`a/(m*x+b)`位置 m a、 b 是常數，而 x 是複雜的任何函數。 當而 b 等於、 和 x > = 0，此函數具有放在 x 隨著的 1 的最大值。 增加的值和 b 分成一組結果呈現部分曲線的整個函式移動。 這些屬性可以將此設定時 x 提高較新的文件的理想函數`rord(datefield)`。 | `recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad> | 將度數轉換成弧度 | `rad(x)` |
| 移到 [列印| 四捨五入到最接近的整數 | `rint(x)`  <br> `rint(5.6)`-傳回 6 |
| sin | 傳回的角度的正弦值 | `sin(x)` |
| sinh | 傳回某個角度的雙曲正弦值 | `sinh(x)` |
| 縮放比例 | 縮放函數 x 的值，使其落在指定的 minTarget 及 maxTarget （含) 之間。 目前的實作穿過所有函數的值，以取得的最小值及最大值]，讓它可以選擇正確的小數位數。 目前的實作無法分辨時已刪除的文件或沒有值的文件。 使用這種情況下使用 0.0 的值。 這表示，如果值是以正常方式所有大於 0.0，其中一個仍然有可能會與 0.0 做為從對應的最小值。 在下列情況下，適當`map()`函數無法作為因應措施若要變更 0.0 值在真實的範圍，如下所示︰`scale(map(x,0,0,5),1,2)` | `scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`-確認，所有值都會 1 和 2 （含) 之間，請調整 x 的值。 |
| sqrt | 傳回指定的值或函數的平方根。 | `sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist | 計算兩個字串之間的距離。 使用 Lucene 拼字檢查程式 StringDistance 介面和支援所有的實作提供套件，以及可讓應用程式插入自己透過 Solr 的資源載入功能。 會帶 strdist `(string1, string2, distance measure)`。 距離量值的可能值為︰ <br>jw: Jaro Winkler<br>編輯︰ Levenstein] 或 [編輯的距離<br>ngram: NGramDistance，如果指定，可以選擇性地傳入 ngram 大小太。 預設值為 2。<br>FQN︰ 完整的類別名稱 StringDistance 介面的實作。 必須要有一個無引數。|`strdist("SOLR",id,edit)` |
| sub | 傳回 x-y 從`sub(x,y)`。 | `sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| 加總 | 傳回的值或多個函數，以逗號分隔清單中指定的總和。 `add(...)`可用於為別名此函數。 | `sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)`|
|termfreq | 傳回的字詞，會出現在文件的欄位中的次數。 | termfreq(text,'memory')|
| tan | 傳回某個角度的正切值 | `tan(x)` |
| tanh | 傳回某個角度的雙曲正切值 | `tanh(x)` |



## <a name="search-field-and-facet-reference"></a>搜尋欄位和多面向參照

當您使用記錄搜尋來尋找資料時，結果會顯示不同的欄位和 facet。 不過，您會看到資訊可能不會出現太清楚。 您可以使用下列資訊協助您瞭解結果。



|功能變數|搜尋類型|描述|
|---|---|---|
|TenantId|所有|用來分割資料|
|TimeGenerated|所有|用來驅動時間表，timeselectors （在搜尋中與其他的畫面中）。 它會表示的資料產生 （通常是在代理程式） 時。 時間表示 ISO 格式，以及永遠是 UTC。 若是根據現有儀器 （亦即事件記錄檔中） 的 「 類型 」 這通常是日誌項目/線條/記錄; 在即時針對透過管理組件，或是在雲端-所產生的其他類型的某些即建議/通知/updateagent/等，這是時收集資料的一些排序的設定，此新段或產生的建議/提醒的時間根據它。|
|EventID|事件|在 Windows 事件記錄檔中 EventID|
|事件記錄檔|事件|Windows 事件記錄所在的事件記錄檔|
|EventLevelName|事件|要徑 / 警告 / 資訊 / 成功|
|EventLevel|事件|要徑 / 警告的數值 / 資訊 / 成功 （EventLevelName 改為使用更容易/更易於閱讀的查詢）|
|SourceSystem|所有|資料來源 (而言服務-亦即 Operations Manager OMS 的 [附加] 模式 (= 產生在雲端的資料)，Azure 儲存體 （資料來自 WAD） 等等|
|物件|PerfHourly|Windows 效能的物件名稱|
|InstanceName|PerfHourly|Windows 效能計數器執行個體名稱|
|CounteName|PerfHourly|Windows 效能計數器名稱|
|ObjectDisplayName|PerfHourly，ConfigurationAlert，ConfigurationObject ConfigurationObjectProperty|顯示效能集合規則 Operations Manager 或發現操作深入資訊，或針對產生警示的物件中的目標的物件名稱|
|RootObjectName|PerfHourly，ConfigurationAlert，ConfigurationObject ConfigurationObjectProperty|顯示名稱的上層的父 (雙裝載關聯中︰ 也就是由 SqlInstance 裝載的 Windows 電腦的 SqlDatabase) 效能集合規則 Operations Manager 或發現操作深入資訊，或針對產生警示的物件中的目標的物件|
|電腦|大部分的類型|資料所屬的電腦名稱|
|裝置名稱|ProtectionStatus|電腦名稱資料屬於 （相同 '電腦 」）|
|DetectionId|ProtectionStatus| |
|ThreatStatusRank|ProtectionStatus|威脅狀態排名是數字的潛在威脅的狀態，表示和 HTTP 回應代碼類似，我們已離開間隔數字之間 （這也是為什麼沒有威脅為 150 但 100 或 0），讓我們已新增新的狀態的空間。 彙總套件威脅和保護狀態時，我們想要顯示的電腦已在選定的時間週期的表現最差狀態。 我們會使用數字，排列不同的狀態，讓我們來看記錄的最大的數字。|
|ThreatStatus|ProtectionStatus|描述 ThreatStatus，地圖 ThreatStatusRank 1:1|
|TypeofProtection|ProtectionStatus|在電腦中偵測到的反惡意程式碼產品︰ 無，Microsoft 惡意程式碼移除工具、 Forefront，等等|
|ScanDate|ProtectionStatus| |
|SourceHealthServiceId|ProtectionStatus RequiredUpdate|此電腦的代理程式的 HealthService 識別碼|
|HealthServiceId|大部分的類型|此電腦的代理程式的 HealthService 識別碼|
|ManagementGroupName|大部分的類型|管理群組名稱 Operations Manager 附加代理程式。否則，它將會 null/空白|
|Save|ConfigurationObject|類型 (如 Operations Manager management pack 「 類型 」 類別) 發現記錄分析組態評估這個物件|
|UpdateTitle|RequiredUpdate|找不到未安裝的更新|
|PublishDate|RequiredUpdate|當已更新發佈 Microsoft 更新？|
|伺服器|RequiredUpdate|電腦名稱資料屬於 （相同 '電腦 」）|
|產品|RequiredUpdate|適用於更新的產品|
|UpdateClassification|RequiredUpdate|類型的更新 （更新彙總套件、 服務套件等）|
|編號|RequiredUpdate|描述此最佳作法或更新的 KB 文章識別碼|
|WorkflowName|ConfigurationAlert|產生提醒的監視器的規則的名稱|
|重要性|ConfigurationAlert|提醒的重要性|
|優先順序|ConfigurationAlert|提醒的優先順序|
|IsMonitorAlert|ConfigurationAlert|此通知產生監視器 (true) 或 (false) 規則？|
|AlertParameters|ConfigurationAlert|XML 與記錄分析提醒的參數|
|內容|ConfigurationAlert|XML 記錄分析提醒的 「 內容 」|
|工作負載|ConfigurationAlert|技術或者 '工作量 「 提醒參照|
|AdvisorWorkload|建議|技術或者 '工作量 「 建議參照|
|描述|ConfigurationAlert|警示描述 （簡短）|
|DaysSinceLastUpdate|UpdateAgent|幾天以前 （相對於 」 這筆記錄的 TimeGenerated)' 並未這個代理程式安裝任何更新從 WSUS/Microsoft 更新？|
|DaysSinceLastUpdateBucket|UpdateAgent|根據 DaysSinceLastUpdate，分類的多久了已在電腦的 「 時間值區 」 中最後一個安裝任何更新從 WSUS/Microsoft 更新|
|AutomaticUpdateEnabled|UpdateAgent|檢查自動更新啟用或停用這個代理程式？
|AutomaticUpdateValue|UpdateAgent|檢查設定為自動下載及安裝，僅下載，或只檢查自動更新嗎？|
|WindowsUpdateAgentVersion|UpdateAgent|Microsoft Update 代理程式的版本號碼|
|WSUSServer|UpdateAgent|此 update 代理程式的目標的 WSUS 伺服器？|
|OSVersion|UpdateAgent|版本的作業系統上執行此 update 代理程式|
|名稱|ConfigurationObjectProperty 的建議|名稱/標題的建議或從記錄分析組態評估屬性名稱|
|值|ConfigurationObjectProperty|從記錄分析組態評估屬性的值|
|KBLink|建議|描述此最佳作法或更新的知識庫文件 URL|
|RecommendationStatus|建議|建議是幾個類型的記錄 」 更新 」，而不只是新增至搜尋索引。 是否建議使用中] 或開啟，或記錄分析偵測到它已解決，此狀態會變更。|
|RenderedDescription|事件|呈現的描述 （填入參數與重複使用文字） 的 Windows 事件|
|ParameterXml|事件|XML 的 Windows 事件 （如同在事件檢視器） 的 [資料] 區段中的參數|
|EventData|事件|在 Windows 事件 （如同在事件檢視器） 的整個 [資料] 區段與 XML|
|來源|事件|產生的事件的事件記錄來源|
|EventCategory|事件|類別的事件，直接從 Windows 事件記錄檔|
|使用者名稱|事件|Windows 事件 (通常 NT AUTHORITY\LOCALSYSTEM) 的使用者名稱|
|SampleValue|PerfHourly|每小時的彙總的效能計數器的平均值|
|最小值|PerfHourly|每小時的時間間隔的效能計數器下限彙總中的最小值|
|最大值|PerfHourly|每小時的時間間隔的效能計數器下限彙總中的最大值|
|Percentile95|PerfHourly|每小時的時間間隔的效能計數器下限彙總第 95 個百分|
|SampleCount|PerfHourly|用來產生此每小時的彙總記錄多少未經處理 」 的效能計數器樣本|
|威脅|ProtectionStatus|找到惡意程式碼的名稱|
|StorageAccount|W3CIISLog|Azure 儲存體帳戶記錄檔已讀取|
|AzureDeploymentID|W3CIISLog|Azure 部署識別碼的雲端服務記錄檔屬於|
|角色|W3CIISLog|Azure 雲端服務的記錄所屬的角色|
|RoleInstance|W3CIISLog|登入屬於 Azure 角色的 RoleInstance|
|sSiteName|W3CIISLog|登入屬於 （中繼資料庫示意圖）; IIS 網站在原始的記錄檔中的 [s 站台名稱] 欄位|
|sComputerName|W3CIISLog|在原始的記錄檔中的 [s 電腦名稱] 欄位|
|sIP|W3CIISLog|伺服器 IP 位址 HTTP 邀請的收件者為。 在原始的記錄檔中的 [s ip] 欄位|
|csMethod|W3CIISLog|使用在 HTTP 邀請中的用戶端 HTTP 方法 （取得/文章/等）。 原始的記錄檔中的 cs 方法|
|cIP|W3CIISLog|用戶端 IP 位址 HTTP 要求來自。 在原始的記錄檔中的 [c ip] 欄位|
|csUserAgent|W3CIISLog|用戶端宣告 HTTP 使用者代理程式 (在瀏覽器或)。 Cs 使用者代理原始的記錄檔|
|scStatus|W3CIISLog|伺服器所傳回的用戶端 HTTP 狀態碼 （200/403/500/等）。 在原始的記錄檔中的 cs 狀態|
|TimeTaken|W3CIISLog|如何 （以毫秒計） 要求完成所花費的時間。 在原始的記錄檔中的 [timetaken] 欄位|
|csUriStem|W3CIISLog|相對 Uri (不含主控位址，亦即 ' / 搜尋 ') 的要求。 在原始的記錄檔中的 [cs uristem] 欄位|
|csUriQuery|W3CIISLog|URI 查詢。 URI 查詢是必要僅適用於動態頁面，例如 ASP 頁面，因此這個欄位通常包含連字號的靜態網頁。|
|運動項目|W3CIISLog|伺服器連接埠的 HTTP 要求 （和 IIS 接聽，因為它挑選）|
|csUserName|W3CIISLog|如果邀請已驗證和不匿名，驗證的使用者名稱，|
|csVersion|W3CIISLog|在邀請中所使用的 HTTP 通訊協定版本 (也就是 「 HTTP / 1.1 （英文） ')|
|csCookie|W3CIISLog|Cookie 資訊|
|csReferer|W3CIISLog|使用者上次造訪的網站。 此網站提供目前網站的連結。|
|csHost|W3CIISLog|要求的主機標頭 (也就是 「 www.mysite.com 」)|
|scSubStatus|W3CIISLog|子狀態，出現錯誤碼|
|scWin32Status|W3CIISLog|Windows 狀態碼|
|csBytes|W3CIISLog|在邀請中從用戶端傳送至伺服器的位元組|
|scBytes|W3CIISLog|傳回回伺服器的回應，用戶端的位元組|
|ConfigChangeType|ConfigurationChange|變更類型 (WindowsServices / 軟體 / 等)|
|ChangeCategory|ConfigurationChange|類別的變更 （修改 / 新增 / 移除）|
|SoftwareType|ConfigurationChange|軟體的類型 (更新 / 應用程式)|
|SoftwareName|ConfigurationChange|（僅適用於軟體變更） 的軟體名稱|
|Publisher|ConfigurationChange|供應商發行軟體 （僅適用於軟體變更）|
|SvcChangeType|ConfigurationChange|Windows 服務已套用的變更類型 (狀態 / StartupType / 路徑 / ServiceAccount)-僅適用於 Windows 服務變更|
|SvcDisplayName|ConfigurationChange|顯示已變更的服務名稱|
|SvcName|ConfigurationChange|已變更的服務名稱|
|SvcState|ConfigurationChange|新 （目前） 服務狀態|
|SvcPreviousState|ConfigurationChange|前一節已知 （只在服務狀態變更如果適用） 服務的狀態|
|SvcStartupType|ConfigurationChange|服務啟動類型|
|SvcPreviousStartupType|ConfigurationChange|上一個服務啟動類型 （僅在服務啟動類型變更如果適用）|
|SvcAccount|ConfigurationChange|服務帳戶|
|SvcPreviousAccount|ConfigurationChange|上一個服務帳戶 （只在服務帳戶變更如果適用）|
|SvcPath|ConfigurationChange|可執行檔的 Windows 服務的路徑|
|SvcPreviousPath|ConfigurationChange|先前路徑 （只在如果變更適用） 的 Windows 服務可執行檔|
|SvcDescription|ConfigurationChange|服務描述|
|上一個|ConfigurationChange|先前的狀態，本軟體 （安裝 / 未安裝 / 前一版）|
|目前|ConfigurationChange|最新狀態，本軟體 （安裝 / 未安裝 / 目前版本）|

## <a name="next-steps"></a>後續步驟
如需記錄搜尋的詳細資訊︰

- 熟悉[記錄搜尋](log-analytics-log-searches.md)檢視收集解決方案的詳細的資訊。
- 您可以使用[記錄分析中的自訂欄位](log-analytics-custom-fields.md)來延伸記錄搜尋。
