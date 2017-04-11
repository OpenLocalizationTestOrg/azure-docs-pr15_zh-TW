<properties
    pageTitle="縮放比例自動計算 Azure 批次資料庫中的節點 |Microsoft Azure"
    description="啟用雲端集區動態調整計算資料庫中的節點數目自動縮放比例。"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>自動調整 Azure 批次資料庫中的運算節點

使用自動縮放比例，Azure 批次服務動態新增或移除運算節點，根據您所定義的參數資料庫中。 您可能可以儲存金錢與時間，會自動調整大小的應用程式時，所使用的計算 power 新增節點為您的工作工作要求增加，及他們減少時將其移除。

您啟用自動縮放比例運算節點的資料庫建立關聯，*自動調整大小公式*的定義，例如使用[PoolOperations.EnableAutoScale] [ net_enableautoscale] [批次.NET](batch-dotnet-get-started.md)文件庫中的方法。 批次服務然後使用公式來決定所需執行您的工作量運算節點數目。 批次回應服務計量定期收集的資料樣本，並調整運算根據您的公式為可設定間隔資料庫中的節點數目。

您可以啟用自動縮放比例建立資料庫時，或現有的資料庫。 您也可以變更現有的公式是 「 自動 」 調整大小，啟用集區。 批次提供之前將其指派給集區評估公式，以及監視的自動縮放比例的執行狀態的能力。

## <a name="automatic-scaling-formulas"></a>自動縮放比例的公式

自動的縮放比例公式是字串值您所定義包含一或多個陳述式，並指派給集區[autoScaleFormula] [rest_autoscaleformula]項目 （批次剩餘） 或[CloudPool.AutoScaleFormula] [net_cloudpool_autoscaleformula]屬性 (批次.NET)。 指派給資料庫時，批次服務使用公式來決定目標的數目運算節點集區中的下一個間隔的處理 （詳細說明更新版本的時間間隔）。 公式的字串不得超過 8 KB 的大小，可以包含最多 100 個陳述式，並以分號分隔，可以包含換行和註解]。

您可以將自動縮放比例的公式，例如使用批次自動調整大小 」 語言 」。 公式的陳述式是可以包含服務定義變數 （批次服務來定義的變數） 與使用者定義的變數 （由您定義的變數） 的形式運算式。 他們可以使用內建的類型、 運算子和函數來執行各種作業的這些值。 例如，陳述式可能需要下列格式︰

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

公式通常包含在執行作業會取得的值在先前的陳述式中的多個陳述式。 例如，在我們取得的第一次`variable1`，然後將它傳遞給函數來填入`variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

在公式中這些陳述式，與您的目標是到達集區應該--依比例縮放的運算節點的數字**專用的節點**的**目標**數目。 這個數字可能會更高，lower 或目前的資料庫中的節點數目相同。 批次在特定時間評估集區自動調整大小的公式 （[自動調整成時間間隔](#automatic-scaling-interval)討論下方）。 然後，將調整自動調整大小公式的評估次所指定的數字集區中的節點的目標數目。

快速範例，這兩行自動調整大小的公式會指定應該調整根據 [作用中的工作，上限為 10 的運算節點數目的節點數目︰

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

章節的本文討論各種實體自動調整大小公式，包括變數、 運算子、 作業和函數將組成。 您可以瞭解如何取得不同批次中的計算資源及工作矩陣。 您可以使用這些計量聰明地調整 [程式集區節點計數根據 [資源使用狀況與任務狀態。 然後，您將學習如何建立公式，並啟用自動縮放比例集區上使用的批次其餘和.NET Api。 我們會完成一些範例公式的儲存格。

> [AZURE.IMPORTANT] 每個 Azure 批次帳戶僅限於核心 （以及運算節點） 可用於處理的最大值數字。 批次服務將會建立只限核心限制的節點。 因此，它可能無法到達運算節點的目標數所指定的公式。 檢視及增加您帳戶的配額的詳細資訊，請參閱[配額和 Azure 批次服務的限制](batch-quota-limit.md)。

## <a name="variables"></a>變數

您可以自動調整大小公式中使用同時**服務定義**和**使用者定義**的變數。 服務已定義之變數的部分內建於批次服務-是讀寫和部分為唯讀。 使用者定義的變數是變數*您*所定義。 上述兩行範例公式中`$TargetDedicated`服務定義變數，同時`$averageActiveTaskCount`的使用者定義的變數。

下表顯示讀寫和唯讀狀態由批次服務所定義的變數。

您可以**取得**並**設定**這些服務定義的變數管理計算資料庫中的節點數目的值︰

| 讀寫服務定義的變數 | 描述 |
| --- | --- |
| $TargetDedicated | 集區的 [**固定運算節點**數目**目標**。 這是集區應該要依比例縮放的運算節點的數字。 因為可能集區不到目標的節點數目，則 「 目標 」 數字。 如果目標的節點數目修改再次過後續自動調整大小評估集區已送達初始目標之前，會發生這項目。 如果達到批次帳戶節點或核心配額時達到目標的節點數目之前時，它也可以發生。 |
| $NodeDeallocationOption | 從資料庫移除運算節點時所發生的動作。 可能的值為︰<ul><li>**將**-立即結束工作，並將它們重新開啟工作佇列中，讓他們會重新排程。<li>**終止**-立即結束工作，並從工作佇列中移除它們。<li>**taskcompletion**-等待目前執行工作完成，然後移除資料庫中的 [節點。<li>**retaineddata**-等待資料庫中移除節點之前，先清除節點上所有的工作保留本機資料。</ul> |

您可以**取得**指標批次服務為基礎的調整這些服務已定義之變數的值︰

| 唯讀服務定義的變數 | 描述 |
| --- | --- |
| $CPUPercent | 平均的 CPU 使用率百分比。 |
| $WallClockSeconds | 耗用的秒數。 |
| $MemoryBytes | 使用 mb 的平均數目。 |
| $DiskBytes | 使用本機磁碟上的 gb 的平均數目。 |
| $DiskReadBytes | 讀取的位元組數。 |
| $DiskWriteBytes | 寫入的位元組數。 |
| $DiskReadOps | 了解的磁碟執行的作業計數。 |
| $DiskWriteOps | 執行寫入磁碟作業的計數。 |
| $NetworkInBytes | 輸入的位元組數。 |
| $NetworkOutBytes | 輸出的位元組數。 |
| $SampleNodeCount | 運算節點數目。 |
| $ActiveTasks | 在 [作用中狀態的任務數量。 |
| $RunningTasks | 在執行狀態中的任務數量。 |
| $PendingTasks | $ActiveTasks 和 $RunningTasks 的總和。 |
| $SucceededTasks | 已順利完成的任務數量。 |
| $FailedTasks | 失敗的任務數量。 |
| $CurrentDedicated | 目前的專用數計算節點。 |

> [AZURE.TIP] 唯讀且服務定義變數上方顯示的是提供各種不同的方法來存取相關聯的每個資料的*物件*。 如需詳細資訊，請參閱下方的[取得範例資料](#getsampledata)。

## <a name="types"></a>類型

在公式中支援這些**類型**。

- 雙引號
- doubleVec
- doubleVecList
- 字串
- 時間戳記，時間戳記是複合結構，包含下列的成員︰

    - 年
    - 月 (1-12)
    - 一天 (1-31)
    - weekday （在數字，例如 1 代表星期一格式）
    - 小時 （在 24 小時制的數字格式，例如 13 表示下午 1）
    - 分鐘 (00 59)
    - 第二個 (00 59)
- timeinterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>作業

上述類型允許這些**作業**。

| 作業                             | 支援的運算子   | 結果類型   |
| ------------------------------------- | --------------------- | ------------- |
| 雙*運算子*雙              | +, -, *, /            | 雙引號            |
| 雙*運算子*timeinterval        | *                     | timeinterval      |
| doubleVec*運算子*雙           | +, -, *, /            | doubleVec         |
| doubleVec*運算子*doubleVec        | +, -, *, /            | doubleVec         |
| timeinterval*運算子*雙        | *, /                  | timeinterval      |
| timeinterval*運算子*timeinterval  | +, -                  | timeinterval      |
| timeinterval*運算子*時間戳記     | +                     | 時間戳記         |
| 時間戳記*運算子*timeinterval     | +                     | 時間戳記         |
| 時間戳記*運算子*時間戳記        | -                     | timeinterval      |
| *運算子*雙                      | -, !                  | 雙引號            |
| *運算子*timeinterval                | -                     | timeinterval      |
| 雙*運算子*雙              | < < =、 = =、 > =、 >，！ =  | 雙引號            |
| 字串*運算子*字串              | < < =、 = =、 > =、 >，！ =  | 雙引號            |
| 時間戳記*運算子*時間戳記        | < < =、 = =、 > =、 >，！ =  | 雙引號            |
| timeinterval*運算子*timeinterval  | < < =、 = =、 > =、 >，！ =  | 雙引號            |
| 雙*運算子*雙              | & &、 & #124; & #124;      | 雙引號            |

當測試 double，以三元運算子 (`double ? statement1 : statement2`)，非零值為**true**，且零為**false**。

## <a name="functions"></a>函數

這些預先定義的**函數**，可讓您定義自動的縮放比例公式中使用。

| 函數                          | 傳回類型   | 描述
| --------------------------------- | ------------- | --------- |
| avg(doubleVecList)                | 雙引號        | 傳回 doubleVecList 中的所有值的平均值。
| len(doubleVecList)                | 雙引號        | 傳回從 doubleVecList 建立向量的長度。
| lg(double)                        | 雙引號        | 會傳回底數 2 的點兩下登入。
| lg(doubleVecList)                 | doubleVec     | 會傳回基底 2 的 doubleVecList componentwise 記錄。 Vec(double) 明確必須傳遞的參數。 否則，則假設雙 lg(double) 版本。
| ln(double)                        | 雙引號        | 傳回雙的自然記錄檔。
| ln(doubleVecList)                 | doubleVec     | 會傳回基底 2 的 doubleVecList componentwise 記錄。 Vec(double) 明確必須傳遞的參數。 否則，則假設雙 lg(double) 版本。
| log(double)                       | 雙引號        | 會傳回底數 10 點兩下的登入。
| log(doubleVecList)                | doubleVec     | 傳回 componentwise 記錄的 doubleVecList 底數 10。 Vec(double) 明確必須傳遞單一雙參數。 否則，則假設雙 log(double) 版本。
| max(doubleVecList)                | 雙引號        | 在 [doubleVecList 傳回最大值。
| min(doubleVecList)                | 雙引號        | 在 [doubleVecList 傳回最小值。
| norm(doubleVecList)               | 雙引號        | 傳回從 doubleVecList 建立向量兩個一般。
| 百分位數 (doubleVec v，雙 p) | 雙引號        | 傳回向量 v 的百分位數項目。
| rand                            | 雙引號        | 傳回 0.0 到 1.0 之間的隨機值。
| range(doubleVecList)              | 雙引號        | 傳回 doubleVecList 最小值與最大值之間的差異。
| std(doubleVecList)                | 雙引號        | 傳回 doubleVecList 樣本標準差的值。
| 了 stop （)                            |               | 停止自動縮放運算式的評估。
| sum(doubleVecList)                | 雙引號        | 傳回 doubleVecList 的所有元素的總和。
| 時間 (字串 dateTime ="")          | 時間戳記     | 如果傳遞，則傳回目前的時間，如果沒有參數傳遞的時間戳記或日期時間字串的時間戳記。 支援的日期時間格式是 W3C DTF 和 RFC 1123。
| val (doubleVec v，雙 i)        | 雙引號        | 傳回值在位置 i 向量 v，在具有零開始索引的項目。

某些功能以上表格中所述可以接受清單做為引數。 逗點分隔清單是*雙*和*doubleVec*的任何組合。 例如︰

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList*值會轉換成單一*doubleVec*評估之前。 例如，如果`v = [1,2,3]`，然後呼叫`avg(v)`相當於來電`avg(1,2,3)`。 呼叫`avg(v, 7)`相當於來電`avg(1,2,3,7)`。

## <a name="getsampledata"></a>取得範例資料

自動調整大小的公式可指標資料 （範例） 所提供的批次服務。 公式會增大，或縮小，它會取得服務的數值為根據的資料庫大小。 服務定義變數上方所描述的是提供存取與該物件相關聯的資料的各種方法的物件。 例如，下列運算式會顯示要求取得的 CPU 使用率最後五分鐘︰

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| 方法 | 描述 |
| --- | --- |
| GetSample() | `GetSample()`方法會傳回的資料範例向量。<br/><br/>樣本是 30 秒值得指標資料。 換句話說，範例會取得每隔 30 秒。 但是如下所示，當範例會收集與時才能使用公式之間的延遲。 因此，在指定的期間內的不是所有範例都可能不適用於評估公式。<ul><li>`doubleVec GetSample(double count)`<br/>指定的範例，以取得最新的樣本已收集的數目。<br/><br/>`GetSample(1)`傳回最後一個可用的範例。 指標等`$CPUPercent`，不過，這不應該使用因為無法知道*時*收集樣本。 可能最近，或因為系統問題，可能是較舊。 最好是在這種情況下，若要使用的時間間隔，如下所示。<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>指定時間範圍收集範例資料。 或者，它也會指定必須能夠使用要求的時間範圍中的範例的百分比。<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`如果出現 CPUPercent 歷程記錄中的最後一個的十分鐘內的所有範例，則會傳回 20 範例。 如果無法使用 [歷程記錄的最後一分鐘，不過，會傳回只有 18 範例。 在此例中︰<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`就會失敗，因為只 90 百分比的範例可供使用。<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`會失敗。<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>指定時間範圍來收集資料，並同時開始時間和結束時間。<br/><br/>如上所述，有範例會收集時與時才能使用公式之間的延遲。 這必須考慮當您使用`GetSample`方法。 請參閱`GetSamplePercent`下方。|
| GetSamplePeriod() | 傳回歷史範例資料集的拍攝的範例。 |
| Count （) | 傳回範例的總數公制歷程記錄中。 |
| HistoryBeginTime() | 傳回最早可用的資料範例的公制的時間的戳記。 |
| GetSamplePercent() |傳回範例，可針對指定的時間間隔的百分比。 例如︰<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>因為`GetSample`如果為時傳回的樣本百分比小於`samplePercent`指定，您可以使用`GetSamplePercent`第一次檢查的方法。 然後您可以執行其他動作，如果沒有足夠的範例，而不停止自動縮放比例評估。|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>範例、 範例百分比與*GetSample()*方法

自動調整大小公式的核心作業是取得任務和資源單位的資料，然後調整 [依資料集區的大小。 因此，請務必清除瞭解如何自動調整大小公式互動指標的資料或 「 範例 」。

**範例**

批次服務定期採用的任務和資源的標準的*範例*，並可使用自動調整大小的公式。 下列範例會記錄每隔 30 秒批次服務。 然而，有通常是導致延遲之間記錄的樣本時，以及當使用者時，可用於 （可以讀取） 一些延遲自動調整大小公式。 此外，因為網路或其他基礎結構問題等各種因素，範例可能不會記錄針對特定的間隔。 這會導致 「 遺失 」 的範例。

**範例百分比**

當`samplePercent`傳遞給`GetSample()`方法或`GetSamplePercent()`呼叫方法時，「 百分比 」 指的是總數的*可能*會記錄批次服務的範例與實際*可用*至自動調整大小的公式範例數目之間的比較。

讓我們來看看 10 分鐘時段作為範例。 因為範例記錄 10 分鐘時段，內的每 30 秒的最大的總數所記錄的批次的範例是 20 範例 (每分鐘 2)。 不過，由於報告機制或一些其他問題 Azure 基礎結構中的既有的延遲，可能會只 15 可讀取您自動調整大小的公式的範例。 這表示，該 10 分鐘期間，只**75%**的總數錄製的範例可實際公式。

**GetSample() 與範例範圍**

自動調整大小公式將會放大或縮小您的資料庫，新增節點或移除節點。 節點成本金錢，因為您想要確保您的公式使用足夠的資料為基礎的分析的智慧方法。 因此，建議您在公式中使用感類型分析。 此類型會變大和縮小根據收集範例*範圍*集區。

若要這麼做，請使用`GetSample(interval look-back start, interval look-back end)`傳回**向量**的範例︰

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

批次來評估上述文字時，它會傳回範圍範例稱為向量的值。 例如︰

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

一旦您所收集到的範例向量，您可以使用函數，例如`min()`， `max()`，及`avg()`衍生收集範圍有意義的值。

獲得額外的安全性，您可以強制*失敗*的公式評估，如果小於特定範例百分比是在特定期間內使用。 強制失敗表示式時，您可以指示批次，停止再次評估公式如果無法使用，指定的百分比的範例，會建立資料庫大小不變。 若要指定必要的百分比的範例成功評估，請將其指定為第三個參數`GetSample()`。 在這裡，指定的範例 75%的需求︰

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

也很重要，因為先前所述的延遲的範例顯示狀態，使其永遠一分鐘較舊的外觀返回開始時間中指定的時間範圍。 這是因為需要花大約一分鐘的範例，才會傳播到系統，因此範圍中範例`(0 * TimeInterval_Second, 60 * TimeInterval_Second)`通常不會提供。 同樣地，您可以使用的百分比參數`GetSample()`強制特定範例百分比需求。

> [AZURE.IMPORTANT] 我們**強烈建議**，您*只*避免依賴**上`GetSample(1)`中您自動調整大小公式 * *。 這是因為`GetSample(1)`基本上指出批次服務中，[讓我有，無論您使用的多久了最後一個範例。 」 因為它是單一樣本，可能是較舊的範例，它可能無法代表的最近的任務或資源狀態的較大的圖片。 如果您使用`GetSample(1)`，請確定它是較大的陳述式，並不只資料點的公式所依賴的一部分。

## <a name="metrics"></a>指標

當您定義公式時，您可以使用同時**資源**及**工作**的指標。 您調整專用的節點，根據您所取得並評估的指標資料集區中的目標數目。 在每個度量，請參閱[變數](#variables)前的一節，如需詳細資訊。

<table>
  <tr>
    <th>公制</th>
    <th>描述</th>
  </tr>
  <tr>
    <td><b>資源</b></td>
    <td><p><b>資源指標</b>根據 CPU、 頻寬和記憶體使用量運算節點，以及的節點數目。</p>
        <p> 這些服務定義變數是很適合用於調整根據節點計算︰</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>這些服務定義變數是很適合用於調整根據節點資源使用狀況︰</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>任務</b></td>
    <td><p><b>任務指標</b>會根據擱置中的任務狀態，例如使用中時，以及完成。 下列服務定義變數是用於資料庫大小調整根據任務指標︰</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>撰寫自動調整大小的公式

藉由形成陳述式，使用上述元件，來建立自動調整大小公式，然後合併這些陳述式完成公式。 在此區段中，我們會建立範例自動調整大小公式可以執行的一些實際的縮放比例決策。

首先，讓我們來定義我們新的自動調整大小公式的需求。 公式應該︰

1. **增加**目標計算中的節點數目如果 CPU 使用率較高的集區。
2. **減少**目標計算中的節點數目的資料庫時的 CPU 使用率太低。
3. 永遠限制節點的數目**上限**為 400。

若要*增加*高 CPU 使用率期間的節點數目，我們定義會填入 [使用者定義的變數陳述式 (`$totalNodes`) 的節點，但是只有目前的目標數字的 110%的值的最小值的平均 CPU 使用率最後 10 分鐘的時間期間所上方 [70%。 否則，我們會使用目前的專屬的值。

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

若要*減少*的 CPU 使用率太低期間的節點數目，我們的公式中的下一個陳述式設定相同`$totalNodes`變數目前目標的節點數目過去的 60 分鐘的平均 CPU 使用率屬性的狀況下 20%的 90%。 使用目前的值，否則`$totalNodes`我們填入上述陳述式中。

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

現在限制目標的數目**上限**為 400 專用的運算節點︰

```
$TargetDedicated = min(400, $totalNodes)
```

以下是完成的公式︰

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>建立已啟用自動調整大小的集區

若要啟用自動縮放與建立新的資料庫，您可以使用下列技巧其中一項︰

**批次.NET**

1. 建立[BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx)集區。
1. 將[CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx)屬性設定為`true`。
1. 設定自動調整大小公式[CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx)屬性]。
1. （選用）設定[CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx)屬性 （預設為 15 分鐘）。
1. 確認[CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx)或[CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx)資料庫。

**批次 REST API**

* [增益集區帳戶](https://msdn.microsoft.com/library/azure/dn820174.aspx)︰ 指定`enableAutoScale`和`autoScaleFormula`在 REST API 邀請中設定自動縮放比例集區您所建立的項目。

下列程式碼片段會建立已啟用自動調整大小的集區使用[批次.NET] [net_api]文件庫。 集區的自動調整大小的公式會從星期一，5 節點目標數] 及 [一週的一天的 1。 [自動縮放比例的間隔時間](#automatic-scaling-interval)設定為 30 分鐘。 在此和其他 C# 程式碼片段本文中的，「 myBatchClient 」 是執行個體正常初始化的[BatchClient][net_batchclient]。

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

除了批次 REST API 和.NET SDK，您可以使用 [自動縮放使用任何其他[批次 Sdk](batch-technical-overview.md#batch-development-apis)、[批次 PowerShell cmdlet](batch-powershell-cmdlets-get-started.md)及[批次 CLI](batch-cli-get-started.md) 。

> [AZURE.IMPORTANT] 當您建立已啟用自動調整大小的資料庫時，您必須**不**指定`targetDedicated`參數。 此外，如果您想要以手動方式調整大小的已啟用自動調整大小的集區 (例如，使用[BatchClient.PoolOperations.ResizePool][net_poolops_resizepool])，則您必須第一個**停用**自動調整集區，然後調整其大小。

### <a name="automatic-scaling-interval"></a>自動縮放比例的間隔時間

根據預設，批次服務會調整每**15 分鐘**集區的大小，根據其自動調整大小的公式。 這段是可設定，不過，使用下列集區內容︰

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] （批次.NET）
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST API)

最小的間隔五分鐘，而最大值為 168 小時。 如果指定的間隔，此範圍，則批次服務會傳回錯誤的要求 (400) 錯誤。

> [AZURE.NOTE] 自動縮放目前並非回應變更一分鐘，但而要調整逐漸當您執行的工作負載的程式集區的大小。

## <a name="enable-autoscaling-on-an-existing-pool"></a>啟用自動縮放上現有的資料庫

如果您已經建立資料庫的運算節點以設定數字使用*targetDedicated*參數，您仍然可以啟用自動縮放集區。 每個批次 SDK 會提供 「 啟用自動調整大小] 作業，例如︰

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] （批次.NET）
*  [啟用自動縮放比例集區][ rest_enableautoscale] (REST API)

當您啟用自動縮放上現有的資料庫時，下列適用於︰

* 如果自動調整為目前集區上的 [**停用**您發行 」 啟用自動調整大小 」 的要求，您*必須*指定有效自動調整大小公式，當您發出邀請。 您*也*可以指定自動調整大小評估時間間隔。 如果您未指定時間間隔，則會使用 15 分鐘的預設值。

* 如果集區上的 [**啟用**自動調整大小是目前，您可以指定在自動調整大小公式、 評估時間間隔，或兩者。 您無法略過這兩個屬性。

  * 如果您指定新的自動調整大小評估間隔，然後停止現有的評估排程，並開始新的排程。 新的排程開始時間是 「 啟用自動調整大小 」 要求發出的時間。

  * 如果您省略自動調整大小公式或評估間隔，批次服務會繼續使用該設定的目前值。

> [AZURE.NOTE] 如果建立資料庫時*targetDedicated*參數指定的值，則會忽略評估時自動縮放比例的公式。

此 C# 程式碼片段使用[批次.NET] [net_api]文件庫啟用自動縮放上現有的資料庫︰

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>更新自動調整大小的公式

您使用 [現有的已啟用自動調整大小的集區上的 [相同] 啟用自動調整大小 」 邀請*更新*公式 (例如，使用[EnableAutoScale] [net_enableautoscale]批次.NET 中)。 有任何特殊的 「 更新自動調整大小] 作業。 例如，如果自動縮放已啟用 「 myexistingpool 」 上執行下列程式碼時，其自動調整大小的公式會取代的內容`myNewFormula`。

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>更新自動調整大小的間隔時間

當更新自動調整大小的公式，您可以使用相同的[EnableAutoScale] [net_enableautoscale]若要變更 [自動調整大小評估間隔的現有的已啟用自動調整大小的資料庫的方法。 例如，若要設定自動調整大小評估間隔 60 分鐘的已啟用自動調整大小的集區︰

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>評估為自動調整大小的公式

您可以將它套用至資料庫之前評估公式。 如此一來，您可以執行 [測試執行] 以查看其陳述式之前將公式放入生產的評估值的公式。

評估為自動調整大小的公式，您必須第一個**啟用自動縮放**以**有效的公式**集區。 如果您想要測試還沒有啟用自動縮放集區上的公式，您可以使用一行公式`$TargetDedicated = 0`當您第一次啟用自動縮放。 然後，請使用下列其中一來評估您想要測試的公式︰

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx)或[EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    批次.NET 種方法要求評估現有的資料庫，並包含自動調整大小公式的字串的識別碼。 評估結果都包含在傳回的[AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx)執行個體。

* [評估為自動縮放比例的公式](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    在此 REST API 邀請中，指定 URI，在資料庫識別碼] 和 [自動調整大小公式*autoScaleFormula*元素的邀請本文中。 作業的回應包含任何可能與公式有關的錯誤資訊。

在此[批次.NET] [net_api]程式碼片段，我們會評估公式之前將其套用至[CloudPool][net_cloudpool]。 如果資料庫沒有啟用自動縮放，我們將它啟用第一次。

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

成功評估此程式碼片段的公式將會導致輸出如下︰

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>取得執行自動調整大小的相關資訊

若要確保您的公式執行如預期般，我們建議您定期檢查自動縮放 」 執行 「 批次執行程式集區的結果。 執行，因此，取得 （或重新整理） 的參考集區，並檢查其自動執行的最後一個調整大小的屬性。

在批次.NET [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx)屬性會有數個屬性提供最新自動縮放比例執行上執行的資料庫批次服務的相關資訊。

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

REST API，[取得資料庫的相關資訊](https://msdn.microsoft.com/library/dn820165.aspx)要求會傳回包含最新自動縮放比例[autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun)中執行資訊的集區的相關資訊。

下列 C# 程式碼片段會使用批次.NET 文件庫來列印資料庫 」 myPool 」 上執行的最後一個自動縮放的相關資訊︰

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

輸出前面的程式碼片段的範例︰

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>自動調整大小公式範例

讓我們來看一下顯示不同的方式來調整的資料庫中的計算資源的幾個公式。

### <a name="example-1-time-based-adjustment"></a>範例 1︰ 以時間為基礎的調整

或許您要調整根據一週的日期和時間，以增加或減少集區中的節點的數據此資料庫大小。

此公式會先取得目前的時間。 如果是工作天 (1-5)，然後 (8 AM 至 6 的 PM) 的工作時間內目標集區的大小會設定為 20 節點中。 否則，請將其設定為 10 的節點。

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>範例 2︰ 工作式調整

在此範例中，集區的大小調整根據佇列中的任務數量。 請注意，是可接受公式字串中的註解及換行。

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>範例 3: Accounting 平行任務

這是會根據任務數量的資料庫大小調整的另一個範例。 此公式也會考慮[MaxTasksPerComputeNode] [net_maxtasks]已設定為集區的值。 這是在[執行平行工作](batch-parallel-node-tasks.md)已啟用您集區的情況下特別有用。

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>範例 4︰ 設定初始資料庫大小

此範例顯示 C# 程式碼片段設定的初始階段期間節點數目資料庫大小的自動調整大小公式。 然後，調整資料庫大小根據執行數與作用中的初始階段期間經過後的工作。

在下列程式碼片段公式︰

- 設定的初始資料庫大小四個節點。
- 不需要的集區生命週期的第一個 10 分鐘內調整集區的大小。
- 10 分鐘的時間之後, 會取得數的最大值在過去的 60 分鐘內執行與使用中工作。
  - 如果這兩個值 0 （表示沒有工作已執行或使用中的最後一個 60 分鐘），資料庫大小是設定為 0。
  - 如果任一個值大於零，不會變更。

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>後續步驟

* [最大化 Azure 批次計算與同時節點任務的資源使用狀況](batch-parallel-node-tasks.md)包含如何您可以執行多個任務同時在您的資料庫中的計算節點的詳細資料。 除了自動縮放，這項功能可以協助降低工作的一些負載，儲存您金錢的持續時間。

* 針對其他效率提升器，確定批次應用程式的查詢批次服務的最佳方式。 在[有效率地查詢 Azure 批次服務](batch-efficient-list-queries.md)，您將學習如何限制的交叉線，當您在查詢的潛在數以千計的運算節點或工作狀態的資料量。

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
