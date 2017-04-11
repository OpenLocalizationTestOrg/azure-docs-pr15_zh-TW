<properties
    pageTitle="建立資料的功能中使用登錄區查詢 Hadoop 叢集 |Microsoft Azure"
    description="登錄區查詢中 Azure HDInsight Hadoop 叢集中儲存的資料產生功能的範例。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />


#<a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>使用登錄區查詢 Hadoop 叢集中建立資料的功能

這份文件會顯示如何建立使用登錄區查詢 Azure HDInsight Hadoop 叢集中儲存的資料的功能。 這些登錄區查詢使用內嵌登錄區使用者定義函數 (Udf)，提供的指令碼。

建立功能時所需的作業可能耗損記憶體。 登錄區查詢的效能會變得更重要在這種情況下，而且可改善調整特定參數。 最後一節中討論這些參數的調整。

所呈現的查詢範例是[NYC 計程車差旅費資料](http://chriswhong.com/open-data/foil_nyc_taxi/)特定案例也會提供在[Github 存放庫](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)。 這些查詢已經有指定的資料結構描述，並準備好提交執行。 在最後一個區段中，也會討論的使用者可以調整以便可改善效能的登錄區查詢參數。

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]說明如何建立資料的功能，在不同環境中的主題此**功能表**連結。 這項工作是[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的步驟。


## <a name="prerequisites"></a>必要條件
本文假設您有︰

* 建立 Azure 儲存體帳戶。 如果您需要的指示，請參閱[建立 Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)
* 佈建自訂的 Hadoop 叢集 HDInsight 服務。  如果您需要的指示，請參閱[進階分析自訂 Azure HDInsight Hadoop 叢集](machine-learning-data-science-customize-hadoop-cluster.md)。
* 資料已上傳至 Azure HDInsight Hadoop 叢集登錄區資料表。 如果沒有出現，請依循第一次上載登錄區資料表的資料[建立和載入登錄區資料表的資料](machine-learning-data-science-move-hive-tables.md)。
* 啟用遠端存取叢集。 如果您需要的指示，請參閱[存取 Hadoop 叢集不對節點](machine-learning-data-science-customize-hadoop-cluster.md#headnode)。


##<a name="hive-featureengineering"></a>功能產生器

在此區段中，說明多個的功能可以會產生使用登錄區查詢的方式的範例。 一旦您產生的其他功能，您可以將其新增至現有的資料表資料行，或建立新資料表的主索引鍵，然後可以加入與原始表格的其他功能。 以下是範例︰

1. [頻率根據功能產生器](#hive-frequencyfeature)
2. [類別中的變數二進位分類的風險](#hive-riskfeature)
3. [從日期時間] 欄位中擷取的功能](#hive-datefeatures)
4. [文字欄位中擷取的功能](#hive-textfeatures)
5. [計算的 GPS 座標之間的距離](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>頻率根據功能產生器

通常是幫助以計算分類變數的層級的頻率或多個類別的變數層級的特定組合的頻率。 使用者可以使用下列指令碼來計算下列頻率︰

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


###<a name="hive-riskfeature"></a>類別中的變數二進位分類的風險

二進位分類，我們需要將非數值分類變數轉換成數值的功能，當只使用模型執行數字的功能時。 這是每個非數值層級取代數字的風險。 在此區段中，我們會示範一些一般登錄區計算的查詢，分類的變數風險值 （記錄機率）。


        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

在此範例中，變數`smooth_param1`和`smooth_param2`設定為平滑導出資料的風險值。 風險有-Inf 和 Inf 之間的範圍。 風險 > 0 表示目標等於 1 的機率大於 0.5。

之後的風險被計算資料表，使用者可以指派風險值至資料表加入該資料表的風險。 在上一節中提供的登錄區結合查詢。

###<a name="hive-datefeatures"></a>從日期時間欄位擷取的功能

登錄區隨附一組 Udf 處理日期時間] 欄位。 在登錄區，預設的日期時間格式是 「 yyyy-MM-dd 00:00:00 」 (' 1970年-01-01 12:21:32 」，例如)。 在此區段中，我們會示範擷取的每個月、 從日期時間] 欄位中，將月份日期的範例和其他格式的日期時間字串轉換以外的日期時間字串的預設格式以預設格式的範例。

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

此登錄區查詢假設*& #60; datetime 欄位 >*是預設的日期時間格式。

如果 datetime 欄位不是預設的格式，您需要先將 datetime 欄位轉換成 Unix 時間戳記，然後再將 Unix 時間戳記轉換為 datetime 字串中的預設格式的。 當 datetime 預設格式，使用者可以套用內嵌的 datetime Udf 擷取的功能。

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

在查詢中，如果*& #60; datetime 欄位 >*等圖樣*03/26/2015年 12:04:39*、 *「 & #60; [日期時間] 欄位的圖樣 >'*應該`'MM/dd/yyyy HH:mm:ss'`。 若要測試，使用者可以執行

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

在這個查詢*hivesampletable*預先安裝上所有的 Azure HDInsight Hadoop 叢集預設時很佈建叢集。


###<a name="hive-textfeatures"></a>從 [文字] 欄位擷取的功能

當登錄區資料表具有包含的以空格分隔的文字字串的文字欄位時，下列查詢會擷取字串，以及文字字串中的數字的長度。

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>計算的 GPS 座標集之間的距離

本節中的查詢可以直接套用至 NYC 計程車差旅費資料。 此查詢的目的，顯示如何套用產生功能區中的內嵌數學函數。

在這個查詢中使用的欄位是收取和 dropoff 的位置，名為的 GPS 座標*收取\_經度*，*收取\_緯度*， *dropoff\_經度*，及*dropoff\_緯度*。 會計算直接收取和 dropoff 座標之間距離的查詢︰

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

計算兩個 GPS 座標之間的距離數學方程式可以找到<a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">可移動類型指令碼</a>網站上，由彼得 Lapisu 撰寫。 在他的 Javascript 函數`toRad()`為只*lat_or_lon*pi/180*，其中將度數轉換成弧度。在這裡， *lat_or_lon * 是緯度或經度。 由於登錄區並未提供此函數`atan2`，但會提供此函數`atan`、`atan2`函數由實作`atan`在上述登錄區查詢中使用<a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">維基百科</a>中提供的定義的函數。

![建立工作區](./media/machine-learning-data-science-create-features-hive/atan2new.png)

完整的清單內嵌的 Udf 可以<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache 登錄區 wiki</a>的**內建函數**] 區段中找到的登錄區）。  

## <a name="tuning"></a>進階主題︰ 微調登錄區參數，以改善查詢速度

預設的登錄區叢集參數設定可能不適合登錄區查詢和資料的查詢處理。 在此區段中，我們討論改善的登錄區查詢效能使用者可以調整某些參數。 使用者需要新增參數微調之前處理資料的查詢的查詢。

1. **Java 堆積空間**︰ 加入大型資料集，或處理長記錄的查詢，**堆積空間不足**的其中一項常見的錯誤。 這可以調整設定參數*mapreduce.map.java.opts*和*mapreduce.task.io.sort.mb*所要的值。 以下是範例︰

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;


    這個參數會配置 4 GB 的記憶體 Java 堆積空間，也可讓您排序更有效率，配置更多的記憶體。 最好玩這些配置，如果有任何工作失敗錯誤相關堆積空間。

2. **DFS 封鎖大小**︰ 此參數是設定檔案系統儲存的資料的最小的單位。 舉例來說，如果 DFS 區塊大小是 128 MB，然後大小的任何資料小於且最多 128 MB 就會儲存在單一區塊，超過 128 MB 分配額外的區塊的資料時。 選擇 [很小區塊大小會大的投影片中 Hadoop，因為名稱節點處理許多更多的要求，尋找相關的封鎖檔案的相關。 建議的設定時處理 gb （或較大） 的資料︰

        set dfs.block.size=128m;

3. **最佳化加入群組中的作業**︰ 通常縮減階段，有時候，會發生的地圖/縮減架構中的加入作業時可以達成大成果排程連接地圖階段 （也稱為 「 mapjoins 」）。 若要將登錄區執行此動作可能的話，我們可以設定︰

        set hive.auto.convert.join=true;

4. **指定登錄區要自行數目**︰ 時 Hadoop 可讓使用者設定 reducers 數、 自行數通常是由使用者設定。 秘訣，可讓某種程度的控制項這個數字是選擇 Hadoop 變數、 *mapred.min.split.size*和*mapred.max.split.size*每項工作的地圖的大小取決於︰

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    一般而言， *mapred.min.split.size*的預設值為的 0，這樣的*mapred.max.split.size* **Long.MAX**並*dfs.block.size*的是 64 MB。 我們可以看到，提供資料的大小，調整這些參數的 「 設定 」 其可讓我們微調自行所用的數目。

5. 以下將討論的一些其他更多**進階的選項**將登錄區效能最佳化。 這些允許您設定的記憶體配置對應，並減少工作，可以用來調整效能。 請記住， *mapreduce.reduce.memory.mb*不能大於 Hadoop 叢集每個工作者節點的實體記憶體大小。

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;
