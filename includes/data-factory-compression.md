### <a name="compression-support"></a>壓縮支援  
處理大型資料集，可能會導致 I/O 和網路瓶頸。 因此，壓縮的存放區中的資料可以不只加速在網路上的資料傳輸並節省磁碟空間，但也會顯示在處理大型資料的重要的效能提升。 目前，例如 Azure Blob 或內部部署檔案系統檔案為基礎的資料儲存區支援壓縮。  

> [AZURE.NOTE] 壓縮設定不支援**AvroFormat**、 **OrcFormat**或**ParquetFormat**中的資料。 

若要指定壓縮的資料集，請使用 [**壓縮**] 屬性中資料集 JSON，如下列範例所示︰   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
[**壓縮**] 區段會有兩個屬性︰  
  
- **類型︰**壓縮轉碼器，可能是**GZIP**、**結實**或**BZIP2**。  
- **層級︰**壓縮比例，可為**Optimal**或**最快**。 
    - **快︰**即使未最佳化壓縮所產生的檔案時，應盡快，完成壓縮作業。 
    - **Optimal**︰ 壓縮作業應最佳壓縮，即使該作業耗費很長的時間才能完成。 
    
    如需詳細資訊，請參閱[壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)主題。 

上述範例資料集作為複製活動的成果，假設複製活動的輸出資料壓縮 GZIP 轉碼器使用最佳比例]，然後撰寫到名稱為 pagecounts.csv.gz Azure Blob 儲存體中的檔案的 [壓縮的資料。   

當您輸入的資料集 JSON 中指定壓縮屬性時，管線可以讀取壓縮的資料來源的資料和您在資料集中輸出 JSON，指定屬性，複製活動可以撰寫壓縮的資料至的目的地。 以下是一些範例案例︰ 

- 從 Azure blob，閱讀 GZIP 壓縮資料解壓縮，並將結果資料寫入 Azure SQL 資料庫。 您可以在此情況下定義壓縮 JSON 屬性輸入的 Azure Blob 資料集。 
- 讀取純文字檔案從內部部署檔案系統中的資料、 壓縮 GZip 格式和 Azure blob 的寫入壓縮的資料。 您可以在此情況下定義壓縮 JSON 屬性輸出 Azure Blob 資料集。  
- 讀取 GZIP 壓縮資料從 Azure blob、 解壓縮它、 壓縮使用 BZIP2，和寫入 Azure blob 的結果資料。 您可以定義輸入的 Azure Blob 資料集與壓縮類型設為 GZIP 與輸出資料集，並在本例中為 BZIP2 壓縮類型。   
