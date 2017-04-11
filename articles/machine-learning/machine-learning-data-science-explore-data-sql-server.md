<properties 
    pageTitle="Azure 上探索資料中的 SQL Server 虛擬機器 |Microsoft Azure" 
    description="如何瀏覽上 Azure SQL Server VM 中所儲存的資料。" 
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
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Azure 上探索 SQL Server 虛擬機器中的資料


這份文件會說明如何瀏覽上 Azure SQL Server VM 中所儲存的資料。 進行資料 wrangling 使用 SQL 或使用這類 Python 的程式設計語言。

主題說明如何使用工具來探索資料從各種不同的儲存空間環境下列**功能表**連結。 這項工作是 Cortana 分析程序 （端點） 中的步驟。

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] 在這份文件中的範例 SQL 陳述式假設資料的 SQL Server。 如果不是，請參閱雲端資料科學程序地圖，若要瞭解如何將資料移到 SQL Server。



## <a name="sql-dataexploration"></a>瀏覽 SQL 資料的 SQL 指令碼

以下是幾個 SQL 指令碼範例，可以用於瀏覽在 SQL Server 中的資料存放區。

1. 取得觀察每日的計數

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. 取得分類的資料行中的階層數

    `select  distinct <column_name> from <databasename>`

3. 在兩個類別的資料行的組合取得階層的數 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. 取得的分配的數值的資料行

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] 實用的範例中，使用[NYC 計程車資料集](http://www.andresmh.com/nyctaxitrips/)，請參閱標題為 「 」 [NYC 資料 wrangling IPython 筆記本和 SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)的端對端逐步解說 IPNB。

##<a name="python"></a>瀏覽與 Python SQL 資料

使用 Python 探索資料，並產生功能，SQL Server 資料時就像處理中使用 Python Azure blob 的資料，如詳加說明[程序 Azure Blob 資料科學環境中的資料](machine-learning-data-science-process-data-blob.md)。 資料必須從資料庫載入熊 DataFrame，然後可以進一步處理。 我們的文件連接至資料庫並將資料載入 DataFrame 本節中的程序。

下列的連接字串格式可用於連線至 SQL Server 資料庫從 Python 使用 pyodbc （取代伺服器名稱、 dbname 引數、 使用者名稱和密碼將特定的值）︰

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[熊文件庫](http://pandas.pydata.org/)中 Python Python 程式的資料管理提供豐富的資料結構及資料分析工具。 下列程式碼會顯示從 SQL Server 資料庫傳回轉換為熊資料圖文框的結果如下︰

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

現在您可以使用熊 DataFrame 涵蓋主題中的[程序 Azure Blob 資料科學環境中的資料](machine-learning-data-science-process-data-blob.md)。

## <a name="cortana-analytics-process-in-action-example"></a>在巨集指令範例 Cortana 分析程序

使用公用資料集 Cortana 分析程序的端對端逐步解說範例，請參閱[小組資料科學處理程序] 動作︰ 使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)。

 
