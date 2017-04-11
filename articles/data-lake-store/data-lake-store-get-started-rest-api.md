<properties 
   pageTitle="開始使用資料湖存放使用 REST API |Microsoft Azure" 
   description="使用 WebHDFS REST Api 來執行資料湖存放區上的作業" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Azure 資料湖存放區使用 REST Api 快速入門

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

在本文中，您將學習如何使用 WebHDFS REST Api 及資料湖市集 REST Api 來執行帳戶管理，以及 Azure 資料湖存放區上的檔案系統作業。 Azure 資料湖存放公開帳戶管理作業自己 REST Api。 不過，因為資料湖存放與 HDFS 和 Hadoop 生態相容，它支援 WebHDFS REST Api 用檔案系統的作業。

>[AZURE.NOTE] 如 REST API 的詳細資訊的支援資料湖存放區，請參閱[Azure 資料湖市集 REST API 參考](https://msdn.microsoft.com/library/mt693424.aspx)。

## <a name="prerequisites"></a>必要條件

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- **建立的 Azure Active Directory 應用程式**。 您可以使用 Azure AD 應用程式來驗證 Azure AD 資料湖市集應用程式。 有驗證 Azure AD，也就是**使用者驗證**或**服務-服務驗證**的不同方法。 如需相關指示與驗證方法的詳細資訊，請參閱[驗證資料湖存放區使用 Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)。

- [cURL](http://curl.haxx.se/)。 本文會使用捲曲，示範如何以進行資料湖存放帳戶 REST API 通話。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何驗證使用 Azure Active Directory？

您可以使用兩種方法，使用 Azure Active Directory 進行驗證。

### <a name="end-user-authentication-interactive"></a>使用者驗證 （互動式）

在此案例中，應用程式會提示使用者登入，所有作業都執行中的使用者內容。 互動式驗證，請執行下列步驟。

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

在此案例中，此應用程式提供自己的認證以執行作業。 此，您必須發行文章要求類似以下所示。 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

此要求的輸出會包含授權權杖 (以`access-token`以下的輸出中) 就會與 REST API 來電傳遞。 此驗證的權杖儲存文字檔案。您會需要此本文稍後的。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

本文使用**非互動式**的方法。 如需非互動 （服務-服務通話） 的詳細資訊，請參閱[使用認證的服務通話的服務](https://msdn.microsoft.com/library/azure/dn645543.aspx)。

## <a name="create-a-data-lake-store-account"></a>建立資料湖存放區帳戶

這項作業根據 REST API 通話定義[以下](https://msdn.microsoft.com/library/mt694078.aspx)。

使用下列捲曲命令。 取代**\<yourstorename >**使用您的資料湖存放名稱。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

在上方的命令，將\< `REDACTED` \>與授權權杖您擷取較舊版本。 所提供的**input.json**檔案中包含此命令的要求裝載`-d`上述參數。 Input.json 檔案的內容如下所示︰

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>資料湖存放區帳戶建立資料夾

這項作業根據 WebHDFS REST API 通話定義[以下](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)。

使用下列捲曲命令。 取代**\<yourstorename >**使用您的資料湖存放名稱。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

在上方的命令，將\< `REDACTED` \>與授權權杖您擷取較舊版本。 這個命令會建立在根資料夾中的資料湖存放帳戶稱為**mytempdir**目錄。

如果作業成功完成，您應該會看到的回應，像這樣︰

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>清單資料湖存放帳戶的資料夾

這項作業根據 WebHDFS REST API 通話定義[以下](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)。

使用下列捲曲命令。 取代**\<yourstorename >**使用您的資料湖存放名稱。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

在上方的命令，將\< `REDACTED` \>與授權權杖您擷取較舊版本。

如果作業成功完成，您應該會看到的回應，像這樣︰

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>上傳至資料湖存放帳戶的資料

這項作業根據 WebHDFS REST API 通話定義[以下](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)。

上傳使用 WebHDFS REST API 資料是執行的程序，如下所述。

1. 提交 HTTP 放置要求而不傳送上傳的檔案資料。 在以下命令，將**\<yourstorename >**使用您的資料湖存放名稱。

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    這個命令的輸出效果將會包含暫時重新導向 URL 想所示。

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. 現在，您必須送出另一個 HTTP 放置的要求，針對所列的 [在回應中的 [**位置**] 屬性的 URL。 取代**\<yourstorename >**使用您的資料湖存放名稱。

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    輸出會類似下列動作︰

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>讀取資料湖存放帳戶中的資料

這項作業根據 WebHDFS REST API 通話定義[以下](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)。

帳戶讀取資料湖存放區中的資料是執行的程序。

* 第一次提交 GET 要求針對端點`https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`。 這會傳回提交的下一個 GET 要求的位置。
* 然後提交針對端點 GET 要求`https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`。 這會顯示檔案的內容。

不過，因為沒有輸入參數中第一個與第二個步驟之間的差異，您可以使用`-L`送出第一次要求的參數。 `-L`選項基本上成一份合併兩個要求，並會使捲曲取消復原上的新位置的要求。 最後，所有的要求呼叫的輸出會顯示，類似如下所示。 取代**\<yourstorename >**使用您的資料湖存放名稱。

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

您應該會看到類似以下的輸出︰

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>重新命名資料湖儲存帳戶檔案

這項作業根據 WebHDFS REST API 通話定義[以下](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)。

您可以使用下列捲曲命令來重新命名檔案。 取代**\<yourstorename >**使用您的資料湖存放名稱。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

您應該會看到類似以下的輸出︰

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>刪除資料湖儲存帳戶檔案

這項作業根據 WebHDFS REST API 通話定義[以下](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)。

若要刪除檔案中使用下列捲曲命令。 取代**\<yourstorename >**使用您的資料湖存放名稱。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

您應該會看到如下所示的輸出︰

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>刪除資料湖存放區帳戶

這項作業根據 REST API 通話定義[以下](https://msdn.microsoft.com/library/mt694075.aspx)。

若要刪除資料湖存放帳戶使用下列捲曲命令。 取代**\<yourstorename >**使用您的資料湖存放名稱。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

您應該會看到如下所示的輸出︰

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>另請參閱

- [開啟來源大資料應用程式相容於 Azure 資料湖存放區](data-lake-store-compatible-oss-other-applications.md)
 
