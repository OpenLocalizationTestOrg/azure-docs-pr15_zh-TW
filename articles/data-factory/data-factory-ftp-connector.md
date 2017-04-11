<properties 
    pageTitle="將資料從 FTP 伺服器移 |Microsoft Azure" 
    description="瞭解如何將資料從使用 Azure 資料工廠 FTP 伺服器。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>將資料從 FTP 伺服器使用 Azure 資料工廠移動
本文概述如何使用複製活動 Azure 資料工廠會將資料從 FTP 伺服器支援的接收資料存放區。 本文是根據呈現資料移動的一般概觀複製活動與支援當做來源/接收的資料存放區清單[資料移動活動](data-factory-data-movement-activities.md)文件。 

資料工廠目前支援只移動資料從 FTP 伺服器其他資料儲存區中，但無法將資料從其他資料儲存區移到 FTP 伺服器。 支援這兩個內部部署與雲端 FTP 伺服器。 

如果您從**內部部署**FTP 伺服器移動資料至雲端的資料儲存區 (範例︰ Azure Blob 儲存體)、 安裝及使用資料管理閘道器。 資料管理閘道是您可讓連接到內部部署資源的雲端服務的內部部署電腦已安裝的用戶端代理程式。 閘道器的詳細資訊，請參閱[資料管理閘道器](data-factory-data-management-gateway.md)。 設定 [閘道器，以及使用，請參閱[內部部署的位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文件，如需逐步指示。 您可以使用閘道器至 FTP 伺服器連線，即使伺服器位於 Azure IaaS 虛擬機器 (VM)。 

您可以安裝在相同的內部部署電腦或 Azure IaaS VM 的閘道器 FTP 伺服器。 不過，我們建議您安裝的閘道器，在不同的電腦或另一個 Azure IaaS VM，以避免資源競爭以及更佳的效能。 當您在不同的電腦上安裝的閘道器時，電腦應能夠存取 FTP 伺服器。 

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立將資料從 FTP 伺服器的管線最簡單的方法是使用複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>範例︰ FTP 伺服器將資料複製到 Azure blob

此範例會示範如何 FTP 伺服器將資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

- 連結的類型[FtpServer](#ftp-linked-service-properties)的服務。
- 連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
- 輸入的[資料集](data-factory-create-datasets.md)的類型[檔案共用](#fileshare-dataset-type-properties)。
- 輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
- [管線](data-factory-create-pipelines.md)與使用[FileSystemSource](#ftp-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從 FTP 伺服器到 Azure blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

**FTP 連結服務**此範例使用基本驗證的使用者名稱和密碼以純文字。 您也可以使用下列方法之一︰ 

- 匿名驗證 
- 使用加密認證的基本驗證
- FTP 透過 SSL/TLS (FTP)

請參閱驗證您可以使用的不同類型的[FTP 連結服務](#ftp-linked-service-properties)一節。 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
    }

**Azure 連結的儲存服務**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**FTP 輸入資料集**此資料集參照 FTP 資料夾`mysharedfolder`和檔案`test.csv`。 管線會將檔案複製到目的地。 

設定 「 外部 」:"true"通知資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**含複製活動的管線**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **FileSystemSource** ，**接收**類型] 設定為**BlobSink**。 
    
    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>FTP 連結服務屬性

下表提供 JSON 項目的特定 FTP 連結的服務描述。

| 屬性 | 描述 | 所需 | 預設值 |
| -------- | ----------- | -------- | ------- | 
| 類型 | [類型] 屬性必須設定為 FtpServer | [是] | &nbsp;
| host （主機) | FTP 伺服器的名稱或 IP 位址 | [是] | &nbsp;
| 識別碼 | 指定驗證類型 | [是] | 基本、 匿名 |
| 使用者名稱 | 有權存取 FTP 伺服器的使用者 | 無 | &nbsp;
| 密碼 | 使用者 （使用者名稱） 的密碼 | 無 | &nbsp;
| encryptedCredential | 若要存取 FTP 伺服器的加密的認證 | 無 | &nbsp;
| gatewayName | 資料管理閘道器閘道連線到內部部署 FTP 伺服器的名稱 | 無    | &nbsp;
| 連接埠 | 要在其接聽 FTP 伺服器連接埠 | 無 | 21 |
| enableSsl | 指定是否要使用 FTP 透過 SSL/TLS 通道 | 無 | true | 
| enableServerCertificateValidation | 指定是否要使用 FTP 透過 SSL/TLS 通道時，啟用伺服器 SSL 憑證驗證 | 無 | true | 

### <a name="using-anonymous-authentication"></a>使用匿名驗證

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>以純文字的使用者名稱和密碼使用基本驗證
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>使用連接埠、 enableSsl enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>使用 encryptedCredential 驗證和閘道器

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

如需詳細資訊設定針對內部部署 FTP 資料來源的認證，請參閱[設定認證與安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>FTP 複製活動類型的內容

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 針對所有類型的活動有內容，例如名稱、 描述、 輸入與輸出資料表及原則。 

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動類型內容而有所不同的來源和接收類型。

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。

## <a name="next-steps"></a>後續步驟
請參閱下列文章︰ 

- 如需逐步指示建立含複製活動的管線[複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 
