<properties 
    pageTitle="Azure 資料工廠-常見問題集" 
    description="常見問題集 Azure 資料工廠。" 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---frequently-asked-questions"></a>Azure 資料工廠-常見問題集

## <a name="general-questions"></a>一般問題

### <a name="what-is-azure-data-factory"></a>什麼是 Azure 資料工廠？

資料工廠是以雲端為基礎的資料整合服務**會自動執行的動作及資料轉換**。 就像執行要素並將它們轉換成完成貨物設備工廠資料工廠協調現有的服務，收集原始資料，並將其轉換為準備要使用的資訊。 
 
資料工廠可讓您建立的資料導向至內部部署與雲端資料存放區以及使用 Azure HDInsight 和 Azure 資料湖分析等的運算服務的程序/轉換資料之間移動資料的工作流程。 建立執行的動作所需的管線之後，您可以將它 （小時、 每天、 每週等），請執行定期排程。   

如需詳細資訊，請參閱[概觀與重要概念](data-factory-introduction.md)。 

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>哪裡可以找到價格 Azure 資料工廠詳細資料？

請參閱[資料工廠定價詳細資料] 頁面][ adf-pricing-details] Azure 資料工廠的定價詳細資料。  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>如何開始使用 Azure 資料工廠？

- Azure 資料工廠的概觀，請參閱[Azure 資料工廠簡介](data-factory-introduction.md)。
- 如何使用複製活動**複製或移動資料**的教學課程，請參閱[複製資料從 Azure Blob 儲存體到 Azure SQL 資料庫](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
- 教學課程中，**將資料轉換**的方式使用 HDInsight 登錄區活動。 請參閱[執行在 Hadoop 叢集登錄區指令碼處理資料](data-factory-build-your-first-pipeline.md) 
  
### <a name="what-is-the-data-factorys-region-availability"></a>什麼是資料工廠地區可用性？
在 [**美國西部**及**北美歐洲**使用資料工廠。 使用資料工廠的計算，儲存空間服務可在其他區域。 請參閱[支援的區域](data-factory-introduction.md#supported-regions)。 
 
### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>資料工廠/管線/活動/資料集的限制是什麼？
 
[Azure 訂閱及服務限制，配額和限制](../azure-subscription-service-limits.md#data-factory-limits)的文件，請參閱**Azure 資料工廠限制**一節。

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>什麼是 Azure 資料工廠服務的開發人員撰寫/體驗？

您可以作者/建立資料工廠使用下列其中一項︰

- **Azure 入口網站** 
   Azure 入口網站中的資料工廠刀提供豐富的使用者介面，以建立資料工廠 ad 連結服務。 **資料工廠編輯器**，也是入口網站的組件，可讓您輕鬆建立連結的服務、 資料表、 資料集，以及管線指定這些成品 JSON 定義。 如需使用入口網站/編輯器來建立並部署資料工廠的範例，請參閱[建立您使用 Azure 入口網站的第一個資料管線](data-factory-build-your-first-pipeline-using-editor.md)。

- **Visual Studio**  
  您可以使用 Visual Studio 建立 Azure 資料工廠。 如需詳細資訊，請參閱[建立您使用 Visual Studio 的第一個資料管線](data-factory-build-your-first-pipeline-using-vs.md)。 

- **Azure PowerShell**  
  如教學課程/逐步引導建立資料 factory 使用 PowerShell，請參閱[建立和使用 PowerShell 的 Azure Azure 資料工廠監視器](data-factory-build-your-first-pipeline-using-powershell.md)。 請參閱[資料工廠 Cmdlet 參考][ adf-powershell-reference] MSDN 文件庫上的資料工廠 cmdlet 全面涵蓋所有內容的文件的內容。
   
- **.NET 類別庫** 
  使用資料工廠.NET SDK，您可以以程式設計方式建立資料工廠。 建立資料 factory 使用.NET SDK 的逐步解說，請參閱[建立、 監控及管理資料使用.NET SDK 的工廠](data-factory-create-data-factories-programmatically.md)。 請參閱[資料工廠類別文件庫參考][msdn-class-library-reference]的資料工廠.NET SDK 全面涵蓋所有內容的文件。

- **REST API**  
  您也可以使用 Azure 資料工廠服務所公開 REST API 來建立並部署資料工廠。 請參閱[資料工廠 REST API 參考][msdn-rest-api-reference]的資料工廠 REST API 全面涵蓋所有內容的文件。
 
- **Azure 資源管理員範本**
  查看[教學課程︰ 建立您使用 Azure 資源管理員範本的第一個 Azure 資料工廠](data-factory-build-your-first-pipeline-using-arm.md)之詳細資料。 

### <a name="can-i-rename-a-data-factory"></a>可以重新命名資料工廠嗎？
[否]。 其他 Azure 的資源，例如無法變更 Azure 資料工廠的名稱。 

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>我可以移動資料工廠從一個 Azure 訂閱到另一個嗎？ 
[是]。 下圖所示，請使用您的資料工廠刀上的 [**移動**] 按鈕。 

![移動資料工廠](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>什麼是受支援的資料工廠計算環境？
下表列出的計算環境支援資料工廠，並在其執行的活動。 

| 計算環境 | 活動 |
| ------------------- | -------- | 
| [視需要 HDInsight 叢集](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)或[HDInsight 叢集](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md)[登錄區](data-factory-hive-activity.md)、[豬](data-factory-pig-activity.md)、 [MapReduce](data-factory-map-reduce.md)、 [Hadoop 串流](data-factory-hadoop-streaming-activity.md) | 
| [Azure 批次](data-factory-compute-linked-services.md#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |  
| [Azure 機器學習](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) | [電腦學習活動︰ 批次執行和更新資源](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure 資料湖狀況分析](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) | [資料湖分析 U SQL](data-factory-usql-activity.md)
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service) [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service)， [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) | [預存程序](data-factory-stored-proc-activity.md)

## <a name="activities---faq"></a>活動-常見問題集
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>有哪些不同類型的活動，您可以使用資料工廠管道？ 

- 若要移動資料的[資料移動活動](data-factory-data-movement-activities.md)。
- 程序轉換資料[的資料轉換的活動](data-factory-data-transformation-activities.md)。 

### <a name="when-does-an-activity-run"></a>何時執行的活動？
在 [輸出資料] 資料表中的**可用性**組態設定會決定時執行的活動。 如果未指定輸入資料集，活動會檢查是否滿足所有輸入的資料相依性 （亦即**準備好**狀態） 開始執行之前。 

## <a name="copy-activity---faq"></a>複製活動-常見問題集
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>這是更有多個活動的管線或每個活動的個別管線？ 
管線應該要封裝相關的活動。 如果所管線外的任何其他活動不使用連接圖形資料集，您可以將一個管道的活動。 如此一來，您就不需要鏈管線作用中期間，讓它們彼此對齊。 此外，更新管線時，會更有效地保留內部管線資料表中的資料完整性。 管線更新基本上停止管線內所有的活動，並移除並重新建立這些。 從撰寫觀點來看，也可能看起來更清楚管線一個 JSON 檔案中的相關活動中的資料流動。

### <a name="what-are-the-supported-data-stores"></a>支援的資料儲存區是什麼？
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

### <a name="what-are-the-supported-file-formats"></a>支援的檔案格式為何？ 
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>執行複製作業的位置 
如需詳細資訊，請參閱[移動全域可用的資料](data-factory-data-movement-activities.md#global)一節。 簡言之，包含內部部署資料存放區，複製進行您的內部部署環境中的資料管理閘道器。 與兩個雲端儲存區之間移動資料時，最接近接收位置，在相同的地理區域執行複製作業。 


## <a name="hdinsight-activity---faq"></a>HDInsight 活動-常見問題集

### <a name="what-regions-are-supported-by-hdinsight"></a>HDInsight 支援哪些地區？

請參閱下列文章中的 [地理可用性] 區段︰ 或[HDInsight 定價詳細資料][hdinsight-supported-regions]。

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>視需要 HDInsight 叢集使用哪些地區？

視需要 HDInsight 叢集會建立在您指定要使用與叢集儲存所在的相同地區。    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>如何建立關聯 HDInsight 叢集額外儲存空間帳戶？

如果您使用自己的 HDInsight 叢集 (BYOC-將您自己的叢集)，請參閱下列主題︰ 

- [使用另一個儲存帳戶和 Metastores HDInsight 叢集][hdinsight-alternate-storage]
- [額外儲存空間帳戶使用 HDInsight 登錄區][hdinsight-alternate-storage-2]

如果您使用所建立的資料工廠服務視叢集，指定額外儲存空間的 HDInsight 帳戶連結服務，讓資料工廠服務可以代表您註冊其。 視需要連結服務的 JSON 定義，來指定下列 JSON 程式碼片段所示的另一個儲存帳戶使用**additionalLinkedServiceNames**屬性︰
 
    {
        "name": "MyHDInsightOnDemandLinkedService",
        "properties":
        {
            "type": "HDInsightOnDemandLinkedService",
            "typeProperties": {
                "clusterSize": 1,
                "timeToLive": "00:01:00",
                "linkedServiceName": "LinkedService-SampleData",
                "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
            }
        }
    } 

在上述範例中，otherLinkedServiceName1 和 otherLinkedServiceName2 代表連結的服務，其定義包含 HDInsight 叢集需要存取另一個儲存帳戶認證。

## <a name="slices---faq"></a>扇形區-常見問題集

### <a name="why-are-my-input-slices-not-in-ready-state"></a>為何我輸入的扇形區不是在準備好狀態？  
常見的錯誤時，無法設定**外部**屬性為**true**輸入的資料集上輸入的資料是外部資料工廠 （不所產生的資料 factory）。 

在下列範例中，您只需要設定**外部**為 true **dataset1**。  

**DataFactory1**管線 1: dataset1]-> [activity1]-> [dataset2]-> [activity2 dataset3 管線 2]-> [: dataset3]-> [activity3 dataset4]->

如果您有另一個資料工廠採用 dataset4 （所產生的管線 2 資料工廠 1） 的管線與，標示 dataset4 為外部資料集因為資料集產生的不同資料工廠 （DataFactory1、 不 DataFactory2）。  

**DataFactory2**    
管線 1: dataset4]-> [activity4 dataset5]->

如果 [外部] 屬性設定正確，請確認輸入的資料是否在輸入資料集定義中指定的位置。 

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>如何在超過午夜時扇形區每天所產生的另一個時間執行扇形區？
使用 [**位移**] 屬性，指定您想要產生的扇形區的時間。 請參閱[資料集可用性](data-factory-create-datasets.md#Availability)] 區段中的此屬性的詳細資料。 以下是快速範例︰

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

每日的扇形區來啟動，而不是預設午夜**6 AM** 。     

### <a name="how-can-i-rerun-a-slice"></a>如何重新執行扇形區？
您可以重新執行扇形區，下列方法之一︰ 

- 若要重新執行的活動] 視窗或扇形區使用監控和管理應用程式。 如需相關指示，請參閱[重新執行選取的活動視窗](data-factory-monitor-manage-app.md#re-run-selected-activity-windows)。   
- 在**資料扇形區**刀 Azure 入口網站中的扇形區的命令列中，按一下 [**執行**]。
- 透過狀態設定為 [**正在等候**的扇形區來執行**設定 AzureRmDataFactorySliceStatus**指令程式。   
    
        Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

請參閱[設定 AzureRmDataFactorySliceStatus] [set-azure-datafactory-slice-status]的 cmdlet 的詳細資料。 

### <a name="how-long-did-it-take-to-process-a-slice"></a>如何將其時間處理扇形區？
使用在監視器與管理應用程式的活動視窗總管知道資料扇形區 [處理程序所需的時間。 如需詳細資訊，請參閱[活動視窗總管](data-factory-monitor-manage-app.md#activity-window-explorer)。 

您也可以執行下列 Azure 入口網站中︰  

1. 按一下您資料的工廠的**資料工廠**刀上的**資料集**磚。
2. 按一下 [在**資料集**刀上特定的資料集]。
3. 選取您感興趣的**表格**刀的 [**最近的扇形區**] 清單的扇形區。
4. 按一下 [從**活動執行**清單**資料的扇形區**刀上執行的活動]。 
5. 按一下 [**活動執行詳細資料**刀上的 [**屬性**] 磚。 
6. 您應該會看到 [**工期**] 欄位的值。 此值是扇形區 [處理程序所花的時間。   

### <a name="how-to-stop-a-running-slice"></a>若要停止執行的扇形區如何？
如果您要停止執行管道時，您可以使用[暫停 AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx)指令程式。 目前，暫停管道的郵件不會停止進行中的扇形區執行。 一旦完成進行中執行，沒有額外的扇形區挑選。

如果您確實想要立即停止所有執行，唯一的方法就是刪除管線，建立一次。 如果您選擇刪除管線，您不需要刪除表格及連結的管線所使用的服務。 


[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 
