<properties
    pageTitle="進階資料瀏覽和火花與模型 |Microsoft Azure"
    description="使用 HDInsight 火花執行資料探索及進行訓練的建議使用跨驗證和 hyperparameter 最佳化的二進位分類和迴歸模型。"
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
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="advanced-data-exploration-and-modeling-with-spark"></a>進階的資料探索和火花與模型 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

這個逐步解說使用 HDInsight 火花執行資料探索和訓練二進位分類和迴歸模型使用跨驗證並 NYC 的樣本 hyperparameter 最佳化計程車差旅費最好 2013年資料集。 它會引導您完成[資料科學程序](http://aka.ms/datascienceprocess)的步驟、 HDInsight 火花使用叢集端對端處理和 Azure blob 儲存資料和模型。 程序瀏覽文章引進從 Azure 儲存體 Blob 的資料，並準備資料來建立預測模型。 已使用 Python 程式碼解決方案，並顯示相關的繪圖。 這些模型是使用火花 MLlib 工具組執行二進位分類和迴歸模型工作的建立。 

- **二進位分類**工作是預測所支付的出差行程井然有序的提示。 
- **迴歸分析**任務是秘訣的預測根據其他秘訣功能。 

模型步驟也包含顯示如何訓練、 評估，和儲存每個類型的模型的程式碼。 主題會說明部分的[資料探索和火花與模型](machine-learning-data-science-spark-data-exploration-modeling.md)主題為相同的範圍。 但其為 「 進階 」，它也使用跨驗證搭配使用 hyperparameter 全面訓練最佳正確的分類和迴歸模型。 

**跨驗證 （cv） 除以**是技術的評估方式訓練上一組已知的資料模型可以產生來預測在它尚未訓練資料集的功能。 這個方法的一般概念是模型訓練資料集已知上的資料上，並針對獨立的資料集然後測試的預測精確度。 使用通用實作是分成 K 將資料集，然後訓練中所有但其中一項動作遞迴方式的模型。 

選擇一組 hyperparameters 學習演算法，通常與最佳化演算法獨立的資料集的量值的目標的問題是效能的**Hyperparameter 最佳化**。 **Hyperparameters**是以外的模型訓練程序，必須指定的值。 假設這些值可能會影響的彈性與模型的精確度。 決策樹有 hyperparameters，例如，例如所要的深度和樹葉樹狀目錄中的數字。 支援向量機器 (SVMs) 需要設定 misclassification 負面影響字詞。 

若要執行以下使用 hyperparameter 最佳化常見的方式是格線搜尋或**參數整理**。 這包含學習演算法執行完整搜尋值 hyperparameter 空間的指定子集合。 跨驗證可以提供效能公制出最佳的結果所產生的格線搜尋演算法的排序。 CV 搭配 hyperparameter 廣泛協助限制問題，例如 overfitting 訓練資料模型，以便模型保留套用至的一般資料要從中擷取訓練資料集的容量。

我們會使用模型包含後勤和線性迴歸、 隨機樹系及漸層提高樹︰

- [使用 SGD 線性迴歸](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD)使用 Stochastic 漸層深度 (SGD) 方法的線性迴歸模型，而縮放預測提示金額支付用於最佳化與功能。 
- [使用 LBFGS 後勤迴歸](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS)或 「 logit 」 迴歸分析，是分類執行資料分類因變數時，可以使用的迴歸模型。 LBFGS 是牛頓最佳化演算法的估計使用一段有限的電腦記憶體 Broyden – Fletcher – Goldfarb – Shanno (BFGS) 演算法及的廣泛用於電腦學習。
- [隨機樹系](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests)是 ensembles 的決策樹。  結合來降低風險的膨脹許多決策樹。 隨機樹系的迴歸分析及分類可以處理分類的功能，並可延伸到 multiclass 分類設定。 他們不需要功能縮放比例，並且可以擷取非 linearities 和功能互動。 隨機樹系是最成功電腦學習分類和迴歸模型。
- [漸層提高樹狀結構](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)(GBTs) 是 ensembles 的決策樹。 GBTs 訓練反覆以縮小的遺失函數的決策樹。 GBTs 的迴歸分析及分類可以處理分類的功能，不需要縮放功能，並可以擷取非 linearities 和功能互動。 他們也可在 multiclass 分類設定。

模型範例使用 CV 和 Hyperparameter 整理顯示二進位分類問題。 簡單範例 （不參數的掃掠） 會顯示在主要迴歸分析任務的主題。 但附錄，驗證使用的線性迴歸和 CV 的彈性的網路，使用參數整理隨機樹系迴歸也會顯示。 **彈性的網路**是的放置模型線性迴歸直線的[套索](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29)和[立體浮凸](https://en.wikipedia.org/wiki/Tikhonov_regularization)方法損失為結合 L1 和 L2 指標 regularized 迴歸分析方法。   



>[AZURE.NOTE] 雖然火花 MLlib 工具組設計用來處理大型資料集，較小的範例 (使用 170 K 列關於原始 NYC 資料集的 0.1%~ 30 Mb) 用於以下便利性。 有效率地 （在 10 分鐘） 的以下練習是 2 的工作者節點的 HDInsight 叢集上執行。 相同的程式碼，稍微修改，可用來處理較大資料集，以適當的修改快取中記憶體的資料，及變更叢集大小。


## <a name="prerequisites"></a>必要條件

您需要 Azure 帳戶和 HDInsight 火花您需要 HDInsight 3.4 火花 1.6 叢集完成此逐步解說。 如何符合這些需求，請參閱[概觀資料科學使用火花 Azure HDInsight 上](machine-learning-data-science-spark-overview.md)的指示。 該主題也包含以下所用的 NYC 2013 計程車資料和指示 Jupyter 筆記本火花叢集上執行程式碼的描述。 本主題中的程式碼範例**pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb**筆記本中會有[Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)。


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>安裝︰ 儲存位置、 文件庫，預設的火花內容

火花都可以讀取和寫入 Azure 儲存體 Blob (也稱為 WASB)。 因此您現有的資料的任何儲存那里可以處理使用火花和 WASB 中一次儲存的結果。

若要儲存 WASB 模型或檔案，必須正確地指定路徑。 預設的容器附加至火花叢集可以使用路徑開頭為的參考: 「 wasb: / / 」。 其他位置所參考 」 wasb: / / 」。

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>在 [WASB 中設定目錄的儲存位置的路徑

下列程式碼範例指定的資料供讀取及儲存模型輸出模型儲存目錄的路徑的位置︰

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**輸出**

datetime.datetime (2016年 4、 18、 17、 36、 27、 832799)


### <a name="import-libraries"></a>匯入文件庫

匯入所需的文件庫，下列程式碼︰

    # LOAD PYSPARK LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime
    

### <a name="preset-spark-context-and-pyspark-magics"></a>預設火花內容和 PySpark 我們

Jupyter 筆記本會提供 PySpark 核心有預設的內容。 因此您不需要設定盡如或開發您開始使用應用程式之前，明確的登錄區內容。 這些內容，可讓您依預設。 這些內容是︰

- sc-的火花 
- sqlContext-的登錄區

PySpark 核心提供預先定義的 「 我們 」，這是您可以使用呼叫的特殊命令 %。 有兩個這類這些程式碼範例中所使用的命令。

- **%本機**指定要在本機上執行的後續幾行中的程式碼。 程式碼，必須是有效的 Python 程式碼。
- **%%sql -o <variable name>**執行 sqlContext 登錄區查詢。 如果 command 和 o 參數傳遞，查詢的結果就會保存在 %熊 DataFrame 本機 Python 內容。
 

如需更多有關 Jupyter 筆記本和預先定義的核心 」 magics 」 的他們提供，請參閱[適用於 Jupyter 的筆記本，與 HDInsight HDInsight 火花 Linux 叢集核心](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md)。


## <a name="data-ingestion-from-public-blob"></a>從公用 blob 資料 ingestion: 

資料科學程序中的第一個步驟是內嵌的資料來源的所在到您的資料瀏覽和模型環境進行分析。 此環境是火花在此逐步解說。 本節中的程式碼，完成一系列的任務︰

- 內嵌資料模型範例
- 閱讀中輸入資料集 （儲存為.tsv 檔案）
- 設定格式，讓資料更簡潔
- 建立並快取記憶體中的物件 （RDDs 或資料框架）
- 登錄 SQL 內容中的 temp 資料表。

以下是資料 ingestion 的程式碼。


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
    
        
    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

執行儲存格上方時所需的時間︰ 276.62 （秒)


## <a name="data-exploration--visualization"></a>資料瀏覽與視覺效果 

後資料已被納入火花，資料科學程序中的下一個步驟是將更深入的瞭解透過探索和視覺效果的資料。 在此區段中，我們會檢查使用 SQL 查詢的計程車資料，並繪製的目標變數和視覺檢查預期的功能。 具體來說，我們繪製計程車往返、 提示金額的頻率和秘訣，視情況而定的付款金額]，然後輸入旅客計算的頻率。

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>繪製直方圖的旅客計程車往返範例中的計算頻率

此程式碼及後續程式碼片段使用 SQL 個神奇查詢範例，並繪製資料的本機個神奇。

- **SQL 個神奇 (`%%sql`)**HDInsight PySpark 核心支援 sqlContext 輕鬆內嵌 HiveQL 查詢。 (-O VARIABLE_NAME) 引數的輸出保存在 SQL 查詢以熊 DataFrame Jupyter 伺服器上。 這表示在本機模式中使用。
- **`%%local`個神奇**用來在 Jupyter 伺服器上，也就是在 HDInsight 叢集 headnode 本機執行程式碼。 通常您會使用`%%local`神奇搭配`%%sql`神奇與 command 和 o 參數。 Command 和 o 參數想要保存在本機的 SQL 查詢輸出然後 %本機個神奇會觸發下一組對保存在本機的 SQL 查詢輸出本機執行的程式碼片段

您執行的程式碼之後，會自動的視覺化成果。

此查詢會擷取依旅客計數往返。 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


將此程式碼從查詢輸出建立本機資料範圍，並繪製的資料。 `%%local`個神奇會建立本機資料-框架， `sqlResults`，它可以用來繪製與 matplotlib。 

>[AZURE.NOTE] 此 PySpark 個神奇此逐步解說中使用多個時間。 如果資料量很大，您應該在本機的記憶體中建立可容納資料框架的範例。


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

以下是要繪製的旅客計數往返的程式碼

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**輸出**

![依旅客計數往返的頻率](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

您可以選取許多不同類型的視覺效果 （資料表、 圓形圖、 折線圖、 區域中或列） 之間使用的筆記本中的 [**類型**] 功能表按鈕。 列繪製如下所示。


### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>繪製直方圖的提示數量與提示量不盡相同，旅客 count 和 fare 金額。

使用範例資料的 SQL 查詢。
    
    # SQL SQUERY
    %%sql -q -o sqlResults
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train 
        WHERE passenger_count > 0 
        AND passenger_count < 7
        AND fare_amount > 0 
        AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 
        AND tip_amount < 25
    

此程式碼儲存格會用來建立三個繪圖資料的 SQL 查詢。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    
    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()
    

**輸出︰** 

![提示量通訊群組](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![提示旅客計算數量](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![提示以 fare 量的金額](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>工程、 轉換與資料模型準備的功能

本節說明，並準備用於毫升模型中的資料使用的程序提供的程式碼。 它會顯示如何執行下列工作︰

- 將流量時間值區 binning 小時建立的新功能
- 編製索引，並在熱編碼分類的功能
- 建立到毫升函數輸入標籤的點物件
- 建立隨機子範例的資料，並將其分割成訓練及測試設定
- 縮放功能
- 在記憶體中的快取物件


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>將流量時間值區 binning 小時建立的新功能

若要將流量時間值區 binning 小時建立的新功能的方式，然後如何快取結果資料框架在記憶體中的，則會顯示此程式碼。 同時分散式資料集 (RDDs) 和資料框架使用重複，快取通往改良的執行時間。 因此，我們快取 RDDs 和資料圖文框的逐步解說中的多個階段。

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**輸出**

126050


### <a name="index-and-one-hot-encode-categorical-features"></a>編製索引及一個熱編碼分類的功能

本節說明如何建立索引或編碼輸入到模型功能分類的功能。 模型和預測函數的 MLlib 需要功能與編製索引或之前使用編碼分類輸入資料。 

模型，根據您要編製索引，或將它們編碼不同的方式。 例如 Logistic 與線性迴歸模型需要一個熱編碼位置，三個類別的功能，例如可展開到三個功能欄，每個包含 0 或 1 根據觀察值的類別。 MLlib 提供[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder)函數，請執行一個熱編碼。 這個編碼器地圖標籤索引資料行的二進位向量，最多為單一項目值的資料行。 這個編碼方式可讓演算法的預期數值重要的功能的詳細資訊，例如後勤迴歸分析，可套用到分類的功能。

以下是要編製索引和編碼分類功能的程式碼︰


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
    
    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)
    
    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)
    
    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)
    
    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

執行儲存格上方時所需的時間︰ 3.14 （秒)


### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>建立到毫升函數輸入標籤的點物件

本節說明如何編製索引的標記的點的資料類型的分類文字資料，編碼後，這樣就可以使用訓練並測試 MLlib 後勤迴歸與其他分類模型的程式碼。 標記的點的物件會同時分散式資料集 (RDD) 格式所需的大部分的毫升演算法 MLlib 中做為輸入資料的方式。 [標示為 [點](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)是本機向量，[緊密] 或 [疏鬆，標籤/回應相關聯。

以下是要編製索引和編碼的二進位分類的文字功能的程式碼。

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


以下是要編碼及編製索引的線性迴歸分析的分類文字功能的程式碼。

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>建立隨機子範例的資料，並將其分割成訓練及測試設定

將此程式碼會建立隨機範例的資料 （以下使用 25%）。 雖然您不需要的大小由於此範例資料集的但我們會示範如何您可以範例以下，讓您瞭解如何使用它自己的問題時所需。 大型範例時，如此可節省大量時間訓練模型時。 接下來我們分割樣本訓練課程的一部分 （以下 75%) 和測試的部分 （以下 25%) 分類和迴歸模型中使用。


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand
    
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**輸出**

執行儲存格上方時所需的時間︰ 0.31 （秒)


### <a name="feature-scaling"></a>縮放功能

縮放功能，也稱為資料正常化確保的功能與廣泛 disbursed 值不指定過多衡量目標函數中。 縮放功能的程式碼會使用[StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler)來調整功能單位變異數。 MLlib 提供用於線性迴歸與 Stochastic 漸層深度 (SGD)，常用的演算法的訓練大範圍的其他電腦學習模型，例如 regularized 的回復或支援向量機器 (SVM)。   

>[AZURE.TIP] 我們發現 LinearRegressionWithSGD 演算法的機密按比例縮放的功能。   

以下是要用於線性的 regularized SGD 演算法的縮放比例變數的程式碼。

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    
    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**輸出**

執行儲存格上方時所需的時間︰ 11.67 （秒)


### <a name="cache-objects-in-memory"></a>在記憶體中的快取物件

快取輸入的資料圖文框的物件會用於分類，迴歸分析，以及縮放功能，可以縮小訓練及測試毫升演算法的所需的時間。

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**輸出** 

執行儲存格上方時所需的時間︰ 0.13 （秒)


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>預測二進位分類模型已付款秘訣

本節說明如何使用三個模型的預測二進位分類工作，無論提示所支付的計程車出差行程井然有序。 呈現的模型有︰

- 後勤迴歸分析 
- 隨機樹系
- 漸層促進樹狀結構

每一個建立的程式碼區段的模型分割成步驟︰ 

1. 一個參數設**模型訓練**資料
2. **模型評估**測試資料集的指標
3. Blob 供日後使用中的 [**儲存模型**

我們會顯示如何交叉驗證 （cv） 除以與全面兩種方法的參數︰

1. 使用**一般**的自訂程式碼的可套用至 MLlib 中任何演算法以及任何參數設定為演算法中。 
1. 使用**pySpark CrossValidator 管線函數**。 請注意，雖然方便，根據我們的經驗，CrossValidator 有火花 1.5.0 的一些限制︰ 

    - 管線模型無法儲存/會保存供日後使用。
    - 無法使用的模型中的每個參數。
    - 無法使用的每個 MLlib 演算法。


### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>一般交互驗證與 hyperparameter 到後勤迴歸演算法用於二進位分類

本節中的程式碼會顯示如何訓練、 評估，和儲存後勤迴歸模型[LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm)預測所支付的旅行 NYC 計程車差旅費和 fare 資料集內的提示。 模型被訓練使用跨驗證 （cv） 除以和 hyperparameter 到實作與可套用到的任何 MLlib 中學習演算法的自訂程式碼。   

>[AZURE.NOTE] 執行此自訂 CV 程式碼可能需要幾分鐘的時間。

**訓練使用 CV 和 hyperparameter 到後勤迴歸模型**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    
    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)
    
    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric
    
    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
        
    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)
    
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))
    
    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

係數: [0.0082065285375-0.0223675576104、-0.0183812028036、-3.48124578069e-05-0.00247646947233、-0.00165897881503、 0.0675394837328、-0.111823113101、-0.324609912762、-0.204549780032、-1.36499216354、 0.591088507921、-0.664263411392、-1.00439726852、 3.46567827545、-3.51025855172、-0.0471341112232、-0.043521833294、 0.000243375810385、 0.054518719222]

Intercept:-0.0111216486893

執行儲存格上方時所需的時間︰ 14.43 （秒)


**評估二進位分類模型與標準的指標**

本節中的程式碼會顯示如何評估後勤迴歸模型針對測試資料集，包括巨鵬曲線的繪圖。


    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
    
    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

區域下 PR = 0.985336538462

區域下巨鵬 = 0.983383274312

摘要統計

精確度 = 0.984174341679

回收 = 0.984174341679

F1 分數 = 0.984174341679

執行儲存格上方時所需的時間︰ 2.67 （秒)


**繪製巨鵬曲線。**

*PredictionAndLabelsDF*已註冊為表格， *tmp_results*，在一個儲存格。 *tmp_results*可用於執行查詢並輸出結果的 sqlResults 資料框架繪製。 以下是程式碼。


    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


以下是進行預測和繪製巨鵬曲線的程式碼。

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc
    
    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    # PLOT ROC CURVES
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()
    

**輸出**

![一般的方法後勤迴歸巨鵬曲線](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)


**固定模型中 blob，以供日後使用**

本節中的程式碼會顯示如何儲存消耗後勤迴歸模型。

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    
    logitBest.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**輸出**

執行儲存格上方時所需的時間︰ 34.57 （秒)


### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>使用 MLlib 的 CrossValidator 管線函數搭配後勤迴歸分析 （彈性的迴歸） 模型

本節中的程式碼會顯示如何訓練、 評估，和儲存後勤迴歸模型[LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm)預測所支付的旅行 NYC 計程車差旅費和 fare 資料集內的提示。 模型是使用跨驗證 （cv） 除以和 hyperparameter 到使用參數整理實作 MLlib CrossValidator 管線函數的履歷表訓練的。   


>[AZURE.NOTE] 執行此 MLlib CV 的程式碼可能需要幾分鐘的時間。


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc
    
    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**輸出**

執行儲存格上方時所需的時間︰ 107.98 （秒)


**繪製巨鵬曲線。**

*PredictionAndLabelsDF*已註冊為表格， *tmp_results*，在一個儲存格。 *tmp_results*可用於執行查詢並輸出結果的 sqlResults 資料框架繪製。 以下是程式碼。


    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

以下是要繪製巨鵬曲線的程式碼。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc
    
    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    #PLOT
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**輸出**

![使用 MLlib 的 CrossValidator 後勤迴歸巨鵬曲線](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)



### <a name="random-forest-classification"></a>隨機樹系分類

本節中的程式碼會顯示如何訓練、 評估，和儲存預測付費差旅費 NYC 計程車差旅費和 fare 資料集內的提示隨機樹系迴歸分析。


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

區域下巨鵬 = 0.985336538462

執行儲存格上方時所需的時間︰ 26.72 （秒)


### <a name="gradient-boosting-trees-classification"></a>漸層促進樹分類

本節中的程式碼會顯示如何訓練、 評估，和儲存預測提示所支付的 NYC 計程車差旅費在出差行程井然有序的漸層 boosting 樹模型和最好資料集。


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # Area under ROC curve
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;
    
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**輸出**

區域下巨鵬 = 0.985336538462

執行儲存格上方時所需的時間︰ 28.13 （秒)


## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>迴歸模型 （不使用 CV） 預測提示量

本節說明如何使用三個模型的預測的秘訣迴歸工作付費計程車旅行根據其他秘訣功能。 呈現的模型有︰

- Regularized 線性迴歸
- 隨機樹系
- 漸層促進樹狀結構

這些模型所述簡介。 每一個建立的程式碼區段的模型分割成步驟︰ 

1. 一個參數設**模型訓練**資料
2. **模型評估**測試資料集的指標
3. Blob 供日後使用中的 [**儲存模型**   


>AZURE 附註︰ 交叉驗證不會使用與三個迴歸模型，在此區段中，因為這顯示在後勤迴歸模型的詳細資料。 本主題本文附件中提供的範例，顯示如何使用彈性網路 CV 用於線性迴歸。


>AZURE 附註︰ 我們的經驗，可以有問題的 LinearRegressionWithSGD 模型交集和參數必須謹慎可以取得有效的模型變更/最佳化。 按比例縮放的變數大幅有助於交集。 彈性網路迴歸分析，顯示在附錄本主題中，也可以用，而不是 LinearRegressionWithSGD。


### <a name="linear-regression-with-sgd"></a>使用 SGD 線性迴歸

本節中的程式碼會顯示如何使用縮放的功能來訓練線性迴歸 stochastic 漸層深度 (SGD) 用於最佳化，以及如何分數、 評估，和儲存模型中 Azure Blob 儲存體 (WASB)。

>[AZURE.TIP] 在我們的經驗，可以有問題的 LinearRegressionWithSGD 模型交集，參數必須謹慎可以取得有效的模型變更/最佳化。 按比例縮放的變數大幅有助於交集。


    # LINEAR REGRESSION WITH SGD 

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats
    
    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))
    
    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)
    
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**輸出**

係數: [0.0141707753435、-0.0252930927087、-0.0231442517137、 0.247070902996、 0.312544147152、 0.360296120645、 0.0122079566092、-0.00456498588241、-0.0898228505177、 0.0714046248793、 0.102171263868、 0.100022455632、-0.00289545676449、-0.00791124681938、 0.54396316518、-0.536293513569、 0.0119076553369、-0.0173039244582、 0.0119632796147、 0.00146764882502]

Intercept: 0.854507624459

RMSE = 1.23485131376

R sqr = 0.597963951127

執行儲存格上方時所需的時間︰ 38.62 （秒)


### <a name="random-forest-regression"></a>隨機樹系迴歸分析

本節中的程式碼會顯示如何訓練、 評估，和儲存預測 NYC 計程車差旅費資料的秘訣金額的隨機樹系模型。   

>[AZURE.NOTE] 交叉驗證與全面使用自訂程式碼的參數附錄中提供。


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**輸出**

RMSE = 0.931981967875

R sqr = 0.733445485802

執行儲存格上方時所需的時間︰ 25.98 （秒)


### <a name="gradient-boosting-trees-regression"></a>漸層的促進樹迴歸

本節中的程式碼會顯示如何訓練、 評估，和儲存預測 NYC 計程車差旅費資料的秘訣金額的漸層 boosting 樹模型。

**訓練，以及評估**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

RMSE = 0.928172197114

R sqr = 0.732680354389

執行儲存格上方時所需的時間︰ 20.9 （秒)


**繪製**
    
*tmp_results*已註冊為登錄區資料表中的上一個儲存格。 從資料表的結果會轉換為繪製*sqlResults*資料圖文框的輸出。 以下是程式碼

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


以下是要繪製的資料使用 Jupyter 伺服器的程式碼。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np
    
    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![實際-與-預測-秘訣-金額](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>使用交叉驗證參數曲線附錄︰ 額外的迴歸工作

本附錄包含顯示如何使用彈性的網路的線性迴歸 CV 以及如何處理隨機樹系迴歸中使用自訂程式碼的參數整理 CV 的程式碼。


### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>交互驗證彈性網路用於線性迴歸

本節中的程式碼會顯示如何執行交互用於線性迴歸彈性網路的驗證及評估測試資料模型。

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    
    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 
    
    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
    
    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)
    
    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

執行儲存格上方時所需的時間︰ 161.21 （秒)

**利用 R SQR 公制評估**

*tmp_results*已註冊為登錄區資料表中的上一個儲存格。 從資料表的結果會轉換為繪製*sqlResults*資料圖文框的輸出。 以下是程式碼

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


以下是要計算 R sqr 的程式碼。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats
    
    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**輸出**

R sqr = 0.619184907088


### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>使用的隨機樹系迴歸分析中使用自訂程式碼的參數整理交互驗證

本節中的程式碼會顯示如何執行交互驗證使用的隨機樹系迴歸分析中使用自訂程式碼的參數整理及評估測試資料模型。


    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid
    
    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))
    
    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric
    
    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
            
    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**輸出**

RMSE = 0.906972198262

R sqr = 0.740751197012

執行儲存格上方時所需的時間︰ 69.17 （秒)


### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>清除從記憶體及列印的模型位置的物件

使用`unpersist()`若要刪除快取在記憶體中的物件。

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


**輸出**

在 PythonRDD.scala RDD PythonRDD [122]: 43


**到消耗筆記本中使用的模型檔案列印成品路徑。**若要使用分數獨立的資料集，必須複製並貼上 「 消耗筆記本 」 中的這些檔案名稱。


    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**輸出**

logisticRegFileLoc = modelDir + 」 LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528 」

linearRegFileLoc = modelDir + 」 LinearRegressionWithSGD_2016-05-0316_51_28.433670 」

randomForestClassificationFileLoc = modelDir + 」 RandomForestClassification_2016-05-0316_50_17.454440 」

randomForestRegFileLoc = modelDir + 」 RandomForestRegression_2016-05-0316_51_57.331730 」

BoostedTreeClassificationFileLoc = modelDir + 」 GradientBoostingTreeClassification_2016-05-0316_50_40.138809 」

BoostedTreeRegressionFileLoc = modelDir + 」 GradientBoostingTreeRegression_2016-05-0316_52_18.827237 」

## <a name="whats-next"></a>下一步是什麼？

現在您已經建立迴歸分析及分類模型與火花 MlLib，是準備好了解如何分數，以及評估這些模型。

**模型消耗︰**若要瞭解如何分數，並在評估分類和迴歸模型建立本主題中，請參閱[分數和評估火花內建電腦學習模型](machine-learning-data-science-spark-model-consumption.md)。
