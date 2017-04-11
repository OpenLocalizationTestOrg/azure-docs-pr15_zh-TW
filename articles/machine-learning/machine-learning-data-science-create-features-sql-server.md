<properties
    pageTitle="在使用 SQL 和 Python 的 SQL Server 中建立資料的功能 |Microsoft Azure"
    description="從 SQL Azure 處理資料"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;fashah;garye" />


# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>在使用 SQL 和 Python 的 SQL Server 中建立資料的功能


這份文件會顯示如何產生之資料儲存在 SQL Server VM 上 Azure 中的協助演算法更有效率地瞭解資料的功能。 這可以使用 SQL 或使用的程式設計語言，例如 Python，這兩種這裡示範完成。

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]說明如何建立資料的功能，在不同環境中的主題此**功能表**連結。 這項工作是[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的步驟。

> [AZURE.NOTE] 實用的範例中，您可以[NYC 計程車資料集](http://www.andresmh.com/nyctaxitrips/)，請參閱，並參考標題[NYC 資料 wrangling IPython 筆記本和 SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)的端對端逐步解說 IPNB。


## <a name="prerequisites"></a>必要條件
本文假設您有︰

* 建立 Azure 儲存體帳戶。 如果您需要的指示，請參閱[建立 Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)
* 在 SQL Server 中儲存您的資料。 如果您有不，請參閱[移動資料至 Azure SQL 資料庫的 Azure 電腦學習](machine-learning-data-science-move-sql-azure.md)如何將資料移指示。


## <a name="sql-featuregen"></a>以 SQL 的功能產生器

在此區段中，我們會說明產生使用 SQL 功能的方法︰  

1. [計數基礎功能產生器](#sql-countfeature)
2. [Binning 功能產生器](#sql-binningfeature)
3. [從單一資料行推出功能](#sql-featurerollout)


> [AZURE.NOTE] 後會產生其他功能，您可以將其新增至現有的資料表資料行，或建立新資料表的主索引鍵，可以加入與原始表格的其他功能。

### <a name="sql-countfeature"></a>計數基礎功能產生器

這份文件示範產生計算功能的兩種方式。 第一種方法使用條件式加總，並使用 「 何處 」 子句的第二個方法。 這些可以再加入原始資料表 （使用主索引鍵資料行） 有計算功能，同時原始資料。

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Binning 功能產生器

下列範例會顯示如何 binning （使用 5 區間） 來產生 binned 的功能數值的資料行，可以改為使用做為功能︰

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>從單一資料行推出功能

在此區段中，我們會示範如何以相簿出單一資料行的資料表，以產生其他功能。 假設您嘗試產生功能的資料表中有緯度或經度資料行。

以下是經緯度位置資料中的 [簡短入門 (從 stackoverflow 資源`http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`)。 這是對 featurizing 之前，了解 [位置] 欄位有幫助︰

- 註冊告訴我們我們是否北或南部、 東亞或西部地球上。
- 非零數百個位數告訴我們我們使用經度，不緯度 ！
- 百位數提供為關於 1000 公里的位置。 提供，讓我們瞭解哪些洲 」 或 「 我們目前正海洋有用的資訊。
- 單位數字 （1 的小數位數度） 可讓位置達 111 公里 （60 海哩，關於 69 英哩）。 它可以告訴我們大致大型的狀態為何我們的國家/地區。
- 第一個小數位數值得最 11.1 金鑰管理︰ 它可以區分的相鄰大城市從一個大型的城市位置。
- 第二個小數位數值得最 1.1 金鑰管理︰ 它可以一個村落分開下一步]。
- 第三個小數位數值得最 110 m︰ 可識別大型農業欄位或機構校園。
- 第四個小數位數值得最 11 m︰ 可辨識土地的資料。 這是沒有干擾相容一般精確度的未修正的 GPS 單位。
- 第五個小數位數值得最 1.1 m︰ 加以區別彼此樹。 只能使用差異校正達到商業 GPS 單位這個等級精確度。
- 第六個小數位數值得最 0.11 m︰ 您可以使用此配置結構的詳細資料，設計環境，建立道路。 很多個不錯的追蹤 glaciers 和河流計時。 這可以接受使用 GPS，例如 differentially 更正後的 GPS 無時無刻量值來達成。

位置資訊可以可 featurized，如下所示，分開區域、 位置及縣/市資訊。 請注意，一次也可以呼叫其餘終點，例如在 Bing 地圖服務 API`https://msdn.microsoft.com/library/ff701710.aspx`以取得區域/地區的資訊。

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

上述的位置以功能進一步可用來產生其他計算功能，如先前所述。


> [AZURE.TIP] 您可以以程式設計方式插入記錄中使用您所選擇的語言。 您可能需要插入區塊以改善寫入效率[請查看如何進行此動作使用 pyodbc 以下範例](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)中的資料。
另一個方法，是插入使用[BCP 公用程式](https://msdn.microsoft.com/library/ms162802.aspx)資料庫中的資料

### <a name="sql-aml"></a>連線至 Azure 機器學習

新產生的功能可以為資料行新增至現有資料表或儲存新的資料表並加入原始電腦學習的資料表。 功能可以產生或存取已經建立時，使用[匯入資料]](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)模組中 Azure 毫升，如下所示︰

![azureml 助讀程式](./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>使用這類 Python 的程式設計語言

使用 Python 的資料是在 SQL Server 時，會產生功能就像處理 Azure blob 使用 Python，如詳加說明[您資料科學環境中的程序 Azure Blob 資料](machine-learning-data-science-process-data-blob.md)中的資料。 資料必須從資料庫載入熊資料範圍，然後可以進一步處理。 我們的文件連接至資料庫並將資料載入資料框架本節中的程序。

下列的連接字串格式可用於連線至 SQL Server 資料庫從 Python 使用 pyodbc （取代伺服器名稱、 dbname 引數、 使用者名稱和密碼與特定值）︰

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[熊文件庫](http://pandas.pydata.org/)中 Python Python 程式的資料管理提供豐富的資料結構及資料分析工具。 下列程式碼會顯示從 SQL Server 資料庫傳回轉換為熊資料圖文框的結果如下︰

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

現在您可以使用熊資料框架涵蓋在主題中[建立使用貓熊 Azure blob 儲存資料的功能](machine-learning-data-science-create-features-blob.md)。
