<properties 
    pageTitle="範例資料中 Azure blob 儲存體 |Microsoft Azure" 
    description="Azure Blob 儲存體中的範例資料" 
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

#<a name="heading"></a>範例資料中 Azure blob 儲存體


本文涵蓋以程式設計方式下載，然後取樣使用撰寫 Python 的程序所儲存的 Azure blob 儲存體中的範例資料。

**為什麼範例資料？**
如果您要分析的資料集很大，通常是最好的方法向下範例資料，以縮減為較小但代表且更容易管理的大小。 這有助於功能工程、 探索和資料的瞭解程度。 啟用快速原型處理資料函數及電腦學習模型可 Cortana 分析程序中的角色。

下方的**功能表**主題說明如何將來自各種不同的儲存空間環境的連結。 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

此範例工作是[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的步驟。


## <a name="download-and-down-sample-data"></a>下載並向下範例資料
1. 從 Azure blob 儲存體使用下列範例 Python 程式碼 blob 服務下載的資料︰ 

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

2. 讀取資料轉換為上方下載的檔案從熊資料圖文框。

        import pandas as pd

        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. 向下範例的資料使用`numpy`的`random.choice`，如下所示︰

        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

現在您可以使用在 1 百分比的範例日後進一步瀏覽和功能產生上述資料框架。

##<a name="heading"></a>上傳的資料，並將 Azure 電腦學習讀取

向下範例資料，並直接在 Azure 毫升中使用，您可以使用下列程式碼︰

1. 撰寫資料圖文框至本機檔案

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 本機的檔案上傳至 Azure blob，使用下列程式碼︰

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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. 讀取資料從 Azure blob 使用 Azure 毫升[匯入資料](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)下, 圖所示︰
 
![閱讀程式 blob](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)

 
