<properties
    pageTitle="建立使用貓熊 Azure blob 儲存資料的功能 |Microsoft Azure"
    description="如何建立之資料的儲存貓熊 Python 套件 Azure blob 容器中的功能。"
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
    ms.author="bradsev;garye" />

#<a name="create-features-for-azure-blob-storage-data-using-panda"></a>建立使用貓熊 Azure blob 儲存資料的功能

這份文件會顯示如何建立使用[熊](http://pandas.pydata.org/)Python 套件 Azure blob 容器中所儲存的資料的功能。 之後大綱如何將資料載入到貓熊資料圖文框，它會顯示如何產生 Python 指令碼使用指標值和 binning 功能分類的功能。

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]說明如何建立資料的功能，在不同環境中的主題此**功能表**連結。 這項工作是[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的步驟。


## <a name="prerequisites"></a>必要條件

本文假設您建立 Azure blob 儲存體帳戶，並儲存您的資料。 如果您需要設定的帳戶的相關指示，請參閱[建立 Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)


## <a name="load-the-data-into-a-pandas-data-frame"></a>將資料載入到熊資料圖文框
才能執行探索及管理資料集，它必須下載 blob 來源至然後可以載入熊資料框架中的本機檔案。 以下是要遵循此程序的步驟︰

1. 下載的資料從 Azure blob 使用 blob 服務的下列範例 Python 程式碼。 下列程式碼中的變數取代您特定的值︰

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

##<a name="blob-featuregen"></a>功能產生器

接下來的兩個區域顯示如何產生分類的功能，與標記值和 binning 使用 Python 指令碼的功能。

###<a name="blob-countfeature"></a>指標值以產生功能

您可以建立類別的功能，如下所示︰

1. 檢查 [分類] 資料行的通訊群組︰

        dataframe_blobdata['<categorical_column>'].value_counts()

2. 產生的每個資料行值的指標值

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. 加入與原始資料框架的 [標記] 欄

            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. 移除原始變數本身︰

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

###<a name="blob-binningfeature"></a>Binning 功能產生器

產生 binned 的功能，我們繼續，如下所示︰

1. 新增一系列的區間數字的資料行的資料行

        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)

2. 轉換 binning 布林變數的順序

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')

3. 最後，回到原始的資料範圍中加入 dummy 變數

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

##<a name="sql-featuregen"></a>資料重新寫入 Azure blob 和使用中 Azure 電腦學習

您有探索資料並建立所需的功能，您可以上傳資料之後 (取樣或 featurized) 至 Azure blob 和 Azure 電腦學習使用下列步驟中使用它︰ 請注意，可以建立 Azure 電腦學習 Studio 以及其他功能。
1. 撰寫資料圖文框至本機檔案

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 將上傳資料至 Azure blob，如下所示︰

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

3. 現在可以從下列畫面所示，使用 Azure 電腦學習[匯入資料](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)模組 blob 讀取資料︰

![閱讀程式 blob](./media/machine-learning-data-science-process-data-blob/reader_blob.png)
