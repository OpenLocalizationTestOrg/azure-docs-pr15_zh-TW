<properties 
   pageTitle="開始使用 Azure SDK 用 Node.js Azure 資料湖儲存 |Microsoft Azure"
   description="瞭解如何使用 Node.js 處理資料湖存放帳戶與檔案系統。" 
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
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Azure 資料湖存放 Azure SDK 用 Node.js 快速入門

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)


瞭解如何使用 Node.js Azure SDK 建立 Azure 資料湖存放帳戶，並執行基本作業，例如建立資料夾上, 傳和下載資料檔案，刪除您的帳戶、 等等。如需有關資料湖存放區的詳細資訊，請參閱[資料湖存放概觀](data-lake-store-overview.md)。 目前 SDK 支援

  *  **Node.js 版本︰ 0.10.0 或更新版本**
  *  **REST API 版帳戶︰ 2015年-10-01-預覽**
  *  **檔案系統的 REST API 版︰ 2015年-10-01-預覽**

## <a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- **建立的 Azure Active Directory 應用程式**。 您可以使用 Azure AD 應用程式來驗證 Azure AD 資料湖市集應用程式。 有驗證 Azure AD，也就是**使用者驗證**或**服務-服務驗證**的不同方法。 如需相關指示與驗證方法的詳細資訊，請參閱[驗證資料湖存放區使用 Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)。

## <a name="how-to-install"></a>如何安裝

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>使用 Azure Active Directory 進行驗證

下列程式碼片段顯示兩個不同的方式，驗證資料湖存放區的使用 Azure AD。 驗證資料湖存放區要使用的各種方法的詳細說明，請參閱[使用資料湖存放區驗證使用 Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)。

下列程式碼片段也需要輸入如 Azure AD 網域名稱，用戶端識別碼 Azure AD 應用程式等。從 Azure AD 應用程式，您必須建立，其中的詳細資料，也會在上述的連結，可以擷取這些詳細資料。

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>建立資料湖存放用戶端

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>建立資料湖存放區帳戶

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>建立檔案的內容
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>取得檔案與資料夾清單

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>另請參閱

- [Microsoft Azure SDK Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK Node.js-湖分析的資料管理](https://www.npmjs.com/package/azure-arm-datalake-analytics)
