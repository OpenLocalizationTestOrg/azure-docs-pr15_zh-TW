<properties 
    pageTitle="在應用程式的深入見解的分析中參照 |Microsoft Azure" 
    description="分析，應用程式的深入見解的強大的搜尋工具中的陳述式的參考。 " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>分析參考

[分析](app-insights-analytics.md)是[應用程式的深入見解](app-insights-overview.md)的強大的搜尋功能。 這些頁面會說明分析查詢語言。

> [AZURE.NOTE] [測試磁碟機上我們模擬的資料分析](https://analytics.applicationinsights.io/demo)如果您的應用程式未傳送資料至應用程式的深入見解尚未。

## <a name="index"></a>索引


**讓**[讓](#let-clause)


**查詢和運算子**[字數統計](#count-operator) | [評估](#evaluate-operator) | [延伸](#extend-operator) | [加入](#join-operator) | [限制](#limit-operator) | [mvexpand](#mvexpand-operator) | [剖析](#parse-operator) | [專案](#project-operator) | [專案離開](#project-away-operator) | [範圍](#range-operator) | [減少](#reduce-operator) | [轉譯指示詞](#render-directive) | [限制子句](#restrict-clause) | [排序](#sort-operator) | [摘要](#summarize-operator) | [採取](#take-operator) | [頂端](#top-operator) | [頂端巢狀](#top-nested-operator) | [聯集](#union-operator) | [位置](#where-operator) | [位置增益集](#where-in-operator)

**彙總**[任何](#any) |  [argmax](#argmax) | [argmin](#argmin) | [平均](#avg) | [buildschema](#buildschema) | [計數](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [最大](#max) | [min](#min) | [百分位數](#percentile) | [的百分位數](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [加總](#sum) | [變異數](#variance)

**純量**[布林值的串連](#boolean-literals) | [布林值運算子](#boolean-operators) | [轉換](#casts) | [純量的比較](#scalar-comparisons) | [gettype](#gettype) | [雜湊](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**數字**[算術運算子](#arithmetic-operators) | [數字](#numeric-literals) | [abs](#abs) | [回收筒](#bin) | [exp](#exp) | [floor](#floor) | [伽瑪](#gamma) | [記錄](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**日期及時間**[日期和時間運算式](#date-and-time-expressions) | [日期和時間常值](#date-and-time-literals) | [之內](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [現在](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**字串**[Guid](#guids) | [模糊化字串串連](#obfuscated-string-literals) | [字串串連](#string-literals) | [字串比較](#string-comparisons) | [countof](#countof) | [擷取](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [取代](#replace) | [分割](#split) | [strcat](#strcat) | [strlen](#strlen) | [子字串](#substring) | [tolower](#tolower) | [toupper](#toupper)

**陣列、 物件和動態**[陣列及物件的常值](#array-and-object-literals) | [動態物件函數](#dynamic-object-functions) | [讓子句中的動態物件](#dynamic-objects-in-let-clauses) | [JSON 路徑運算式](#json-path-expressions) | [名稱](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [範圍](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>讓

### <a name="let-clause"></a>讓子句

**表格式讓-命名表格**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**純量讓-命名值**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda 讓-命名函數**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

讓的子句會將[名稱](#names)繫結到表格式結果、 純量值或函數。 子句首碼，查詢，並繫結的範圍是該查詢。 （讓不提供您稍後在您的工作階段中使用的名稱各項的方式）。

**語法**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *類型︰*`bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query:*查詢，不讓子句前置字元。

**範例**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


自我聯結︰

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>查詢和運算子

在您遙測查詢組成的來源資料流，後面接著篩選的管線的參考。 例如︰


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
前置管道字元每個篩選`|`*運算子*，某些參數的執行個體。 運算子輸入是表格，是上述管道的結果。 在大部分情況下，任何參數，請輸入的資料行是[純量運算式](#scalars)。 在一些情況下，參數是輸入的資料行的名稱，然後在一些情況下，參數是第二個資料表。 查詢的結果一律是表格，即使僅有一個資料行和一列。

查詢可能包含單一分行符號，但終止空白行。 也可以包含註解之間`//`和一行的結尾。

查詢可能會前面加上一個或多個[讓子句](#let-clause)，其中定義純量、 資料表或查詢中可以使用的函數。

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`會在下面的查詢範例中用來代表上述管道的郵件或來源表格。
> 

### <a name="count-operator"></a>計算運算子

`count`運算子傳回的記錄 （列） 數，在輸入記錄設定。

**語法**

    T | count

**引數**

* *T*︰ 其記錄是要計算的表格式資料。

**傳回**

此函數會傳回含有單一記錄和類型的資料行的資料表`long`。 只儲存格的值是*T*中的記錄數目。 

**範例**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>評估運算子

`evaluate`是，可讓特殊的演算法附加到查詢的副檔名機制。

`evaluate`必須是查詢管線中的最後一個運算子 (除了可能`render`)。 它必須不會出現在函數內文中。

[評估 autocluster](#evaluate-autocluster) | [評估籃](#evaluate-basket) | [評估 diffpatterns](#evaluate-diffpatterns) | [評估 extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>評估 autocluster

     T | evaluate autocluster()

AutoCluster 尋找資料中的分隔屬性 （維度） 的常見的模式，並會降低至小型的圖樣的原始 （是否為 100 位或 100 k 列） 的查詢的結果。 AutoCluster 所開發來協助分析失敗 （例如例外狀況、 當機），但可能就可以使用任何篩選的資料集。 

**語法**

    T | evaluate autocluster( arguments )

**傳回**

AutoCluster 會傳回一組 （通常是小型企業版） 跨多個不連續的屬性擷取部分共用常見的值的資料的模式。 以結果中的資料列，表示每一個模式。 

前兩個資料行是 count 和百分比的不在原始的查詢模式所擷取的資料列。 剩餘的資料行是從原始的查詢和其值為 [特定值從資料行或 ' *' 表示變數值。 

請注意，圖樣並未斷續︰ 他們可能重疊，通常不會包含所有原始資料列。 某些列可能不落在任何模式] 底下。

**秘訣**

* 使用`where`和`project`中輸入管道以減少到只是什麼您感興趣的資料。
* 當您找到感興趣的資料列時，您可能想要進一步將其新增至其特定值以深入瞭解您`where`篩選。

**引數 （所有可省略）**

* `output=all | values | minimal` 

    結果的格式。 [字數統計和百分比] 欄一律會出現在結果中。 

 * `all`-將輸入的所有資料行是輸出
 * `values`-篩選出資料行只 ' *' 在結果中
 * `minimal`-也會篩選出相同的原始的查詢中的所有資料列的欄。 


* `min_percent=`*雙引號*(預設︰ 1)

    產生的資料列的最小百分比的範圍。

    範例︰`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*int*(預設︰ 25) 

    種子數會決定演算法的初始本機搜尋點的數目。 在某些情況下，根據的資料，結構增加種子數目會增加透過在較慢的查詢取捨空間，提高的搜尋結果的數字 （或品質）。 Num_seeds 引數的降低兩個方向的結果，以便將其下方 5 達成些許的效能增強功能，並增加 50 上方很少會產生其他模式。

    範例︰`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < 雙 < 1*+ (預設︰ 0.5)

    可讓您控制部分一般 （高涵蓋） 和資訊 （許多共用的值） 之間的平衡。 增加 size_weight 通常減少的圖樣，每一個模式會以涵蓋較大的百分比。 減少 size_weight 通常會產生更具體的圖樣，較小的百分比涵蓋與共用的值。 顯示進階設定公式是加權的幾何平均值之間標準化的一般分數及 size_weight 與 1 size_weight 指定具資訊意義的分數為 [線寬]。 

    範例︰`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*column_name*

    根據指定的權重根據輸入每個資料列 （依預設每個資料列具有粗細 '1'），粗細欄的常見使用方式是將帳戶取樣] 或 [值區/彙總已將每個資料列內嵌的資料。

    範例︰`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>評估購物籃

     T | evaluate basket()

購物籃尋找資料中的分隔屬性 （維度） 的所有經常模式，並且會傳回傳遞頻率臨界原始的查詢中的所有經常模式。 購物籃保證尋找資料中的所有經常模式，但不是保證有多項式的執行時間。 查詢的執行時間線性中的列數，但在某些情況下可能指數中的欄 （維度） 數。 購物籃根據原始開發的籃分析資料採礦 Apriori 演算法。 

**傳回**

出現在一個以上的事件指定分數 （預設值 0.05） 中所有的模式。

**引數 （所有可省略）**


* `threshold=`*0.015 < 雙 < 1*(預設︰ 0.05) 

    設定被視為經常之資料列的最小的比率 （不會傳回具有較小的比例模式）。

    範例︰`T | evaluate basket("threshold=0.02")`


* `weight_column=`*column_name*

    根據指定的權重根據輸入每個資料列 （依預設每個資料列具有粗細 '1'），粗細欄的常見使用方式是將帳戶取樣] 或 [值區/彙總已將每個資料列內嵌的資料。

    範例︰ T |評估 basket("weight_column=sample_Count")


* `max_dims=`*1 < int*(預設︰ 5)

    每個籃，預設值，減少查詢執行階段受限於設定詞的不相關的尺寸的最大數目。


* `output=minimize` | `all` 

    結果的格式。 [字數統計和百分比] 欄一律會出現在結果中。

 * `minimize`-篩選出資料行只 ' *' 在結果中。
 * `all`-將輸入的所有資料行的輸出。




#### <a name="evaluate-diffpatterns"></a>評估 diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns 比較兩個資料集的相同的結構，並會找出模式的分隔屬性 （維度） 描述兩個資料集之間的差異。 Diffpatterns 所開發來協助分析 （例如依指定的時間範圍中的非失敗的比較失敗） 失敗，但可能可以找到相同的結構的任何兩個資料集之間的差異。 

**語法**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**傳回**

Diffpatterns 會傳回一組 （通常是小型企業版） 擷取的兩個集合中的資料的不同部分 （亦即模式擷取大量的第一個資料集中的資料列和第二個集合中的資料列低的百分比） 的模式。 以結果中的資料列，表示每一個模式。

前四個資料行是 count 和百分比的不在原始的查詢，每個集合中的模式所擷取的資料列，第五欄是兩個集之間的差異 （絕對百分比點為單位）。 剩餘的資料行是從原始的查詢和其值為 [特定值從資料行或 * 表示變數值。 

請注意，圖樣並未相異︰ 他們可能重疊，通常不會包含所有原始資料列。 某些列可能不落在任何模式] 底下。

**秘訣**

* 使用的位置，並輸入管道以減少到只是什麼您感興趣的資料中的專案。

* 當您找到感興趣的資料列時，您可能要切入進一步以將其特定值新增至您的位置篩選。

**引數**

* `split=`*資料行名稱*（必要）

    資料欄必須具備確實兩個值。 如果有需要，請建立一個資料行︰

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*字串*

    告訴只尋找演算法模式的目標資料集中有較高的百分比、 目標必須是一個兩個值的分割資料行。

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0.015 < 雙 < 1*(預設︰ 0.05) 

    設定最小的圖樣 （比例） 之間的差異兩組。

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    結果的格式。 [字數統計和百分比] 欄一律會出現在結果中。 

 * `minimize`-篩選出資料行只 ' *' 在結果中
 * `all`-將輸入的所有資料行是輸出

* `weight_column=`*column_name*

    根據指定的權重根據輸入每個資料列 （依預設每個資料列具有粗細 '1'）。 常用的 [寬度] 資料行是將帳戶取樣] 或 [值區/彙總已將每個資料列內嵌的資料。

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>評估 extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns 用來豐富具有多個簡單的資料行，以動態方式解壓縮登出 （半透明） 根據其類型的結構化資料行的資料表。 目前支援 json 欄為限，同時動態和字串的 jsons 序列化。


* `max_columns=`*int*(預設︰ 10) 

    新增新的資料欄的數字是動態，而且可以很大 （實際上是 json 的所有記錄中的唯一索引鍵的數字），我們也必須將其限制。 新的資料行依遞減根據他們的頻率順序排序，並最 max_columns 新增至資料表。

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*雙引號*(預設︰ 10.0) 

    略過的欄的頻率低於 min_percent 也能限制新的資料行的另一種方法。

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*bool*(預設︰ true) 

    如果 true 複雜的資料行名稱將會新增為首碼解壓縮的欄名稱。


* `prefix_delimiter=`*字串*(預設: 「 _ 」) 

    如果 add_prefix = true，則此參數會將用來串連新的資料行名稱的分隔符號。

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*bool*(預設值︰ false) 

    如果為 true 的原始 (json) 資料行會保留在 [輸出表格。


* `output=query | table` 

    結果的格式。 

 * `table`-輸出是相同的資料表，所接收減去指定輸入的資料行，加上新擷取自輸入的資料行的資料行。
 * `query`-輸出是字串，表示您想要對為表格的結果的查詢。 




### <a name="extend-operator"></a>延伸運算子

     T | extend duration = stopTime - startTime

將一或多個導出資料行新增至資料表。 


**語法**

    T | extend ColumnName = Expression [, ...]

**引數**

* *T:*輸入的表格。
* *ColumnName:*若要新增資料行的名稱。 [名稱](#names)會區分大小寫，可以包含字母，數字或 '_' 字元。 使用`['...']`或`["..."]`至報價關鍵字或與其他字元的名稱。
* *運算式︰*在現有的資料行的計算。

**傳回**

[輸入] 資料表中指定的其他資料行的複本。

**秘訣**

* 使用[`project`](#project-operator)相反地，如果您也想要放或重新命名某些資料欄。
* 不使用`extend`只是要取得在長的運算式中使用較短的名稱。 `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    索引的原生資料行的資料表;您的新名稱定義未編製索引，其他欄，讓查詢很慢。

**範例**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>加入運算子

    Table1 | join (Table2) on CommonColumn

合併兩個資料表的資料列所指定的資料行的相符的值。


**語法**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**引數**

* *Table1* -'左側 」 的聯結。
* *2* -'右側' 加入。 它可以是輸出表格的巢狀的查詢運算式。
* *CommonColumn* -的資料行在兩個資料表中有相同的名稱。
* *類型*-指定兩個資料表的資料列的對應的方式。

**傳回**

使用表格︰

* 每個資料行中的兩個資料表，包括相符的索引鍵資料行。 如果有名稱衝突，就會自動命名右側的資料行。
* 輸入資料表之間的每一個相符項一列。 相符的項目是從所有具有相同值的一個資料表中選取一列`on`為在其他資料表的資料列的欄位。 

* `Kind`未指定的

    從左側的只有一個資料列的每個值相符`on`鍵。 輸出包含列右側的資料列，使用此列的每一個相符項目。

* `Kind=inner`
 
     資料列中沒有相符的資料列的每個組合輸出從左和向右鍵。

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     除了內部相符的項目，有一列的每一列的左側 （及/或向右），不論是否有不相符。 在此情況下，不吻合資料的輸出儲存格包含 null 值。

* `kind=leftanti`

     傳回從左側沒有相符的項目從右邊的所有記錄。 結果資料表只會有從左邊的資料行。 
 
如果有多個與這些欄位的值相同的列，您會收到列的所有組合。

**秘訣**

為獲得最佳效能︰

* 使用`where`和`project`之前減少的數字的列數和欄中輸入的資料表， `join`。 
* 如果永遠小於另一個資料表，請將其作為 （管道） 的聯結的左側。
* 加入相符的資料行必須有相同的名稱。 如有必要，若要重新命名欄中的資料表，請使用專案運算子。

**範例**

取得從記錄中的某些項目符號的開始和結束活動的延伸活動。 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>限制運算子

     T | limit 5

輸入表格從 [傳回進位至指定的列數。 有哪些記錄會傳回不保證。 (若要傳回特定的記錄，請使用[`top`](#top-operator)。)

**Alias （別名)**`take`

**語法**

    T | limit NumberOfRows


**秘訣**

`Take`是簡單且有效的方式，可查看結果的範例，當您使用互動的方式。 請注意，並不保證來產生任何特定的列，或以任何特定順序產生它們。

有是用戶端，傳回的資料列數目的隱含限制，即使您不是使用`take`。 若要提升此限制，使用`notruncation`用戶端要求選項。



### <a name="mvexpand-operator"></a>mvexpand 運算子

    T | mvexpand listColumn 

讓每一個項目具有不同資料列，請展開從動態輸入 (JSON) 儲存格的清單。 重複的所有其他的儲存格展開的列。 

(請參閱[`summarize makelist`](#summarize-operator)執行相反的函數。)

**範例**

假設輸入的資料表︰

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{「 金鑰 」: 「 值 」}|
|2|「 世界 」|[0,1，「 k 」、 「 v 」]|

    mvexpand D

結果如下︰

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{「 金鑰 」: 「 值 」。|
|2|「 世界 」|0|
|2|「 世界 」|1|
|2|「 世界 」|"k"|
|2|「 世界 」|「 v 」|


**語法**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**引數**

* *ColumnName:*在結果中，在 [名稱] 欄的陣列會展開以多個資料列。 
* *ArrayExpression:*運算式，進而產生陣列。 如果使用此表單時，新的資料行，並會保留現有的項目。
* *名稱︰*新的資料行名稱。
* *Typename:*轉換為特定類型的展開的運算式
* *RowLimit:*每個原始資料列產生的資料列數上限。 預設值是 128。

**傳回**

多個資料列，每個命名資料行中，或在陣列運算式中的任何陣列中的值。

展開資料行具有動態類型。 例如，使用轉換`todatetime()`或`toint()`如果您想要計算或彙總的值。

支援的屬性包展開的兩種模式︰

* `bagexpansion=bag`︰ 將單一項目屬性袋展開屬性袋。 這是預設展開。
* `bagexpansion=array`︰ 將兩個項目展開] 屬性袋`[`*鍵*`,`*值*`]`陣列結構，允許統一存取索引鍵和值 （以及，例如，在屬性名稱執行相異計數彙總）。 

**範例**


    exceptions | take 1 
  	| mvexpand details[0]

分割列的 [詳細資料] 欄位中的每個項目中的例外狀況記錄。



### <a name="parse-operator"></a>剖析運算子

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

從字串中擷取的值。 可以使用簡單或規則運算式相符。

**語法**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**引數**

* `T`︰ 輸入的表格。
* `kind`: 
 * `simple`（預設）︰`Match`字串是純文字字串。
 * `relaxed`︰ 如果文字無法剖析為類型的欄，欄會設定為 null 和剖析持續 
 * `regex`:`Match`字串的規則運算式。
* `Text`︰ 資料行或其他運算式評估為或可以轉換成字串。
* *符合的項目︰*比對下一個字串的一部分，並放棄它。
* *欄︰*指定此欄之字串的下一個部分。 如果不存在，會建立資料行。
* *類型︰*剖析字串的下一個組件為指定的類型，例如 int、 雙日期。 


**傳回**

輸入的表格，延伸根據的資料行清單。

中的項目`with`子句會依序比對來源文字。 每個項目 chews 關閉文字區塊的來源︰ 

* 常值字串或規則運算式將相符的游標移動比對長度。
* 在 regex 剖析，規則運算式可以使用降低運算子 '？ ' 越快越前往下列符合的項目。
* 資料行名稱與類型剖析文字為指定的類型。 除非類型 = 寬鬆，失敗的剖析失效符合整個模式。
* 資料行名稱，而不是類型，或與類型 」 字串 」，將複製的最小值，前往下列相符的字元數。
* 「 *' 略過的最小值，前往下列相符的字元數。您可以使用 「*」 的開頭和結尾的模式中，在或之後以外的字串，或是字串相符的項目類型。

所有剖析模式中的項目必須正確; 符合否則，將會不產生任何結果。 此規則的例外狀況時，類型 = 寬鬆，如果剖析輸入的變數失敗，請持續的剖析的其餘部分。

**範例**

*簡單︰*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | [計數器 | 簡報 | 年
---|---|---|---
1 | 2 | socks | 多為 63

*寬鬆︰*

輸入包含時輸入的每一欄的正確相符項目，寬鬆的剖析會產生簡單的剖析為相同的結果。 但如果輸入的資料行的其中一個不正確剖析，寬鬆的剖析會繼續處理其餘的圖樣，而簡單的剖析停止且無法產生任何結果。


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | 簡報 | 年
---|---|---
1 |  socks | 多為 63


*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

資源 | 扇形區 | 鎖定 | 發行 | 上一個
---|---|---|---|---
排程器 | 16 | 02/17 2016年 08:41:00 | 02/17 2016年 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>專案運算子

    T | project cost=price*quantity, price

選取的資料行包含、 重新命名或刪除，並插入新的計算資料行。 引數的順序來指定結果中的資料行的順序。 在結果中包含引數中指定的資料行︰ 輸入中的任何其他會遭到捨棄。  (請參閱`extend`。)


**語法**

    T | project ColumnName [= Expression] [, ...]

**引數**

* *T:*輸入的表格。
* *ColumnName:*若要顯示在輸出資料行的名稱。 沒有*運算式*，必須輸入中出現該名稱的資料行。 [名稱](#names)會區分大小寫，可以包含字母，數字或 '_' 字元。 使用`['...']`或`["..."]`至報價關鍵字或與其他字元的名稱。
* *運算式︰*輸入的資料行的參考選用的純量運算式。 

    則法律傳回相同的現有資料行名稱的新導出資料行中輸入。

**傳回**

表格中有資料行命名為引數，並盡可能輸入表格的資料列。

**範例**

下列範例會示範幾種操作，可以使用`project`運算子。 輸入的表格`T`有三種類型的資料行`int`: `A`， `B`，及`C`。 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>專案離開運算子

    T | project-away column1, column2, ...

排除指定的資料行。 結果會包含除了您輸入的資料行。

### <a name="range-operator"></a>範圍運算子

    range LastWeek from ago(7d) to now() step 1d

會產生值的單一資料行資料表。 請注意，沒有管線，輸入。 

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**語法**

    range ColumnName from Start to Stop step Step

**引數**

* *ColumnName:*輸出資料表中的單一資料行名稱。
* *開始︰*輸出中的最小值。
* *停止︰*輸出 （或如果*步驟*步驟上此值的最高值的繫結） 中所產生的最高值。
* *步驟︰*兩個連續的值之間的差異。 

引數必須是數字、 日期或時段的值。 他們不能參考任何資料表的資料行。 （如果您想要計算輸入資料表為基礎的範圍，使用[範圍*函數*](#range)，是不是使用[mvexpand 運算子](#mvexpand-operator)）。 

**傳回**

名為*ColumnName*，其值為*啟動*，請*啟動*單一資料行的資料表 + *步驟*，...，且包含*停止*。

**範例**  

```AIQL
range Steps from 1 to 8 step 3
```

單一資料行的資料表稱為`Steps`型態`long`，其值`1`， `4`，及`7`。

**範例**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

在過去七天午夜的資料表。 回收筒 (floor) 函數減少每次開始的日期。

**範例**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

顯示如何`range`運算子可用於建立小型臨機操作、 維度資料表，然後用來介紹零的來源資料已經沒有值的位置。

### <a name="reduce-operator"></a>減少運算子

    exceptions | reduce by outerMessage

嘗試一起組成群組，類似的記錄。 針對每個群組中，輸出運算子`Pattern`其認為最適合說明該群組，以及`Count`該群組中的記錄。


![](./media/app-insights-analytics-reference/reduce.png)

**語法**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**引數**

* *ColumnName:*若要檢查資料行。 它必須是字串類型。
* *閥值︰*{包括 0 和 1} 範圍中的值。 預設值為 0.001。 大型輸入，閥值應該是小型。 

**傳回**

兩個資料行，`Pattern`和`Count`。 在許多情況下，圖樣會完成資料行的值。 在某些情況下，它可以識別常見的字詞，並取代變數的組件與 ' *'。

例如，結果`reduce by city`可能包括︰ 

|圖樣 | 字數統計 |
|---|---|
| 舊 * | 5182 |
| 聖 * | 2846 |
| 莫斯科 | 3726 |
| \*-在-\* | 2730 |
| 輸入辦公室電話 | 27163 |


### <a name="render-directive"></a>呈現指示詞

    T | render [ table | timechart  | barchart | piechart ]

轉譯指示展示層顯示表格的方式。 您應該管道的最後一個項目。 很方便的其他方法來使用顯示]，讓您以特定的簡報方法來儲存查詢上的控制項。

### <a name="restrict-clause"></a>限制子句 

指定資料表名稱，請依照下列運算子使用的設定。 例如︰

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>排序運算子 

    T | sort by country asc, price desc

排序順序將一或多個欄輸入資料表的資料列。

**Alias （別名)**`order`

**語法**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**引數**

* *T:*輸入要排序表格。
* *欄︰**T*的做為排序依據的欄。 類型的值必須是數字、 日期、 時間或字串。
* `asc`排序方式將高至低遞增順序]。 預設值是`desc`，遞減高至低。

**範例**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
所有的資料列中有特定的表格追蹤`ActivityId`、 排序，其時間戳記。

### <a name="summarize-operator"></a>摘要運算子

會產生資料表彙總輸入表格的內容。
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

資料表中每個國家/地區顯示的數字、 平均要求期間與設定的城市。 每個不同國家/地區的輸出中有一個資料列。 輸出資料行顯示 count、 average 工期、 城市和國家/地區。 忽略所有其他輸入的資料行。


    T | summarize count() by price_range=bin(price, 10.0)

顯示有多少項目中 [0,10.0]，每個間隔價格的表格 [10.0,20.0]，依此類推。 此範例中有計數的資料行，一個用於價格範圍。 忽略所有其他輸入的資料行。


**語法**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**引數**

* *欄︰*選用的結果資料行的名稱。 預設值的名稱從運算式。 [名稱](#names)會區分大小寫，可以包含字母，數字或 '_' 字元。 使用`['...']`或`["..."]`至報價關鍵字或與其他字元的名稱。
* *彙總︰*在彙總函數，例如通話`count()`或`avg()`，以資料行名稱做為引數。 請參閱[彙總](#aggregations)。
* *GroupExpression:*提供一組唯一值的運算式的資料行。 通常是已提供有限的值，其中一個資料行名稱或`bin()`數值或時間的資料行，做為引數。 

如果所提供的數值或時間的運算式，而不使用`bin()`，分析會自動套用其與時間間隔的`1h`的時間，或`1.0`的數字。

如果您沒有提供*GroupExpression，*則會將整個表格摘要單一輸出資料列中。



**傳回**

輸入的資料列排列成群組有相同值的`by`運算式。 然後指定的彙總函數會計算每個群組，產生的每個群組的一列上方。 結果會包含`by`欄和每個也至少有一個資料行來計算彙總。 （某些彙總函數︰ 傳回多個資料行）。

結果將會有多個列的不同組合所`by`值。 如果您要合併彙算的數字值的範圍，請使用`bin()`以減少範圍不連續的值。

**附註**

雖然您可以提供任意運算式彙總與群組運算式，它會更有效率使用簡單的資料行名稱，或套用`bin()`的數字的資料行。



### <a name="take-operator"></a>需要運算子

Alias （別名) 的[限制](#limit-operator)


### <a name="top-operator"></a>頂端運算子

    T | top 5 by Name desc nulls first

會傳回依指定的資料行排序的第一個*N*記錄。


**語法**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**引數**

* *NumberOfRows:*若要傳回*T*的資料列數目。
* *Sort_expression:*用來排序資料列運算式。 通常是只是資料行名稱。 您可以指定一個以上的 sort_expression。
* `asc`或`desc`（預設） 可能會出現控制項選取項目實際上是從 「 下方 」 或 「 上方 」 的範圍。
* `nulls first`或`nulls last`控制項 null 值的顯示位置。 `First`預設`asc`，`last`預設`desc`。


**秘訣**

`top 5 by name`表面上相同`sort by name | take 5`。 不過，執行速度，一律會傳回排序結果，而`take`讓這類保證。

### <a name="top-nested-operator"></a>頂端巢狀運算子

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

產生的階層式的結果，每個層級，向下切入從上一層級] 位於何處。 很適合用來回答的問題也聽起來 」 有哪些上方 5 要求]，及的每個是什麼上方 3 效能值區，以及每個它們，這頂端 3 國家/地區要求來自？ 」

**語法**

   T |頂端巢狀 N 的資料行的彙總 [，...]

**引數**

* N:int-傳回或傳遞到下一層級的資料列數目。 在查詢中其中 N 是 5、 3 和 3 的三個層級，列的總數會 45。
* 資料行的彙總的 [群組依據欄。 
* 彙總-要套用至每個群組的資料列的[彙總函式](#aggregations)。 這些彙總的結果會決定要顯示的上方群組。


### <a name="union-operator"></a>聯集運算子

     Table1 | union Table2, Table3

需要兩個或多個資料表，並傳回的所有資料列。 

**語法**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**引數**

* *Table1*， *Table2* ...
 *  表格的名稱，例如`requests`，或將表格定義在[讓子句](#let-clause);或
 *  A 中查詢運算式，例如`(requests | where success=="True")`
 *  一組指定使用萬用字元的資料表。 例如，`e*`想表單已定義之名稱開頭 'e'，與 '例外狀況 」 資料表的前一個讓子句中的所有資料表的聯集。
* `kind`: 
 * `inner`-結果將會有通用至所有輸入資料表的資料行的子集。
 * `outer`-結果有發生的所有資料行中輸入的任何。 未定義所輸入的資料列的儲存格設定為`null`。
* `withsource=`*ColumnName:*如果指定，輸出中會包含欄名為*ColumnName*的值會指出哪些來源資料表已提供每個資料列。

**傳回**

為有多個列的資料表的所有輸入資料表，及為有多欄中輸入唯一的資料行名稱。

**範例**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
名稱開頭 「 tt 」 的所有資料表的聯集。


**範例**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
有產生的不同使用者數目`exceptions`事件或`traces`移過去的一天的事件。 在結果中，「 SourceTable 」 欄會指出 「 查詢 」 或 「 命令]。

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

這個更有效率的版本會產生相同的結果。 篩選每個資料表建立聯集之前。

### <a name="where-operator"></a>位置運算子

     requests | where resultCode==200

篩選資料表以符合述詞的資料列的子集。

**Alias （別名)**`filter`

**語法**

    T | where Predicate

**引數**

* *T:*表格式輸入其記錄的篩選。
* *述詞︰*A `boolean` *T*的資料行的[運算式](#boolean)。將評估*T*中每個資料列。

**傳回**

*T*的*述詞*是中的資料列`true`。

**秘訣**

若要將最快的效能︰

* **使用簡單的比較**資料行名稱和常數。 (「 固定 」 資料表-表示常數，讓`now()`和`ago()`是 [確定]，所以會以及純量值分派使用[`let`子句](#let-clause)。)

    例如，偏好`where Timestamp >= ago(1d)`至`where floor(Timestamp, 1d) == ago(1d)`。

* **Simplest 條款第一**︰ 如果您有多個子句共同使用`and`，將第一次涉及僅在單一欄條款。 因此`Timestamp > ago(1d) and OpId == EventId`優於倒。


**範例**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

不早於 1 小時，而且是來自來源稱為 「 Kuskus 」，並有相同值的兩個資料行的記錄。 

請注意，我們將兩個資料行之間的比較最後一個，就不能使用索引並強制掃描。


### <a name="where-in-operator"></a>位置 in 運算子

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**語法**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**引數**

* `col`︰ 在資料表中資料行。
* `expr1`...: 純量運算式的清單。

使用`in`用來在其中包含只有資料列`col`等於其中一個運算式`expr1...`。

使用`!in`包含只有資料列在其中`col`不相等的任何運算式`expr1...`。  


## <a name="aggregations"></a>彙總

彙總的函數，用於合併在中的[摘要作業](#summarize-operator)建立群組中的值。 例如，在此查詢中，dcount() 是彙總函式︰

    requests | summarize dcount(name) by success

### <a name="any"></a>任何 

    any(Expression)

隨機選取其中一列的群組，並傳回指定之運算式的值。

這是很有用，例如時某些資料欄有大量的類似的值 （例如，「 錯誤文字 」 欄），而且您想要一次一個群組複合索引鍵的唯一值的資料行的範例。 

**範例**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

找到 [最小化/maximises *ExprToMaximize*，並傳回值的*ExprToReturn* ] 群組中的一列 (或`*`傳回整個資料列)。

**提示**︰ 傳遞到欄會自動重新命名。 若要確保您使用的正確的名稱，檢查結果使用`take 5`將結果輸出到另一個運算子之前。

**範例**

為每個要求的名稱，顯示發生的最長的要求︰

    requests | summarize argmax(duration, timestamp) by name

顯示所有的最長的邀請，而不只是時間戳記的詳細資料︰

    requests | summarize argmax(duration, *) by name


找出每個公制，以及其時間戳記和其他資料的最低值︰

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>平均

    avg(Expression)

計算整個群組*運算式*的平均值。

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

傳回准許加入*DynamicExpression*的所有值的最小結構描述。 

參數的資料行類型應該`dynamic`-陣列或屬性紙袋。 

**範例**

    exceptions | summarize buildschema(details)

結果︰

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

請注意，`indexer`用來標示您應該使用數值索引的位置。 此結構描述，就會 （假設這些範例索引範圍中） 一些有效路徑︰

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**範例**

假設輸入的資料行具有三個動態值︰

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


會產生的結構描述︰

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

結構描述告訴我們的︰

* 根物件是名為四個屬性與容器 x 與 y、 z t。
* 稱為 「 x 」 的可能的屬性會的類型 」 int 」 或 「 string 」 類型。
* 呼叫"y"的可能的其中一項 「 雙 」 類型的屬性，或屬性的另一個容器稱為 「 類型 」 字串 」 的 「 w 」。
* ``indexer``關鍵字表示 「 z 」 和 「 t 」 陣列。
* 陣列 」 z 」 中的每個項目是 int 或字串。
* 「 t 」 是陣列的字串。
* 每個屬性隱含，而且可能會是空的任何陣列。

##### <a name="schema-model"></a>結構描述模型

傳回的結構描述的語法是︰

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

他們的年齡子集 TypeScript 類型註釋編碼為動態值相同。 在 Typescript，是範例結構描述︰

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>字數統計

    count([ Predicate ])

傳回的資料列的*述詞*評估為計數`true`。 如果有指定任何*述詞*，傳回記錄總數] 群組中。 

**效能的秘訣**︰ 使用`summarize count(filter)`而不是`where filter | summarize count()`

> [AZURE.NOTE] 避免使用 count （） 若要尋找的要求、 例外狀況或其他事件發生的數目。 [取樣](app-insights-sampling.md)作業後，仍會保留在應用程式的深入見解的資料點數目會小於原始事件的數目。 請改用`summarize sum(itemCount)...`。 ItemCount 屬性會反映出原始事件代表每個保留的資料點的數目。

### <a name="countif"></a>countif

    countif(Predicate)

傳回的資料列的*述詞*評估為計數`true`。

**效能的秘訣**︰ 使用`summarize countif(filter)`而不是`where filter | summarize count()`

> [AZURE.NOTE] 避免使用 countif 尋找要求、 例外狀況或其他事件發生的數目。 作業[取樣](app-insights-sampling.md)時，資料點數目會小於實際的事件的數目。 請改用`summarize sum(itemCount)...`。 ItemCount 屬性會反映出原始事件代表每個保留的資料點的數目。

### <a name="dcount"></a>dcount

    dcount( Expression [ ,  Accuracy ])

傳回群組中的唯一值的*Expr*的數字的估計值。 (若要在清單中的相異值，使用[`makeset`](#makeset)。)

*正確性*，如果指定，控制速度和精確度平衡。

 * `0`= 至少有正確且最快速的計算。
 * `1`預設值，平衡正確性和計算的時間。瞭解 0.8%錯誤。
 * `2`= 最慢且計算。瞭解 0.4%錯誤。

**範例**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

在群組的*述詞*為真，則會傳回*Expr*的資料列的唯一值的數字的估計值。 (若要在清單中的相異值，使用[`makeset`](#makeset)。)

*正確性*，如果指定，控制速度和精確度平衡。

 * `0`= 至少有正確且最快速的計算。
 * `1`預設值，平衡正確性和計算的時間。瞭解 0.8%錯誤。
 * `2`= 最慢且計算。瞭解 0.4%錯誤。

**範例**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist

    makelist(Expr [ ,  MaxListSize ] )

傳回`dynamic`(JSON) 陣列*Expr* ] 群組中的所有的值。 

* *MaxListSize*會在傳回的項目數目上限為選用的整數限制 （預設值是*128*）。

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

傳回`dynamic`的一組*Expr*所需的時間] 群組中的唯一值的陣列，(JSON)。 (提示︰ 只要計算唯一值，請使用[`dcount`](#dcount)。)
  
*  *MaxSetSize*會在傳回的項目數目上限為選用的整數限制 （預設值是*128*）。

**範例**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

另請參閱[`mvexpand`運算子](#mvexpand-operator)相反的函數。


### <a name="max-min"></a>最大值，最小值

    max(Expr)

計算*Expr*的最大值。
    
    min(Expr)

計算*Expr*的最小值。

**提示**︰ 這可讓您的最小或最大值自己-例如、 最高或最低的價格。 但如果您想要其他欄中的資料列，例如名稱最低的價格的供應商-使用[argmin 或 argmax](#argmin-argmax)。


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>百分位數、 的百分位數、 percentilew percentilesw

    percentile(Expression, Percentile)

傳回指定的百分位數] 群組中的*運算式*估計值。 精確度取決於區域中的百分位數的母體的密度。
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

例如`percentile()`，但會計算數字的百分位數的值 （這是較快，個別計算每個百分位數）。

    percentilew(Expression, WeightExpression, Percentile)

加權的百分位數。 您可以使用此預設彙總的資料。  `WeightExpression`是整數，表示多少原始資料列會以每個彙總的資料列。

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

例如`percentilew()`，但會計算數字的百分位數值。

**範例**


值的`duration`大於 95%的設定和小於 5%範例設定，計算的每一個要求名稱的範例︰

    request 
  	| summarize percentile(duration, 95)
      by name

省略 」 來...」 來計算整個表格。

同時計算多個百分位數，不同的要求名稱︰

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

結果會顯示的要求 /Events/Index 的請要求的 5%會回應中小於 2.44s，3.52s，其中的下半部，且低於 6.85s年 5%。

計算多個統計資料︰

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>加權的百分位數

在已預先彙總資料的情況下使用加權的百分位數函數。 

例如，假設您的應用程式執行大量的秒的作業，而且您想要知道他們延遲。 簡單的解決方案是產生應用程式的深入見解要求或自訂活動的每一項作業。 雖然調整取樣會生效減少，這會建立大量的資料傳輸。 但您決定執行更好的解決辦法︰ 您會在您的應用程式，然後再傳送以應用程式獲得深入見解彙總資料中撰寫的部分程式碼。 彙總的摘要將會傳送定期間隔，可能減少每分鐘的幾個點的資料工資率。

您的程式碼會延遲以毫秒為單位的度量單位的資料流。 例如︰
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

它會計算下列區間度量值︰`{ 10, 20, 30, 40, 50, 100 }`

定期，這可讓一系列的 TrackEvent 呼叫，針對每個連結，以自訂的度量單位，每個通話並列文字格式︰ 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

在分析資料，您會看到一個這類群組的事件，像這樣︰

`opCount` | `latency`| 意義
---|---|---
8 | 10 | = 10 毫秒回收筒] 中的 8 作業
6 | 20 | = 6 20 毫秒回收筒] 中的作業
3 | 30 | = 3 30ms年回收筒] 中的作業
1 | 40 | = 1 40ms年回收筒] 中的作業

若要取得準確的原始的通訊群組的事件延遲，我們使用`percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

結果是相同，如同我們所使用的純`percentiles`原始的度量單位上。

> [AZURE.NOTE] 加權的百分位數不會用於[取樣資料](app-insights-sampling.md)，其中每個取樣資料列代表隨機樣本原始資料列，而不是回收筒。 純文字的百分位數函數是適用於取樣資料。

#### <a name="estimation-error-in-percentiles"></a>中的百分位數的估計錯誤

將彙總的百分位數提供使用[T 摘要](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)估計值。 

幾個重要事項︰ 

* 要求的百分位數的值，而有所不同估計錯誤的範圍。 最佳的正確性且結束 [0..100] 的縮放，0 的百分位數 100 的確切的最小值和分配的最大值。 精確度逐漸將會減少小數位數的中間。 表現最差的中位數，並會處於 1%。 
* 錯誤範圍被觀察的排名上的值。 假設百分位數 (X 50) 傳回 Xm 的值。 評估保證至少 49%和最 51%的值的 X 是 Xm 小於。 有任何理論限制位於 Xm 與實際 X 的中位數的值之間的差異。

### <a name="stdev"></a>stdev 函數

     stdev(Expr)

傳回群組上*Expr*的標準差。

### <a name="variance"></a>變異數

    variance(Expr)

傳回*Expr*群組上方的變異數。

### <a name="sum"></a>加總

    sum(Expr)

傳回群組上的*Expr*總和。                      


## <a name="scalars"></a>純量

[轉換](#casts) | [比較](#scalar-comparisons)
<br/>
[gettype](#gettype) | [雜湊](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

支援的類型如下︰

| 類型      | 其他的名稱   | 對等的.NET 類型 |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>轉換

您可以從一種類型轉換到另一個。 一般而言，如果在轉換意義運作方式︰

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

檢查字串是否可以轉換成特定類型︰

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>純量的比較

||
---|---
`<` |降低
`<=`|降低或等於
`>` |大於
`>=`|大於或等於
`<>`|不等於
`!=`|不等於 
`in`| 右運算元 （動態） 的陣列，而左邊的運算元等於其中一個項目。
`!in`| 右運算元 （動態） 的陣列，而左邊的運算元不等於任何一項目。




### <a name="gettype"></a>gettype

**傳回**

代表單一引數的基礎的儲存空間類型的字串。 當您有類型的值時，這是特別有用`dynamic`︰ 在此情況下`gettype()`會顯示值會編碼方式。

**範例**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>雜湊

**語法**

    hash(source [, mod])

**引數**

* *來源*︰ 雜湊會計算純量的來源。
* *mod*: 模數套用雜湊結果上的值。

**傳回**

指定的純量，指定 mod 值 （如果有指定） 模數 xxhash （時間） 值。

**範例**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>iff

`iff()`函數會計算第一個引數 （述詞），並傳回第二或第三個引數是述詞而定值`true`或`false`。 第二個和第三個引數必須屬於相同類型。

**語法**

    iff(predicate, ifTrue, ifFalse)


**引數**

* *述詞︰*若要評估的運算式`boolean`值。
* *ifTrue:*要評估的運算式，其值函式如果*述詞*評估為傳回`true`。
* *ifFalse:*要評估的運算式，其值函式如果*述詞*評估為傳回`false`。

**傳回**

此函數會傳回*ifTrue*的值，如果*述詞*評估為`true`，或*ifFalse*否則的值。

**範例**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>isnull isnotnull notnull

    isnull(parsejson("")) == true

使用單一引數，會告訴您是否為 null。

**語法**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**傳回**

True 或 false 根據是否值會是空值或不是 null。


|x|isnull(x)
|---|---
| "" | false
|「 x 」 | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false

**範例**

    T | where isnotnull(PossiblyNull) | count

請注意有達成此效果的其他方法︰

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

評估查詢或運算式，並會傳回單一值的結果。 這個功能很有用的分段計算。例如，計算總計的事件，然後使用的比較基準。

**語法**

    toscalar(query)
    toscalar(scalar)

**傳回**

評估引數。 如果引數是資料表，會傳回第一列的第一欄。 （很好的做法是排列引數的一個欄和列）。

**範例**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>布林值的串連

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>布林值運算子

    and 
    or 

    

## <a name="numbers"></a>數字

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>數字

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>算術運算子

|| |
|---|-------------|
| + | 新增         |
| - | 相減    |
| * | 將相乘    |
| / | 除數      |
| % | 模數      |
||
|`<` |降低
|`<=`|降低或等於
|`>` |大於
|`>=`|大於或等於
|`<>`|不等於
|`!=`|不等於 


### <a name="abs"></a>abs

**語法**

    abs(x)

**引數**

* x 的整數、 實數或時段

**傳回**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>回收筒 floor

將無條件捨去至整數值指定回收筒大小的倍數。 使用中的許多[`summarize by`](#summarize-operator)查詢。 如果您有分散的一組值時，他們都會分組到較小的一組特定值。

Alias （別名) `floor`。

**語法**

     bin(value, roundTo)
     floor(value, roundTo)

**引數**

* *值︰*數字、 日期或時段。 
* *roundTo:*「 回收筒大小]。 數字、 日期或會將*值*的時段。 

**傳回**

最接近的*roundTo* *值*下方。  
 
    (toint((value/roundTo)-0.5)) * roundTo

**範例**

運算式 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


下列運算式會計算直方圖的工期，1 的第二個值區大小︰

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>exp

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>會場

別名[`bin()`](#bin)。

### <a name="gamma"></a>gamma

[Gamma 函數](https://en.wikipedia.org/wiki/Gamma_function)

**語法**

    gamma(x)

**引數**

* *x:*實數

正數的整數， `gamma(x) == (x-1)!` ，例如`gamma(5) == 4 * 3 * 2 * 1`。

另請參閱[loggamma](#loggamma)。


### <a name="log"></a>記錄檔

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`應該實數 > 0。 否則，則傳回 null。

### <a name="loggamma"></a>loggamma


絕對值[伽瑪函數](#gamma)的自然對數。

**語法**

    loggamma(x)

**引數**

* *x:*實數


### <a name="rand"></a>rand

亂數產生器。

* `rand()`-0.0 到 1.0 之間的實數
* `rand(n)`-0 到 n-1 之間的整數




### <a name="sqrt"></a>sqrt

平方根函數。  

**語法**

    sqrt(x)

**引數**

* *x:*實數 > = 0。

**傳回**

* 正數的數字，例如`sqrt(x) * sqrt(x) == x`
* `null`如果引數是負數，或無法轉換成`real`值。 




### <a name="toint"></a>toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>日期及時間


[前](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [現在](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>日期及時間的常值

|||
---|---
**日期時間**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|時間永遠是以 utc 表示。 省略日期，可讓目前的時間。
`now()`|目前的時間。
`now(`-*時段*`)`|`now()-`*時段*
`ago(`*時段*`)`|`now()-`*時段*
**時段**|
`2d`|2 天
`1.5h`|1.5 小時 
`30m`|30 分鐘
`10s`|10 秒內
`0.1s`|0.1 的第二個
`100ms`| 100 毫秒
`10microsecond`|
`1tick`|了 100ns
`time("15 seconds")`|
`time("2")`| 2 天
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>日期及時間的運算式

運算式 |結果
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |降低
`<=`|降低或等於
`>` |大於
`>=`|大於或等於
`<>`|不等於
`!=`|不等於 




### <a name="ago"></a>之內

減去指定的時段從目前的 UTC 時鐘時間。 例如`now()`，此函數可重複使用陳述式中所參照的 UTC 時間都相同的所有例項。

**語法**

    ago(a_timespan)

**引數**

* *a_timespan*︰ 若要從目前的 UTC 時鐘時間減去的間隔 (`now()`)。

**傳回**

    now() - a_timespan

**範例**

具有過去小時內的時間戳記的所有資料列︰

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>datepart

    datepart("Day", datetime(2015-12-14)) == 14

會擷取指定的日期的整數部分。

**語法**

    datepart(part, datetime)

**引數**

* `part:String`-{「 Year 」"月份"，「 日期 」、 「 小時 」、 「 分 」、 「 第二 」、 「 毫秒 」、 「 微秒 」、 「 奈秒 」}
* `datetime`

**傳回**

長整數，代表指定的組件。


### <a name="dayofmonth"></a>dayofmonth

    dayofmonth(datetime("2016-05-15")) == 15 

月份的日期的序號。

**語法**

    dayofmonth(a_date)

**引數**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

整數天數後上述星期日、 為`timespan`。

**語法**

    dayofweek(a_date)

**引數**

* `a_date`: A `datetime`.

**傳回**

`timespan`後的前一個開頭午夜星期日、 下捨入為整數的天數。

**範例**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>dayofyear

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

一年的日期的序號。

**語法**

    dayofyear(a_date)

**引數**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday、 endofweek、 endofmonth endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth

從日期時間中取得的月數字 (1-12)。

**範例**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear

取得年日期時間。

**範例**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>現在

    now()
    now(-2d)

目前 UTC 時鐘的時間，您也可以選擇位移的指定的時段。 此函數可重複使用陳述式中，所參照的時間會相同的所有例項。

**語法**

    now([offset])

**引數**

* *位移︰*A `timespan`、 新增至目前 UTC 時鐘時間。 預設︰ 0。

**傳回**

為目前的 UTC 時鐘時間`datetime`。

    now() + offset

**範例**

決定間隔自所述詞識別的事件︰

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday、 startofweek、 startofmonth startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>todatetime

Alias （別名) `datetime()`。

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

檢查字串是否有效的日期︰

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan

Alias （別名) `timespan()`。

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

整數結果代表 ISO 8601 標準的週數字。 一週的第一天星期日，而一年的第一週是該年的第一個星期四包含的週。 （一年的最後一天因此可以包含一年的第 1 週的日子的部分或第一天最多可以包含部分週 52 或前一年的 53）。


## <a name="string"></a>字串

[countof](#countof) | [擷取](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [取代](#replace) | [分割](#split) | [strcat](#strcat) | [strlen](#strlen) | [子字串](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>字串串連

規則是 JavaScript 都一樣。

單一或雙報價字元，可能會以括字串。 

反斜線 (`\`) 用來逸出字元例如`\t`（索引標籤） `\n` （新行） 和封閉式引號字元的執行個體。

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>模糊的字串串連

模糊的字串串連是字串，輸出的字串 （例如，追蹤時） 時，將會遮住分析。 模糊程序會取代所有模糊的字元來開始 (`*`) 字元。

若要模糊的字串常值，在前面加上`h`或 「 H 」。 例如︰

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>字串比較

運算子|描述|區分大小寫|為 true 的範例
---|---|---|---
`==`|等於 |[是]| `"aBc" == "aBc"`
`<>` `!=`|不等於|[是]| `"abc" <> "ABC"`
`=~`|等於 |無| `"abc" =~ "ABC"`
`!~`|不等於 |無| `"aBc" !~ "xyz"`
`has`|右左手邊 (RHS) 是在左左手邊 (LHS) 的整個詞彙|無| `"North America" has "america"`
`!has`|RHS 不是在 LHS 完整字詞|無|`"North America" !has "amer"` 
`hasprefix`|RHS 是在 LHS 字詞的前置字元|無|`"North America" hasprefix "ame"`
`!hasprefix`|RHS 不是在 LHS 任何字詞的前置字元|無|`"North America" !hasprefix "mer"`
`hassuffix`|RHS 是在 LHS 字詞的尾碼|無|`"North America" hassuffix "rth"`
`!hassuffix`|無法在 LHS 任何字詞的尾碼 RHS。|無|`"North America" !hassuffix "mer"`
`contains` | RHS 發生 LHS 的子字串|無| `"FabriKam" contains "BRik"`
`!contains`| RHS 沒有出現在 LHS|無| `"Fabrikam" !contains "xyz"`
`containscs` | RHS 發生 LHS 的子字串|[是]| `"FabriKam" contains "Kam"`
`!containscs`| RHS 沒有出現在 LHS|[是]| `"Fabrikam" !contains "Kam"`
`startswith`|RHS 是 LHS 中的初始子字串。|無|`"Fabrikam" startswith "fab"`
`!startswith`|RHS 不是初始 LHS 的子字串。|無|`"Fabrikam" !startswith "abr"`
`endswith`|RHS 是 LHS 的終端子字串。|無|`"Fabrikam" endswith "kam"`
`!endswith`|RHS 不的 LHS 終端子字串。|無|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS 包含 RHS 的相符項目|[是]| `"Fabrikam" matches regex "b.*k"`
`in`|等於任何項目|[是]|`"abc" in ("123", "345", "abc")`
`!in`|不等於任何項目|[是]|`"bc" !in ("123", "345", "abc")`

使用`has`或`in`如果您正在測試的目前狀態的整個語彙字詞-也就是符號或英數字元的 word 所繫結非英數字元或開始或結束的欄位。 `has`執行較快， `contains`，`startswith`或`endswith`。 這些查詢中的第一個執行速度︰

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

計算子字串中的項的目。 純文字字串相符的項目可能會重疊。不要使用 regex 相符的項目。

**語法**

    countof(text, search [, kind])

**引數**

* *文字︰*字串。
* *搜尋︰*純文字字串或規則運算式以符合內*的文字*。
* *類型︰*`"normal"|"regex"`預設`normal`。 

**傳回**

搜尋字串可以相符容器中的次數。 純文字字串相符的項目可能會重疊。不要使用 regex 相符的項目。

**範例**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (不 2 ！)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>擷取

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

從文字字串中取得[規則運算式](#regular-expressions)的相符項目。 （選擇性），然後轉換解壓縮的子字串的指定類型。

**語法**

    extract(regex, captureGroup, text [, typeLiteral])

**引數**

* *regex:*[規則運算式](#regular-expressions)。
* *captureGroup:*正`int`常數，指出擷取擷取的群組。 0 代表整個相符，也就是 2 或更多的後續括號括住的規則運算式中的第一個 「 （' 括號'）' 符合的值為 1。
* *文字︰*A`string`搜尋。
* *typeLiteral:*選擇性輸入常值 (例如`typeof(long)`)。 如有提供解壓縮的子字串會轉換為這個類型。 

**傳回**

如果*regex*尋找相符的*文字*中︰ 子比對指定的擷取群組*captureGroup*，您也可以選擇將會轉換成*typeLiteral*。

如果沒有符合，或類型轉換失敗︰ `null`。 

**範例**

範例字串`Trace`定義搜尋`Duration`。 符合的項目會轉換為`real`，然後乘以時間常數 (`1s`)，讓`Duration`是類型`timespan`。 在此範例中，就等於 123.45 的秒數︰

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

此範例中等同於`substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>isempty isnotempty notempty

    isempty("") == true

如果引數為空字串或 null，則為 true。
另請參閱[isnull](#isnull)。


**語法**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**傳回**

指出是否空字串或 isnull 引數。

|x|isempty(x)
|---|---
| "" | true
|「 x 」 | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false


**範例**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl

分割 URL 其。

**語法**

    parseurl(urlstring)

**引數**

* *urlstring:*URL。

**傳回**

包含字串的組件的物件。

**範例**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>取代

取代另一個字串中的所有 regex 相符的項目。

**語法**

    replace(regex, rewrite, text)

**引數**

* *regex:*搜尋*文字*[運算式](https://github.com/google/re2/wiki/Syntax)。 它可以包含擷取群組 」 （'括號括住 」）'。 
* *重寫︰*取代 regex *matchingRegex*所做的任何符合項目。 使用`\0`至整個符合的項目，請參閱`\1`的第一個 [擷取] 群組中，`\2`後續擷取群組的等等。
* *文字︰*字串。

**傳回**

更換*regex*的所有相符項目與*改寫*的評估後的*文字*。 相符的項目不會重疊。

**範例**

此陳述式︰

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

具有下列結果︰

| x    | str | 取代|
|---|---|---|
| 1    | 數字是 1.000000  | 數字是︰ 1.000000|
| 2    | 數字是 2.000000  | 數字是︰ 2.000000|
| 3    | 數字是 3.000000  | 數字是︰ 3.000000|
| 4    | 數字是 4.000000  | 數字是︰ 4.000000|
| 5    | 數字是 5.000000  | 數字是︰ 5.000000|
 



### <a name="split"></a>分割

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

分隔指定的字串根據指定的分隔符號，並傳回字串陣列 conatined 子字串。 （選擇性） 特定的子字串可傳回的 if 存在。

**語法**

    split(source, delimiter [, requestedIndex])

**引數**

* *來源*︰ 會根據指定的分隔符號已分割的來源字串。
* *分隔符號*︰ 將會用來分割來源字串的分隔符號。
* *requestedIndex*︰ 選擇性起始索引`int`。 如有提供傳回的字串陣列會包含要求的子字串如果存在。 

**傳回**

字串陣列，其中包含指定的來源字串的以指定的分隔符號分隔的子字串。

**範例**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

介於 1 和 16 的引數，必須是字串串連。

### <a name="strlen"></a>strlen

    strlen("hello") == 5

字串的長度。

### <a name="substring"></a>子字串

    substring("abcdefg", 1, 2) == "bc"

從指定索引開始指定的來源字串中擷取子字串。 或者，您可以指定要求的子字串的長度。

**語法**

    substring(source, startingIndex [, length])

**引數**

* *來源︰*子字串來自來源字串。
* *startingIndex:*起始起始字元位置的要求的子字串。
* *長度︰*選擇性參數，可用來指定子字串中的 [要求的字元數。 

**傳回**

從指定的字串子字串。 子啟動 startingIndex （起始） 字元位置，並持續如果指定的結尾的字串或長度的字元。

**範例**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower

    tolower("HELLO") == "hello"

字串轉換為小寫。

### <a name="toupper"></a>toupper

    toupper("hello") == "HELLO"

字串轉換成大寫。



### <a name="guids"></a>Guid

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>陣列、 物件和動態

[串連](#dynamic-literals) | [轉換](#casting-dynamic-objects) | [運算子](#operators) | [讓子句](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [範圍](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)


以下是在應用程式的深入見解例外狀況查詢的結果。 中的值`details`是陣列。

![](./media/app-insights-analytics-reference/310.png)

**編製索引作業︰**陣列及一樣 JavaScript 的物件索引︰

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* 但是使用`arraylength`和其他分析函數 (不 「.length 」 ！)

**轉換**在某些情況下就必須轉換您擷取物件的項目，因為其類型例項。 例如，`summarize...to`需要特定類型︰

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**串連**若要建立的明確的陣列或屬性包物件，請為 JSON 字串和轉換撰寫︰

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:**若要拉開物件的屬性到另一個資料列，使用 mvexpand:

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath:**若要尋找複雜的物件中的所有路徑︰

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:**若要尋找 [准許加入表格中的運算式的所有值的最小結構描述︰

    exceptions | summarize buildschema(details)

結果︰

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

請注意，`indexer`用來標示您應該使用數值索引的位置。 此結構描述，就會 （假設這些範例索引範圍中） 一些有效路徑︰

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>陣列及物件的常值

若要建立動態常值，請使用`parsejson`(別名`todynamic`) 使用 JSON 字串引數︰

* `parsejson('[43, 21, 65]')`-數字陣列
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-單一值包含數字的動態型別
* `parsejson('"21"')`-單一值的動態包含字串的類型

請注意，跟 JavaScript] JSON 規定雙引號中的使用 (`"`) 字串。 因此，輕鬆通常報價 JSON 編碼的字串串連使用單引號 (`'`)。

此範例會建立動態值，然後再使用 [其欄位︰

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>動態物件函數

|||
|---|---|
| *值*`in` *陣列*| 如果*陣列*項目的則為 true 的 = =*值*<br/>`where City in ('London', 'Paris', 'Rome')`
| *值*`!in` *陣列*| 如果沒有*陣列*的項目，則為 true 的 = =*值*
|[`arraylength(`陣列`)`](#arraylength)| 如果不是陣列的 null
|[`extractjson(`路徑物件`)`](#extractjson)|使用瀏覽至物件的路徑。
|[`parsejson(`來源`)`](#parsejson)| 動態物件會變成 JSON 字串。
|[`range(`從]，步驟`)`](#range)| 值的陣列
|[`mvexpand`listColumn](#mvexpand-operator) | 會將複製的資料列中指定的儲存格的清單中的每個值。
|[`summarize buildschema(`資料行`)`](#buildschema) |推斷資料行的內容類型結構描述
|[`summarize makelist(`欄`)`](#makelist)| 簡維列的群組，並將資料行的值陣列中。
|[`summarize makeset(`資料行`)`](#makeset) | 簡維列的群組，並將資料行的值陣列，而不須複製中。

### <a name="dynamic-objects-in-let-clauses"></a>讓子句中的動態物件


[讓子句](#let-clause)市集動態值的字串，因此下列兩個子句的相同，並同時需要`parsejson`(或`todynamic`) 才能使用︰

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>arraylength

動態陣列中的元素數目。

**語法**

    arraylength(array)

**引數**

* *陣列︰*A`dynamic`值。

**傳回**

*陣列*中的元素數目或`null`如果*陣列*不是陣列。

**範例**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

取得指定的項目，不使用的路徑運算式 JSON 文字。 您也可以將擷取的字串轉換成特定類型中。


**語法**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**傳回**

此函數會將資料來源，其中包含有效 JSON 字串，您也可以將該值轉換為根據的第三個引數的另一個類型執行 JsonPath 查詢。



**範例**

[括號] 標記法和點標記法是相同的︰

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**效能的秘訣**

* 套用 where 子句，再使用`extractjson()`
* 請考慮使用[擷取](#extract)改為使用規則運算式相符項目。 這非常速度更快，可以執行，如果 JSON 會產生從範本更有效率。
* 使用`parsejson()`如果您需要從 JSON 擷取多個值。
* 不妨 JSON 剖析在 ingestion 宣告的欄類型是動態。

### <a name="json-path-expressions"></a>JSON 路徑運算式

|||
|---|---|
|`$`|根物件|
|`@`|目前的物件|
|`[0]`|陣列註標|
|`.`或`[0]` | 子|

*（我們不目前實作萬用字元、 遞迴、 union 或扇形區。）*




### <a name="parsejson"></a>parsejson

解譯`string`為[JSON 值](http://json.org/))，並傳回值`dynamic`。 它是使用`extractjson()`在需要時擷取 JSON 複合物件的多個項目。

**語法**

    parsejson(json)

**引數**

* *json:*JSON 文件。

**傳回**

物件類型的`dynamic`指定*json*。

**範例**

在下列範例中，當`context_custom_metrics`是`string`看起來像這樣︰ 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

下列片段擷取的值，然後`duration`物件，以及其位置中擷取兩個位置，`duration.value`和 `duration.min`(`118.0`和`110.0`分別)。

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>範圍

`range()`函數 (為無法與混淆`range`運算子) 會產生動態陣列拿著一系列的平均為單行間距值。

**語法**

    range(start, stop, step)

**引數**

* *開始︰*結果的陣列中的第一個元素的值。 
* *停止︰*結果的陣列中的最後一個元素的值或最低值大於最後一個項目中的結果的陣列，並為整數的*步驟*，從 [*開始*的倍數。
* *步驟︰*陣列的兩個連續的項目之間的差異。

**範例**

下列範例會傳回`[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

下列範例會傳回保留在 2015 年中的所有工作日的陣列︰

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

將字串轉換成動態的值。

### <a name="treepath"></a>treepath

    treepath(dynamic_object)

列舉識別樹葉的動態物件的所有路徑運算式。 

**傳回**

陣列的路徑運算式。

**範例**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

附註的 「 [0] 」 會指出目前的陣列，但未指定特定路徑所使用的索引。

### <a name="zip"></a>郵遞區號

    zip(list1, list2, ...)

將一組清單結合成 tuple 的一份清單中。

* `list1...`︰ 值的清單

**範例**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>名稱

名稱可以長度上限為 1024年字元。 他們會區分大小寫，可能包含字母、 數字和底線 (`_`)。 

報價名稱，使用 ['...'] or [" ...「] 包含其他字元，或使用關鍵字的名稱。 例如︰

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|[' 路徑\\file\n\'x\''] | 使用 \ 逸出字元|
|[「 d-e.=/f#\n 」] | |
|[@'path\file'] | 沒有逸出字元-\ 是常值|
|[@"\now& 然後\"] | |
|[位置] | 使用語言關鍵字的名稱|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


