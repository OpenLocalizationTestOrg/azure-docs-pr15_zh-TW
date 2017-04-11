<properties 
   pageTitle="開始使用資料湖分析使用 REST API |Microsoft Azure" 
   description="在 [資料湖分析上執行作業使用 WebHDFS REST Api" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>使用 REST Api Azure 資料湖分析快速入門

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

瞭解如何使用 WebHDFS REST Api 和資料湖分析 REST Api 來管理資料湖分析帳戶、 工作和目錄。 

## <a name="prerequisites"></a>必要條件

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- **建立的 Azure Active Directory 應用程式**。 您可以使用 Azure AD 應用程式來驗證 Azure AD 資料湖分析應用程式。 有驗證 Azure AD，也就是**使用者驗證**或**服務-服務驗證**的不同方法。 如需相關指示與驗證方法的詳細資訊，請參閱[資料湖分析使用 Azure Active Directory 驗證方法](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。

- [cURL](http://curl.haxx.se/)。 本文會使用捲曲，示範如何以進行資料湖分析帳戶 REST API 通話。

## <a name="authenticate-with-azure-active-directory"></a>Azure Active Directory 驗證方法

有兩種方法可以與 Azure Active Directory 驗證。

### <a name="end-user-authentication-interactive"></a>使用者驗證 （互動式）

使用此方法，應用程式會提示使用者登入，並在使用者的內容中執行所有作業。 

請遵循下列步驟來互動式驗證︰

1. 透過您的應用程式中，將使用者導向至下列 URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<重新導向 URI > 需要用於 URL 編碼。 因此，如 https://localhost，使用`https%3A%2F%2Flocalhost`)

    針對此教學課程中，可以取代上方 URL 中的版面配置區，並將其貼於網頁瀏覽器的網址列。 您會被重新導向，以驗證您的 Azure 登入。 當您順利登入時，回應會顯示在瀏覽器的網址列中。 回應會以下列格式︰
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. 擷取回應的授權碼。 在此教學課程中，您可以從網頁瀏覽器的網址列複製授權程式碼並傳遞文章邀請中至 token 端點，如下所示︰

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] 在此情況下，\<重新導向 URI > 不需要編碼。

3. 回應是包含存取權杖 JSON 物件 (例如`"access_token": "<ACCESS_TOKEN>"`) 和重新整理權杖 (例如`"refresh_token": "<REFRESH_TOKEN>"`)。 您的應用程式時所使用的存取權杖存取 Azure 資料湖儲存和重新整理權杖存取權杖到期時，取得其他存取權杖。

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  當存取權杖到期時，您可以要求新的存取權杖，使用 [重新整理權杖，如下所示︰

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
如需有關互動式使用者驗證的詳細資訊，請參閱[授與流量的驗證碼](https://msdn.microsoft.com/library/azure/dn645542.aspx)。

### <a name="service-to-service-authentication-non-interactive"></a>若要服務驗證 （非互動式）

使用此方法時，應用程式提供自己的認證以執行作業。 您必須發行文章要求類似以下所示︰ 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

此要求的輸出會包含授權權杖 (以`access-token`以下的輸出中) 就會與 REST API 來電傳遞。 此驗證的權杖儲存文字檔案。您會需要此本文稍後的。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

本文使用**非互動式**的方法。 如需非互動 （服務-服務通話） 的詳細資訊，請參閱[使用認證的服務通話的服務](https://msdn.microsoft.com/library/azure/dn645543.aspx)。
## <a name="create-a-data-lake-analytics-account"></a>建立資料湖分析帳戶

您可以建立資料湖分析帳戶之前，您必須建立 Azure 資源] 群組中和資料湖存放帳戶。  請參閱[建立資料湖存放帳戶](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account)。

下列捲曲命令顯示如何建立帳戶︰

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

取代\< `REDACTED` \>與授權權杖， \< `AzureSubscriptionID` \>與您的訂閱識別碼\< `AzureResourceGroupName` \>與現有 Azure 資源群組名稱，並\< `NewAzureDataLakeAnalyticsAccountName` \>為新的資料湖分析帳戶名稱。 所提供的**CreateDatalakeAnalyticsAccountRequest.json**檔案中包含此命令的要求裝載`-d`上述參數。 Input.json 檔案的內容如下所示︰

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>在訂閱清單資料湖分析帳戶

下列捲曲命令顯示如何在清單中訂閱的帳戶︰

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

取代\< `REDACTED` \>與授權權杖， \< `AzureSubscriptionID` \>與您的訂閱 id。 輸出非常類似︰

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>取得資料湖分析帳戶的相關資訊

下列捲曲命令顯示如何取得帳戶資訊︰

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

取代\< `REDACTED` \>與授權權杖， \< `AzureSubscriptionID` \>與您的訂閱識別碼\< `AzureResourceGroupName` \>與現有 Azure 資源群組名稱，並\< `DataLakeAnalyticsAccountName` \>的現有資料湖分析帳戶名稱。 輸出非常類似︰

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>資料湖分析帳戶的清單資料湖存放區

下列捲曲命令顯示如何清單資料湖存放區上的帳戶︰

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

取代\< `REDACTED` \>與授權權杖， \< `AzureSubscriptionID` \>與您的訂閱識別碼\< `AzureResourceGroupName` \>與現有 Azure 資源群組名稱，並\< `DataLakeAnalyticsAccountName` \>的現有資料湖分析帳戶名稱。 輸出非常類似︰

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>提交 U SQL 工作

下列捲曲命令顯示如何提交 U SQL 工作︰

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

取代\< `REDACTED` \>與授權權杖， \< `DataLakeAnalyticsAccountName` \>的現有資料湖分析帳戶名稱。 所提供的**SubmitADLAJob.json**檔案中包含此命令的要求裝載`-d`上述參數。 Input.json 檔案的內容如下所示︰

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

輸出非常類似︰

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>清單 U SQL 工作

下列捲曲命令顯示如何列出 U SQL 作業︰

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

取代\< `REDACTED` \>與授權權杖，及\< `DataLakeAnalyticsAccountName` \>的現有資料湖分析帳戶名稱。 


輸出非常類似︰

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>取得目錄項目

下列捲曲命令顯示如何從目錄取得資料庫︰

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

輸出非常類似︰

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>另請參閱

- 若要查看較複雜的查詢，請參閱[使用 Azure 資料湖分析分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U SQL 應用程式，請參閱[使用資料湖工具的 Visual Studio 開發的 U SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要瞭解 U SQL，請參閱[開始使用 Azure 資料湖分析 U SQL 語言](data-lake-analytics-u-sql-get-started.md)。
- 管理工作，請參閱[管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析的概觀，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。
- 若要查看同一個教學課程中使用其他工具，請按一下 [上方的頁面] 索引標籤選擇器]。
- 若要登入的診斷資訊，請參閱[Azure 資料湖分析存取診斷記錄](data-lake-analytics-diagnostic-logs.md)
