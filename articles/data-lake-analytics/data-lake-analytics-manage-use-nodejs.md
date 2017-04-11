<properties
   pageTitle="管理 Azure 資料湖分析 Azure SDK 用 Node.js |Azure"
   description="瞭解如何管理資料湖分析帳戶、 資料來源、 工作和 Azure SDK 用 Node.js 的使用者"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>管理 Azure SDK 用 Node.js Azure 資料湖分析


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure SDK Node.js 可用於管理 Azure 資料湖分析帳戶、 工作和類別目錄。 若要查看管理主題使用其他工具，按一下 [] 索引標籤選取上方。

您現在支援︰

  *  **Node.js 版本︰ 0.10.0 或更新版本**
  *  **REST API 版帳戶︰ 2015年-10-01-預覽**
  *  **目錄的 REST API 版︰ 2015年-10-01-預覽**
  *  **為工作的 REST API 版本︰ 2016年 03 20-預覽**

## <a name="features"></a>功能

- 帳戶管理︰ 建立、 取得、 清單、 更新和刪除。
- 工作管理︰ 送出，取得清單、 取消。
- 型錄管理︰ 取得、 清單、 建立 （密碼）、 更新 （密碼），請刪除 （密碼）。

## <a name="how-to-install"></a>如何安裝

```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>使用 Azure Active Directory 進行驗證

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>建立資料湖分析用戶端

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>建立資料湖分析帳戶

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
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

## <a name="get-a-list-of-jobs"></a>取得工作的清單

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>取得資料湖分析目錄] 中的資料庫的清單
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>另請參閱

- [Microsoft Azure SDK Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK Node.js-資料湖管理](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)
