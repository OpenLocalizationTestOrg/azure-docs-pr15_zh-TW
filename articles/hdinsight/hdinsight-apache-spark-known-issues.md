<properties 
    pageTitle="已知問題的 HDInsight 中 Apache 火花 |Microsoft Azure" 
    description="Apache 火花 HDInsight 中的已知的問題。" 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>HDInsight linux Apache 火花叢集的已知的問題

這份文件會持續追蹤的 HDInsight 火花公用 preview 版的已知的問題。  

##<a name="livy-leaks-interactive-session"></a>晚總遺漏互動式工作階段
 
使用互動式工作階段 （從 Ambari 或因為 headnode 0 虛擬機器重開機） 仍在執行重新啟動晚總時，會洩漏互動式工作階段。 因此，新的工作可以卡在已接受的狀態，並無法啟動。

**降低風險︰**

使用以下程序，因應措施此問題︰

1. Ssh 到 headnode。 
2. 執行下列命令以尋找應用程式識別碼開始到晚總的互動式工作。 

        yarn application –list

    名稱會指定晚總如果工作已開始與晚總互動式工作階段沒有明確的名稱，如晚總工作階段啟動 Jupyter 筆記本的預設工作的工作名稱開頭會 remotesparkmagics_ *。 

3. 執行下列命令以刪除這些工作。 

        yarn application –kill <Application ID>

新的工作會開始執行。 

##<a name="spark-history-server-not-started"></a>火花歷程記錄伺服器未啟動 

建立叢集後火花歷程記錄伺服器未自動啟動。  

**降低風險︰** 

從 Ambari 中手動啟動歷程記錄伺服器。

## <a name="permission-issue-in-spark-log-directory"></a>使用權限火花記錄目錄中的問題 

當 hdiuser 提交 spark-submit 工作時，就會發生錯誤 java.io.FileNotFoundException︰ 不會寫入 /var/log/spark/sparkdriver_hdiuser.log （拒絕的權限） 和驅動程式登入。 

**降低風險︰**
 
1. 新增 hdiuser Hadoop 群組。 
2. 提供叢集建立之後 /var/log/spark 777 權限。 
3. 更新要 777 權限的目錄使用 Ambari 火花記錄檔位置。  
4. 執行火花-送出 sudo。  

## <a name="issues-related-to-jupyter-notebooks"></a>Jupyter 筆記本的相關問題

以下是一些相關 Jupyter 筆記本的已知的問題。


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>在 [檔案名稱中的非 ASCII 字元的筆記本

Jupyter 筆記本的可用於火花 HDInsight 叢集不應該非 ASCII 字元的檔案名稱。 如果您嘗試透過 Jupyter UI 有非 ASCII 檔案名稱將檔案上傳時，它將會失敗火車 （也就是 Jupyter 不會讓您上傳檔案，但它可能不會擲回看到的錯誤）。 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>載入更大的大小的筆記本時發生錯誤

您可能會看到錯誤**`Error loading notebook`**時載入大小較大的筆記本。  

**降低風險︰**

如果您收到此錯誤，並不表示您的資料已損毀或遺失。  您的筆記本是在磁碟上仍`/var/lib/jupyter`，，您可以將叢集存取他們的 SSH。 您可以複製您的筆記本叢集到本機電腦 （使用 SCP 或 WinSCP） 為 [備份] 可防止遺失的筆記本中任何重要資料。 然後您可以 SSH 通道到您在連接埠不透過閘道器存取 Jupyter 8001 headnode。  從該位置，您可以清除您的筆記本的輸出，並重新儲存至筆記本的大小最小化。

若要防止未來發生此錯誤，您必須遵循一些最佳作法︰

* 請務必保持筆記本的大小。 從您的火花工作會傳回到 Jupyter 的任何輸出保存在筆記本中。  最佳作法是 Jupyter 與一般避免執行`.collect()`開啟大型 RDD 的或 dataframes;不過，如果您想要查看 RDD 內容，請考慮執行`.take()`或`.sample()`，讓您的輸出無法取得太大。
* 此外，當您儲存筆記本，清除所有輸出要縮小儲存格。

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>筆記本初始啟動耗費的時間比預期 

使用火花個神奇 Jupyter 筆記本中的第一個程式碼陳述式可能需要一分鐘以上。  

**說明︰**
 
這是因為時執行第一個程式碼儲存格。 這在背景中啟動工作階段設定，火花 SQL，並登錄區內容已設定。 設定這些內容後，在第一個陳述式執行此人的印象的陳述式花費很長的時間才能完成。

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>建立工作階段 Jupyter 筆記本逾時

當火花叢集超出資源時，Jupyter 筆記本中的火花和 Pyspark 核心將會在嘗試建立工作階段逾時。 

**降低︰** 

1. 釋放中火花叢集的資源︰

    - 停止其他火花筆記本移至 [關閉] 和 [終止] 功能表，或按一下 [筆記本檔案總管] 中的 [關閉。
    - 停止 YARN 從其他火花應用程式。

2. 重新啟動您嘗試啟動的筆記本。 您應該可供您建立的工作階段，現在足夠的資源。

##<a name="see-also"></a>另請參閱

* [概觀︰ Apache 火花上 Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>案例

* [使用 BI 火花︰ 執行火花 HDInsight 中使用的 BI 工具的互動式的資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [與電腦學習火花︰ 使用火花 HDInsight 分析建置溫度使用 HVAC 資料中](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [與電腦學習火花︰ 使用火花 HDInsight 預測食物檢查結果中](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [火花串流︰ 使用火花 HDInsight 建置即時串流應用程式中](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight 中使用火花網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

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
