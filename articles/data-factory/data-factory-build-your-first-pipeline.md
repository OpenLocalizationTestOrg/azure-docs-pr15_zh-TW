<properties
    pageTitle="資料工廠教學課程︰ 第一個資料管線 |Microsoft Azure"
    description="本 Azure 資料工廠教學課程中會顯示如何建立及排程資料工廠處理 Hadoop 叢集上使用登錄區指令碼的資料。"
    services="data-factory"
    keywords="azure 資料工廠教學課程，hadoop 叢集，hadoop 登錄區"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>教學課程︰ 建立您的第一個管線使用 Hadoop 叢集的程序資料 
> [AZURE.SELECTOR]
- [概觀與先決條件](data-factory-build-your-first-pipeline.md)
- [Azure 入口網站](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

在本教學課程中，您可以建立 Azure HDInsight (Hadoop) 叢集上執行登錄區指令碼處理資料的資料管線與您第一份 Azure 資料工廠。 

> [AZURE.NOTE] 本文不提供 Azure 資料工廠的概觀。 服務的概觀，請參閱[Azure 資料工廠簡介](data-factory-introduction.md)。 請參閱[資料工廠學習路徑](https://azure.microsoft.com/documentation/learning-paths/data-factory/)的建議方式瀏覽內容若要深入瞭解資料工廠。

## <a name="whats-covered-in-this-tutorial"></a>在本教學課程涵蓋內容？ 
**Azure 資料工廠**可讓您的資料導向的工作流程 （也稱為資料管線） 為撰寫資料**移動**及資料**處理**工作。 您瞭解如何建立您的第一個資料與資料處理 （或資料轉換） 的管線活動。 此活動使用 HDInsight Hadoop 叢集轉換及分析範例網站記錄。  

在本教學課程中，您可以執行下列步驟︰

1.  建立**資料工廠**。 資料工廠可以包含一個或多個資料 pipelines 移動與程序資料。 
2.  建立**連結的服務**。 若要連結的資料存放區的連結的服務或計算資料 factory 服務建立。 資料儲存區例如 Azure 儲存體管道保留輸入輸出資料的活動。 計算服務，例如 HDInsight Hadoop 叢集程序/轉換資料。    
3.  建立輸入與輸出**資料集**。 輸入資料集表示輸入的管線中的活動，輸出資料集表示活動的輸出效果。
3.  建立**管線**。 管線可以有一或多個活動 (範例︰ 複製活動、 HDInsight 登錄區活動)。 此範例使用 HDInsight Hadoop 叢集執行登錄區指令碼 HDInsight 登錄區活動。 指令碼第一次會建立參照 Azure blob 儲存體中儲存的原始 web 記錄資料的資料表，然後由 year 和 month 分割的原始資料。

    有兩種類型的支援 Azure 資料工廠的活動。 他們的年齡︰[資料移動活動](data-factory-data-movement-activities.md)」 和 「[資料轉換的活動](data-factory-data-transformation-activities.md)。 有的複製活動只有一個資料移動活動。 在本教學課程中，您不使用複製活動。 使用複製活動的教學課程，請參閱[教學課程︰ 複製資料從 Azure blob Azure sql](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 您在本教學課程中使用的 HDInsight 登錄區活動是一種資料工廠所支援的資料轉換活動。  
 
以下是您在本教學課程中建立的範例資料工廠的**圖表檢視**。 

![在資料工廠教學課程中的 [圖表] 檢視](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

在本教學課程， **adfgetstarted** Azure blob 容器**inputdata**資料夾會包含一個名為 input.log 的檔案。 這個記錄檔有三個月的項目︰ 一月、 二月和三月的 2016年。 以下是每個月的範例資料列中輸入的檔案。 

    2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

含 HDInsight 登錄區活動的管線處理檔案時，活動登錄區指令碼上執行 HDInsight 叢集的磁碟分割區輸入資料 year 和 month。 指令碼會建立三個輸出資料夾包含從每個月的項目使用的檔案。  

    adfgetstarted/partitioneddata/year=2016/month=1/000000_0
    adfgetstarted/partitioneddata/year=2016/month=2/000000_0
    adfgetstarted/partitioneddata/year=2016/month=3/000000_0

如上所示的範例行，從第一個 （含 2016年-01-01) 寫入 000000_0 檔案月 = 1 的資料夾。 同樣地，第二個寫入檔案月 = 2 的資料夾和第三個一個月寫入檔案 = 3 的資料夾。  


## <a name="pre-requisites"></a>必要條件
本教學課程之前，您必須具備下列先決條件︰

1.  **Azure 訂閱**-如果您沒有 Azure 的訂閱，您可以建立的免費的試用帳戶在幾分鐘。 在您要如何取得免費的試用帳戶，請參閱[免費試用版](https://azure.microsoft.com/pricing/free-trial/)的文件。

2.  **Azure 儲存體**– 您將資料儲存在此教學課程中使用 Azure 儲存體帳戶。 如果您沒有安裝 Azure 儲存體帳戶，請參閱[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。 您建立的儲存空間帳戶後，請注意下 [**帳戶名稱**] 和 [**便捷鍵**。 請參閱[檢視、 複製及重新產生的儲存空間便捷鍵](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)。 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>教學課程上傳檔案到 Azure 儲存體
在開始之前教學課程，您需要使用範例檔案的 Azure 儲存體帳戶準備教學課程。

1. 登錄區查詢檔案 (HQL) 上傳至**adfgetstarted** blob 容器的**指令碼**] 資料夾。
2. 輸入的檔案上傳**inputdata** **adfgetstarted** blob 容器] 資料夾。 

#### <a name="create-hql-script-file"></a>建立 HQL 指令碼檔 

1. 啟動 [**記事本]**並貼上下列的 HQL 指令碼。 此登錄區指令碼會建立兩個資料表︰ **WebLogsRaw**和**WebLogsPartitioned**。 按一下 [**檔案**] 功能表，然後選取 [**另存新檔**。 切換至您的硬碟上的 [ **C:\adfgetstarted** ] 資料夾。 選取 [**所有檔案 (*。*)**的**儲存輸入**欄位。輸入**partitionweblogs.hql**的****的檔案名稱。確認已選取**編碼**底部的對話方塊] 欄位設為**ANSI**。如果不是，將它設定為**ANSI * *。  

        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw

在執行階段，登錄區中的活動資料工廠管線將值傳遞**inputtable**和**partitionedtable**參數下列程式碼片段所示︰  

        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

**Storageaccountname**是您的 Azure 儲存體帳戶名稱。
 
#### <a name="create-a-sample-input-file"></a>建立範例輸入的檔案
使用 [記事本]，建立名為**input.log** **c:\adfgetstarted**下列內容中的檔案︰ 

    #Software: Microsoft Internet Information Services 8.0
    #Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>將輸入的檔案和 HQL 檔案上傳至您的 Azure Blob 儲存體

本節提供指示使用**AzCopy**工具將 input.log 和 partitionweblogs.hql 檔案複製到 Azure Blob 儲存體。 您可以使用您所選擇的任何工具 (例如︰ [Microsoft Azure 儲存檔案總管](http://storageexplorer.com/)、 [CloudXPlorer ClumsyLeaf 軟體](http://clumsyleaf.com/products/cloudxplorer)，若要執行這項工作。   
     
1. 下載[最新版本的**AzCopy**](http://aka.ms/downloadazcopy)或[最新的 preview 版本](http://aka.ms/downloadazcopypr)。 如需使用公用程式的指示，請參閱[如何使用 AzCopy](../storage/storage-use-azcopy.md)文章。
2. 瀏覽至 [c:\adfgetstarted] 資料夾，然後執行下列命令︰ 

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

    這個命令上載**input.log**檔案儲存空間帳戶 （**adfgetstarted**容器和**inputdata**資料夾）。 儲存帳戶及**storageaccesskey**名稱取代儲存便捷鍵**storageaccountname** 。

    > [AZURE.NOTE] 這個命令會建立名為您 Azure Blob 儲存體中**adfgetstarted**容器，並將**input.log**檔案從本機磁碟複製容器中的 [ **inputdata** ] 資料夾。 
    
3. 成功上傳的檔案後，您會看到類似以下從 AzCopy 輸出。
    
        Finished 1 of total 1 file(s).
        [2015/12/16 23:07:33] Transfer summary:
        -----------------
        Total files transferred: 1
        Transfer successfully:   1
        Transfer skipped:        0
        Transfer failed:         0
        Elapsed time:            00.00:00:01
5. 執行下列命令以**partitionweblogs.hql**檔案上傳至**adfgetstarted**容器的**指令碼**] 資料夾。 以下是命令︰ 
    
        AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

您已完成的必要條件。 您可以建立資料工廠使用下列方法之一。 按一下其中一個索引標籤的頂端或執行教學課程下列連結。 

- [Azure 入口網站](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)