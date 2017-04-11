<properties 
    pageTitle="範例資料的 SQL Server Azure 上 |Microsoft Azure" 
    description="在 SQL Server Azure 上中的範例資料" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>在 SQL Server Azure 上中的範例資料


這份文件會顯示如何範例資料儲存在 SQL Server Azure 在使用 SQL 或 Python 程式設計語言。 也會顯示如何將取樣的資料移到 Azure 電腦學習，以將其儲存至檔案、 上傳至 Azure blob，然後再讀取該 Azure 電腦學習 studio。

Python 範例使用[pyodbc](https://code.google.com/p/pyodbc/) ODBC 文件庫連線至 SQL Server Azure 和[熊](http://pandas.pydata.org/)文件庫，若要執行的範例。

>[AZURE.NOTE] 在這份文件中的範例 SQL 程式碼假設的資料是在 SQL Server Azure 上。 如果尚未選取，請參閱[移動到 SQL Server Azure 上資料](machine-learning-data-science-move-sql-server-virtual-machine.md)的主題，如需如何將資料移到 SQL Server，Azure 上的指示。

**為什麼範例資料？**
如果您要分析的資料集很大，通常是最好的方法向下範例資料，以縮減為較小但代表且更容易管理的大小。 這有助於功能工程、 探索和資料的瞭解程度。 啟用快速原型處理資料函數及電腦學習模型為其[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的角色。

下方的**功能表**主題說明如何將來自各種不同的儲存空間環境的連結。 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

此範例工作是[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的步驟。

##<a name="SQL"></a>使用 SQL

本節說明使用 SQL 資料庫中執行簡單的隨機範例，對資料有數種方法。 請根據您的資料大小和其通訊群組的方法。

下列兩個項目會顯示如何在 SQL Server 中使用 newid，若要執行的範例。 您選擇的方法，取決於您想要範例隨機 （在下面的範例 pk_id 假設為自動產生的主索引鍵）。

1. 嚴格較少的隨機範例

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. 更多隨機範例 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample 可以會用於取樣，以及下方所示。 若您的資料大小 （假設不同的頁面上的資料不會相互關聯），並在合理的時間完成查詢，這可能是較好的方法。

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] 您可以瀏覽並從這個取樣資料產生功能，以將其儲存至新的資料表


###<a name="sql-aml"></a>連線至 Azure 機器學習

您可以直接使用範例查詢上方 Azure 電腦學習[匯入資料][import-data]向下範例即時的資料，並放到 Azure 電腦學習實驗的模組。 使用閱讀程式模組閱讀取樣的資料的螢幕擷取畫面所示︰
   
![閱讀程式 sql][1]

##<a name="python"></a>使用程式設計語言 Python 

此區段中，將示範如何使用[pyodbc 文件庫](https://code.google.com/p/pyodbc/)建立 ODBC 連線至 SQL server 資料庫中 Python。 資料庫連接字串如下: （取代伺服器名稱、 dbname 引數、 使用者名稱和密碼設定）︰

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python[熊](http://pandas.pydata.org/)文件庫提供資料管理 Python 程式豐富的資料結構及資料分析工具。 下列程式碼會熊資料從 Azure SQL 資料庫中的表格資料的 0.1%範例︰

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

您現在可以使用取樣資料熊資料框架中。 

###<a name="python-aml"></a>連線至 Azure 機器學習

您可以使用下列程式碼將向下取樣資料儲存為檔案，並將它上傳至 Azure blob。 Blob 中的資料可以直接將 Azure 電腦學習實驗用讀取[匯入資料][import-data]模組。 步驟如下所示︰ 

1. 撰寫熊資料圖文框至本機檔案

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 上傳至 Azure blob 的本機檔案

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. 從 Azure blob 使用 Azure 電腦學習[匯入資料]讀取資料[import-data]模組下列畫面抓取所示︰
 
![閱讀程式 blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>小組資料科學處理程序] 動作範例

小組資料科學程序的端對端逐步解說範例使用公用資料集，請參閱[小組資料科學處理程序] 動作︰ 使用 SQL 伺服器](machine-learning-data-science-process-sql-walkthrough.md)。

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
