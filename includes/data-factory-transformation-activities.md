Azure 資料工廠支援下列轉換活動可新增至管線可以個別或鏈結與另一個活動。

資料轉換活動 |  計算環境 
:----------------------- | :--------------------
[登錄區](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[豬](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop 串流](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[電腦學習活動︰ 批次執行和更新資源](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[預存程序](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL Azure SQL 資料倉庫的 SQL Server |
[資料湖分析 U SQL](../articles/data-factory/data-factory-usql-activity.md) | Azure 資料湖狀況分析 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] 或 Azure 批次
   
> [AZURE.NOTE] 
> 您可以使用 MapReduce 活動 HDInsight 火花叢集上執行火花程式。 如需詳細資訊，請參閱[從 Azure 資料工廠叫用火花程式](../articles/data-factory/data-factory-spark.md)。
> 您可以建立自訂的活動，以執行安裝 R HDInsight 叢集 R 指令碼。 請參閱[執行 R 指令碼使用 Azure 資料工廠](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)。