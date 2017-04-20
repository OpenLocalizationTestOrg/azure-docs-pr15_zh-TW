<properties 
    pageTitle="處理與進階分析的 Azure blob 資料 |Microsoft Azure" 
    description="在 Azure Blob 存放區中的程序資料。" 
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
    ms.date="09/19/2016"
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>使用進階的分析的程序 Azure blob 資料

本文件涵蓋探索資料及產生的資料儲存在 Azure Blob 存放區中的功能。 

## <a name="load-the-data-into-a-pandas-data-frame"></a>將資料載入熊資料圖文框
若要探索及操作資料集，它必須下載 blob 來源至然後熊資料圖文框中載入本機檔案。 若要遵循此程序的步驟如下：

1. 下載 （英文） 使用 blob 服務的下列範例 Python 程式碼與 blob Azure 中的資料。 以您特定的值取代下列程式碼中的變數： 

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


2. 讀入下載的檔案從熊資料圖文框中的資料。

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

現在您就可以探索資料及產生此資料集上的功能。


##<a name="blob-dataexploration"></a>資料探索

以下是一些範例探索使用熊資料的方式：

1. 檢查列和欄的數 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. 檢查中做為以下的資料集的第一筆或最後一個幾列：

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. 檢查每個資料行已匯入使用下列範例程式碼的資料類型
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. 檢查資料組中的各欄的基本 stats，如下所示
 
        dataframe_blobdata.describe()
    
5. 以下列方式查看每個欄值的項目數目

        dataframe_blobdata['<column_name>'].value_counts()

6. Count 遺失值與實際的項目中使用下列範例程式碼的各欄數

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  如果您有遺失值的特定欄資料中，您可以如下拖：

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    另一種方式來取代遺失值為函數的模式：
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. 建立使用可變數目之筒繪製變數的散佈長條圖繪圖 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. 查看變數使用 scatterplot 或使用內建的相互關聯函數之間的相互關聯

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    
    
##<a name="blob-featuregen"></a>功能產生
    
我們可以產生，如下所示使用 Python 功能：

###<a name="blob-countfeature"></a>標記值型功能產生

分類的功能可以建立如下：

1. 檢查分類資料行的通訊群組：
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. 每個欄值產生標記值

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. 加入與原始資料圖文框的 [標記] 欄 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. 移除原始變數本身：

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>Binning 功能產生

針對產生 binned 的功能，我們繼續執行，如下所示：

1. 新增欄至 bin 數值資料行的順序
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. 轉換至 boolean 變數的順序 binning

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. 最後，回到原始資料圖文框加入虛設變數

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 


##<a name="sql-featuregen"></a>以 Azure blob 回寫資料及取用在 Azure 機器學習

您已探索資料並建立所需的功能，您可以將資料上傳之後 (時或 featurized) 至 Azure blob 及取用在 Azure 機器學習使用下列步驟： 請注意可以在 Azure 機器學習 Studio 中，以及建立額外的功能。 
1. 資料圖文框寫入本機檔案

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 將資料上傳至 Azure blob，如下所示：

        from azure.storage.blob import BlobService
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

3. 現在可使用 Azure 機器學習[匯入資料]的 blob 從讀取資料[import-data]模組下列畫面所示：
 
![讀取者 blob][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
