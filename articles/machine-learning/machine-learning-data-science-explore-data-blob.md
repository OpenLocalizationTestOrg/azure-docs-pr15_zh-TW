<properties 
    pageTitle="探索與熊 Azure blob 儲存體中的資料 |Microsoft Azure" 
    description="如何瀏覽使用熊 Azure blob 容器中所儲存的資料。" 
    services="machine-learning,storage" 
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

#<a name="explore-data-in-azure-blob-storage-with-pandas"></a>探索與熊 Azure blob 儲存體中的資料

這份文件會說明如何探索 Azure blob 容器使用[熊](http://pandas.pydata.org/)Python 套件中所儲存的資料。

主題說明如何使用工具來探索資料從各種不同的儲存空間環境下列**功能表**連結。 這項工作是在[資料科學程序]()中的步驟。

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## <a name="prerequisites"></a>必要條件
本文假設您有︰

* 建立 Azure 儲存體帳戶。 如果您需要的指示，請參閱[建立 Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)
* 儲存在 Azure blob 儲存體帳戶中的資料。 如果您需要的指示，請參閱[移動資料與 Azure 儲存體](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>將資料載入到熊 DataFrame
若要瀏覽及管理資料集，它必須第一次下載 blob 來源的資料至本機的檔案，然後在熊 DataFrame 載入。 以下是要遵循此程序的步驟︰

1. 下載的資料從 Azure blob 下列 Python 程式碼範例使用 blob 服務使用。 下列程式碼中的變數取代您特定的值︰ 

        from azure.storage.blob import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. 讀取資料轉換為所下載的檔案從熊資料圖文框。

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

現在您已經準備好探索資料，並產生在此資料集的功能。

##<a name="blob-dataexploration"></a>使用熊的資料瀏覽的範例

以下是一些範例使用熊瀏覽資料的方式︰

1. 檢查**列數和欄數** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **檢查**第一個或最後一個幾個**資料列**中下列資料集︰

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. 檢查每一欄匯入為使用下列程式碼的**資料類型**
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. 檢查**基本的統計**資料集的欄，如下所示
 
        dataframe_blobdata.describe()
    
5. 查看每個資料行值的項目數目，如下所示

        dataframe_blobdata['<column_name>'].value_counts()

6. 與使用下列程式碼的每一欄中的項目的實際次數**計數遺漏的值**

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  如果您**遺失值**的特定資料行中有資料，您可以放，如下所示︰

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    若要取代遺失的值的另一個方法是使用模式函數︰
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. 建立使用可變的區間數目繪製一個變數的**直方圖**繪圖 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. 看看變數使用 scatterplot 或使用內建的相互關聯函數之間**的關聯性**

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
