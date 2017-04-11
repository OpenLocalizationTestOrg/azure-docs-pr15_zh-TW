<properties 
    pageTitle="使用自訂的文件庫與 HDInsight 火花叢集分析網站記錄 |Microsoft Azure" 
    description="使用自訂文件庫來分析網站記錄 HDInsight 火花叢集" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>分析網站使用自訂的文件庫與 Apache 火花叢集 HDInsight linux 的記錄檔

此筆記本示範如何記錄使用分析資料的自訂文件庫與火花 HDInsight 上。 我們使用的自訂文件庫是稱為**iislogparser.py**Python 文件庫。

> [AZURE.TIP] 本教學課程中也會有為 Jupyter 上筆記本火花 (Linux) 叢集在 HDInsight 中所建立的。 筆記本體驗，可讓您從筆記本本身執行 Python 程式碼片段。 若要執行的教學課程筆記本中，建立火花叢集、 啟動 Jupyter 筆記本 (`https://CLUSTERNAME.azurehdinsight.net/jupyter`)，然後再**PySpark**資料夾中的 [執行**分析記錄以使用自訂的 library.ipynb 火花**的筆記本。

**先決條件︰**

您必須具備下列項目︰

- Azure 的訂閱。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight linux Apache 火花圖組。 如需相關指示，請參閱[建立 Apache 火花叢集中 Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md)。

## <a name="save-raw-data-as-an-rdd"></a>為 RDD 儲存原始資料

在此區段中，我們使用 Apache 火花中叢集 HDInsight 相關聯的[Jupyter](https://jupyter.org)筆記本執行作業，處理您的原始範例資料，並將其儲存為登錄區資料表。 範例資料是.csv 檔案 (hvac.csv) 使用預設的所有叢集。

一旦將資料儲存成登錄區資料表時下, 一節我們會連接到登錄區資料表中使用 Power BI 等 Tableau 的 BI 工具。

1. 從[Azure 入口網站](https://portal.azure.com/)，從 startboard 中，按一下 [火花叢集的磚 （如果您釘選它 startboard）。 您也可以導覽下**瀏覽所有**叢集 > **HDInsight 叢集**。   

2. 從火花叢集刀中，按一下 [**叢集儀表板**，，然後按一下**Jupyter 筆記本**。 如果出現提示，請輸入叢集管理員認證。

    > [AZURE.NOTE] 您也可能會在瀏覽器中開啟下列 URL 達到叢集 Jupyter 筆記本。 取代您叢集名稱__CLUSTERNAME__ :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的筆記本。 按一下 [**新增**]，然後按一下 [ **PySpark**。

    ![建立新的 Jupyter 筆記本](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter 筆記本")

3. 建立新的筆記本，並開啟 Untitled.pynb 名稱。 按一下筆記本名稱上方，然後輸入好記的名稱。

    ![提供筆記本的名稱](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "提供筆記本的名稱")

4. 您建立 PySpark 核心的筆記本，因為您不需要明確地建立任何內容。 當您執行第一個程式碼儲存格時，火花和登錄區內容，會自動建立適合您。 您可以開始匯入所需的這種情況類型。 貼上下列的程式碼片段空白的儲存格，然後再按下**SHIFT + enter 鍵**。


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. 建立使用的範例記錄的資料已提供叢集 RDD。 您可以存取**\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**叢集相關聯的預設儲存帳戶中的資料。


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. 擷取範例記錄設定，以確認已順利完成的上一個步驟。

        logs.take(5)

    您應該會看到類似以下的輸出︰

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>記錄檔使用分析資料的自訂 Python 文件庫

7. 上述輸出的前幾行包含的標頭資訊並剩餘的每一行比對該標題所述的結構描述。 剖析這類記錄可能很複雜。 因此，我們使用自訂 Python 文件庫 (**iislogparser.py**)，讓剖析很容易這類記錄。 根據預設，此文件庫是隨附於在**/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**上 HDInsight 火花叢集。

    不過，不在此文件庫`PYTHONPATH`，因此我們無法使用的匯入陳述式，例如將其`import iislogparser`。 若要使用此文件庫，我們必須將其發送給所有的工作者節點。 執行下列程式碼片段。


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`提供函數`parse_log_line`傳回的`None`是否記錄線條為標題列，，並傳回的執行個體`LogLine`課程遇到記錄線條。 使用`LogLine`類別以從 RDD 擷取記錄行︰

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. 擷取數解壓縮的記錄檔行，以確認已順利完成的步驟。

        logLines.take(2)

    輸出應如下︰

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. `LogLine`類別，接著，有一些實用的方法，想`is_error()`，傳回的記錄項目是否有錯誤的程式碼。 使用此計算數解壓縮的記錄檔行中的錯誤，然後將所有錯誤記錄到不同的檔案。

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    您應該會看到如下所示的輸出︰

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. 您也可以使用**Matplotlib**來建構資料的視覺效果。 例如，如果您要找出執行一段時間的要求的原因，可能要尋找最的時間才能作為平均的檔案。
下列程式碼片段擷取大部分的時間來服務要求的上方 25 資源。

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    您應該會看到如下所示的輸出︰

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. 您也可以呈現繪圖的形式這項資訊。 若要建立的繪圖，請讓我們先建立暫時表格**AverageTime**第一個步驟。 表格群組記錄，請參閱在特定時間沒有任何異常延遲波峰上的時間。

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. 然後，您可以執行下列 SQL 查詢以取得**AverageTime**資料表中的所有記錄。

        %%sql -o averagetime
        SELECT * FROM AverageTime

    `%%sql`個神奇後面`-o averagetime`可確保查詢的輸出本機保存在 Jupyter 伺服器 (通常是叢集 headnode)。 輸出會保存為以指定的名稱**averagetime**[熊](http://pandas.pydata.org/)dataframe。

    您應該會看到如下所示的輸出︰

    ![SQL 查詢輸出](./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "SQL 查詢輸出")

    如需有關`%%sql`的優勢，以及其他我們提供 PySpark 核心，請參閱[核心提供火花 HDInsight 叢集 Jupyter 筆記本](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)。

15. 您現在可以使用 Matplotlib，用來建構視覺效果的資料，文件庫來建立的繪圖。 因為您必須從本機保存的**averagetime** dataframe 建立繪圖，都必須開始的程式碼片段`%%local`個神奇。 如此一來，可確保 Jupyter 伺服器上執行的程式碼是本機。

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    您應該會看到如下所示的輸出︰

    ![Matplotlib 輸出](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Matplotlib 輸出")

16. 當您完成執行應用程式之後，您應該要關閉的筆記本，以釋出資源。 若要這麼做，從 [**檔案**] 功能表上的筆記本中，按一下 [**關閉並停止**]。 這將會關閉，然後關閉的筆記本。
    

## <a name="seealso"></a>另請參閱


* [概觀︰ Apache 火花上 Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>案例

* [使用 BI 火花︰ 執行火花 HDInsight 中使用的 BI 工具的互動式的資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [與電腦學習火花︰ 使用火花 HDInsight 分析建置溫度使用 HVAC 資料中](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [與電腦學習火花︰ 使用火花 HDInsight 預測食物檢查結果中](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [火花串流︰ 使用火花 HDInsight 建置即時串流應用程式中](hdinsight-apache-spark-eventhub-streaming.md)

### <a name="create-and-run-applications"></a>建立和執行應用程式

* [建立使用 Scala 獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

* [在使用晚總火花叢集從遠端執行工作](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和延伸模組

* [使用 HDInsight 工具增益集，如 IntelliJ 瞭解建立及提交火花 Scala 應用程式](hdinsight-apache-spark-intellij-tool-plugin.md)

* [使用 HDInsight 工具增益集，如 IntelliJ 瞭解遠端偵錯火花應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [使用上 HDInsight 火花叢集運貨用飛艇筆記本](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [核心適用於 HDInsight 火花叢集 Jupyter 筆記本](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [使用外部封包 Jupyter 筆記本](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [在 [您的電腦上安裝 Jupyter 並連線到 HDInsight 火花叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

* [管理資源 Apache 火花叢集中 Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [追蹤和偵錯 Apache 火花中叢集 HDInsight 上執行的工作](hdinsight-apache-spark-job-debugging.md)
