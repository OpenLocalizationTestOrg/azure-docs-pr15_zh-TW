<properties 
   pageTitle="檢視 [診斷記錄 Azure 資料湖市集 |Microsoft Azure" 
   description="瞭解如何設定和存取 Azure 資料湖市集診斷記錄 " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>存取 Azure 資料湖市集診斷記錄

進一步瞭解如何啟用診斷記錄您的資料湖存放帳戶，來檢視您的帳戶收集的記錄的方式。

組織可以啟用診斷記錄的提供的使用者存取資料，頻率資料的存取，例如清單資訊其 Azure 資料湖存放帳戶來收集資料存取稽核線索多少資料都會儲存在 [帳戶] 等。

## <a name="prerequisites"></a>必要條件

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- **Azure 資料湖存放帳戶**。 請遵循在[開始使用 Azure 資料湖存放區使用 Azure 入口網站](data-lake-store-get-started-portal.md)的指示進行。

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>啟用診斷記錄您的資料湖存放區帳戶

1. 登入新的[Azure 入口網站](https://portal.azure.com)。

2. 開啟您資料湖儲存的帳戶，然後從您的資料湖存放帳戶刀，按一下 [**設定**]，然後按一下**診斷設定**。

3. 在 [**診斷**刀，變更下列設定診斷記錄。

    ![啟用診斷記錄](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "啟用診斷記錄")

    * 若要**在**啟用診斷記錄設定**狀態**。
    * 您可以選擇儲存/處理程序資料兩種方式。
        * 選取到 Azure 事件集線器串流記錄資料匯出**至事件中心**的選項。 很可能是您會使用這個選項，如果您有下游處理管線分析在即時內送的記錄。 如果您選取此選項時，您必須提供您想要使用 [Azure 事件中心詳細資料。
        * 選取要**匯出至儲存帳戶**儲存記錄檔給 Azure 儲存體帳戶的選項。 如果您想要保存的資料，將會批次處理之後，您可以使用這個選項。 如果您選取這個選項，您必須提供儲存記錄檔給 Azure 儲存體帳戶。
    * 指定您是否要取得稽核記錄或要求記錄檔或兩者。
    * 指定其必須保留資料的天數。
    * 按一下 [**儲存**]。

一旦您已啟用診斷設定，您可以觀看 [**診斷記錄**] 索引標籤中的記錄。

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>檢視您的資料湖存放帳戶的診斷記錄

有兩種方式檢視資料湖存放帳戶記錄的資料。

* 從資料湖存放帳戶檢視設定
* 從 Azure 儲存體帳戶儲存資料的位置

### <a name="using-the-data-lake-store-settings-view"></a>使用資料湖存放區 」 設定檢視

1. 從您資料湖存放區帳戶**設定**把刀中，按一下 [**診斷記錄**]。

    ![檢視診斷記錄](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "檢視診斷記錄") 

2. 在 [**診斷記錄**刀中，您應該會看到依**稽核記錄**，以及**要求記錄**分類的記錄。
    * 要求記錄檔擷取每個資料湖存放帳戶所做的 API 邀請。
    * 稽核記錄類似要求記錄，但提供更詳細的分析資料湖存放帳戶所執行的作業。 例如，要求記錄中的單一上傳 API 呼叫可能會導致稽核記錄中的多個 「 新增 」 作業。

3. 按一下 [**下載**] 連結，針對每一個下載記錄的記錄項目]。

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>從 Azure 儲存體帳戶含有記錄資料

1. 開啟記錄，相關聯資料湖存放 Azure 儲存體帳戶刀，然後按一下 [二進位大型物件。 **Blob 服務**刀列出兩個容器。

    ![檢視診斷記錄](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "檢視診斷記錄")

    * 容器**的深入見解-記錄-稽核**包含稽核記錄。
    * 容器**的深入見解記錄要求**包含要求記錄檔。

2. 這些容器內的記錄儲存在下列結構。

    ![檢視診斷記錄](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "檢視診斷記錄")

    例如，可能是稽核記錄檔的完整路徑`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    要求記錄的完整路徑可能是 similary，`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>了解記錄資料的結構

稽核與要求記錄位於 JSON 格式。 在此區段中，我們會查看要求 JSON 結構，並稽核記錄。

### <a name="request-logs"></a>要求記錄檔

在 [JSON 格式化要求登入，以下是範例項目。 每個 blob 有一個名為包含陣列的記錄物件的**記錄**的根物件。

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>要求記錄結構描述

| 名稱            | 類型   | 描述                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| 時間            | 字串 | 記錄檔的時間戳記 （以 utc 表示）                                              |
| 預設      | 字串 | 作業的資源的識別碼置於                            |
| 類別        | 字串 | [記錄] 類別中。 例如，**要求**。                                   |
| operationName   | 字串 | 作業的記錄名稱。 例如，getfilestatus。              |
| resultType      | 字串 | 例如，200 作業的狀態。                                 |
| callerIpAddress | 字串 | 提出要求的用戶端 IP 位址                                |
| 相互關聯識別碼   | 字串 | 可以的記錄檔的識別碼用於分成一組相關的記錄項目 |
| 身分識別        | 物件 | 產生的登入身分識別                                            |
| 屬性      | JSON   | 如需詳細資訊，請參閱下面的                                                          |

#### <a name="request-log-properties-schema"></a>要求記錄屬性結構描述

| 名稱                 | 類型   | 描述                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | 字串 | HTTP 方法用於作業。 例如，取得。 |
| 路徑                 | 字串 | 在上執行作業的路徑                   |
| RequestContentLength | int    | HTTP 要求的內容的長度                    |
| ClientRequestId      | 字串 | 可唯一識別此要求 Id              |
| 開始時間            | 字串 | 用以伺服器收到要求的時間         |
| 結束時間              | 字串 | 在其伺服器傳送回應的時間              |

### <a name="audit-logs"></a>稽核記錄

以下是範例項目 JSON 格式化稽核記錄中。 每個 blob 有一個名為包含陣列的記錄物件的**記錄**的根物件

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>稽核記錄檔結構描述

| 名稱            | 類型   | 描述                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| 時間            | 字串 | 記錄檔的時間戳記 （以 utc 表示）                                              |
| 預設      | 字串 | 作業的資源的識別碼置於                            |
| 類別        | 字串 | [記錄] 類別中。 例如，**稽核**。                                      |
| operationName   | 字串 | 作業的記錄名稱。 例如，getfilestatus。              |
| resultType      | 字串 | 例如，200 作業的狀態。                                 |
| 相互關聯識別碼   | 字串 | 可以的記錄檔的識別碼用於分成一組相關的記錄項目 |
| 身分識別        | 物件 | 產生的登入身分識別                                            |
| 屬性      | JSON   | 如需詳細資訊，請參閱下面的                                                          |

#### <a name="audit-log-properties-schema"></a>稽核記錄檔的屬性結構描述

| 名稱       | 類型   | 描述                              |
|------------|--------|------------------------------------------|
| StreamName | 字串 | 在上執行作業的路徑  |


## <a name="samples-to-process-the-log-data"></a>若要處理的記錄資料的範例

Azure 資料湖存放如何處理程序及分析該記錄的資料提供的範例。 您可以在[https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)上找到的範例。 


## <a name="see-also"></a>另請參閱

- [Azure 資料湖存放區的概觀](data-lake-store-overview.md)
- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)

