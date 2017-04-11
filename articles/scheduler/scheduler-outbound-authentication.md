<properties
 pageTitle="排程器輸出驗證"
 description="排程器輸出驗證"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>排程器輸出驗證

排程器作業可能需要撥需要驗證的服務。 如此一來，稱為的服務可以決定排程器工作是否可以存取其資源。 這些服務部分包含其他 Azure 服務、 Salesforce.com、 Facebook 和安全的自訂網站。

## <a name="adding-and-removing-authentication"></a>新增及移除驗證

新增至排程器工作驗證很簡單 – 新增 JSON 子項目`authentication`至`request`時建立或更新工作項目。 機密資料傳遞至放入、 修補程式或文章要求 – 的排程器服務屬於`authentication`物件 – 永遠不會在回應中傳回。 在回覆私人資訊已設為空值，或可能會有公用的權杖，代表已驗證的實體。

若要移除驗證，將或更新工作，設定`authentication`物件為 null。 您不會看到回回應任何驗證內容。

目前，只支援的驗證類型是`ClientCertificate`模型 （適用於使用 SSL/TLS 用戶端憑證），`Basic`模型 （適用於基本驗證），以及`ActiveDirectoryOAuth`模型 （適用於 Active Directory OAuth 驗證。）

## <a name="request-body-for-clientcertificate-authentication"></a>要求 ClientCertificate 驗證本文

新增驗證使用時`ClientCertificate`模型、 指定在邀請內文中的下列其他項目。  

|項目|描述|
|:---|:---|
|_驗證 （父項目）_|使用 SSL 用戶端憑證驗證物件。|
|_類型_|所需。 驗證類型。值必須是 SSL 用戶端憑證， `ClientCertificate`。|
|_pfx_|所需。 Base64 編碼 PFX 檔案的內容。|
|_密碼_|所需。 若要存取 PFX 檔案的密碼。|


## <a name="response-body-for-clientcertificate-authentication"></a>回應 ClientCertificate 驗證的內容

驗證資訊會傳送要求，回應會包含下列驗證相關項目。

|項目 |描述 |
|:--|:--|
|_驗證 （父項目）_ |使用 SSL 用戶端憑證驗證物件。|
|_類型_ |驗證類型。 SSL 用戶端憑證，該值`ClientCertificate`。|
|_certificateThumbprint_ |憑證的指紋。|
|_certificateSubjectName_ |憑證主旨辨別的名稱。|
|_certificateExpiration_ |到期日的憑證。|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>範例其餘要求 ClientCertificate 驗證

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>範例其餘回應 ClientCertificate 驗證

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>基本驗證的邀請內文

新增驗證使用時`Basic`模型、 指定在邀請內文中的下列其他項目。

|項目|描述|
|:--|:--|
|_驗證 （父項目）_ |使用基本驗證的驗證物件。|
|_類型_ |所需。 驗證類型。 基本驗證的值必須是`Basic`。|
|_使用者名稱_ |所需。 若要驗證的使用者名稱。|
|_密碼_ |所需。 要驗證的密碼。|

## <a name="response-body-for-basic-authentication"></a>基本驗證的回應內容

驗證資訊會傳送要求，回應會包含下列驗證相關項目。

|項目|描述|
|:--|:--|
|_驗證 （父項目）_ |使用基本驗證的驗證物件。|
|_類型_ |驗證類型。 基本驗證的值是`Basic`。|
|_使用者名稱_ |經過驗證的使用者名稱。|

## <a name="sample-rest-request-for-basic-authentication"></a>基本驗證的範例其餘要求

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>範例基本驗證的其餘回應

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>要求 ActiveDirectoryOAuth 驗證本文

新增驗證使用時`ActiveDirectoryOAuth`模型、 指定在邀請內文中的下列其他項目。

|項目 |描述 |
|:--|:--|
|_驗證 （父項目）_ |使用 ActiveDirectoryOAuth 驗證驗證物件。|
|_類型_ |所需。 驗證類型。 值必須是 ActiveDirectoryOAuth 驗證`ActiveDirectoryOAuth`。|
|_租用戶_ |所需。 Azure AD 租用戶租用戶識別碼。|
|_對象_ |所需。 這是設定為 https://management.core.windows.net/。|
|_clientId_ |所需。 Azure AD 應用程式中提供的用戶端識別碼。|
|_密碼_ |所需。 要求權杖用戶端的密碼。|

### <a name="determining-your-tenant-identifier"></a>判斷您的租用戶識別碼

您也可以執行 Azure AD 租用戶找到租用戶識別碼`Get-AzureAccount`PowerShell 的 Azure 中。

## <a name="response-body-for-activedirectoryoauth-authentication"></a>回應 ActiveDirectoryOAuth 驗證的內容

驗證資訊會傳送要求，回應會包含下列驗證相關項目。

|項目 |描述 |
|:--|:--|
|_驗證 （父項目）_ |使用 ActiveDirectoryOAuth 驗證驗證物件。|
|_類型_ |驗證類型。 ActiveDirectoryOAuth 驗證，該值`ActiveDirectoryOAuth`。|
|_租用戶_ |Azure AD 租用戶租用戶識別碼。 |
|_對象_ |這是設定為 https://management.core.windows.net/。|
|_clientId_ |Azure AD 應用程式的用戶端識別碼。|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>範例其餘要求 ActiveDirectoryOAuth 驗證

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>範例其餘回應 ActiveDirectoryOAuth 驗證

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>另請參閱


 [什麼是排程器？](scheduler-intro.md)

 [Azure 排程器概念、 詞彙，以及實體階層](scheduler-concepts-terms.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [方案與帳單方面的 Azure 排程器](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制與預設值]，出現錯誤碼](scheduler-limits-defaults-errors.md)
