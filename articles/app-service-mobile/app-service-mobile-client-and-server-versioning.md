<properties
  pageTitle="在行動應用程式和行動服務中的用戶端和伺服器 SDK 版本設定 |Azure 應用程式服務"
  description="用戶端 Sdk 的清單和行動服務和 Azure Mobile 應用程式的伺服器 SDK 版本的相容性"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>在行動應用程式和行動服務中的用戶端和伺服器版本

最新版 Azure 行動服務是 Azure 應用程式服務的**行動應用程式**功能。

行動應用程式的用戶端和伺服器 Sdk 原本根據那些在行動電話服務，但它們不*是*相容彼此。
也就是說，您必須使用的*行動應用程式*的用戶端 SDK 與*Mobile 應用程式*伺服器 SDK 和同樣的*行動電話服務*。 本合約會強制執行特殊標題值，使用用戶端和伺服器 Sdk，透過`ZUMO-API-VERSION`。

附註︰ 每當參照*行動服務*後端這份文件，它不會不一定需要會裝載於行動電話服務。 您就可以移轉不以任何程式碼的變更，應用程式服務上執行的行動訊息服務，但服務會仍在使用*行動電話服務*SDK 版本。

若要深入瞭解應用程式服務移轉不進行任何的程式碼變更，請參閱[移轉至 Azure 應用程式服務行動訊息服務]的文件。

## <a name="header-specification"></a>頁首規格

索引鍵`ZUMO-API-VERSION`可能會在 [HTTP 頁首] 或 [查詢字串中指定。 值為表單**x.y.z**版本字串。

例如︰

取得 https://service.azurewebsites.net/tables/TodoItem

標題︰ ZUMO API 版本︰ 2.0.0

文章 https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>參加版本檢查

您可以退出檢查設定值**，則為 true**的應用程式設定**MS_SkipVersionCheck**的版本。 在您 web.config 或 Azure 入口網站的 [應用程式設定] 區段中，請指定此選項。

> [AZURE.NOTE] 有的行動電話服務與 Mobile 應用程式，特別是在的離線同步處理、 驗證及推入通知區域之間的行為變更數字。 您應該只退出檢查完成測試，以確保這些行為變更不會中斷您的應用程式功能之後的版本。

## <a name="summary-of-compatibility-for-all-versions"></a>相容性，所有版本的摘要

下圖顯示所有的用戶端和伺服器類型之間的相容性。 後端被歸類為行動**服務**或行動**應用程式**以 SDK，它會在伺服器上。

|                           | **行動裝置的服務**Node.js 或.NET | **行動應用程式**Node.js 或.NET |
| ----------                | -----------------------             |   ----------------              |
| [行動電話服務用戶端] | 確定                                  | 錯誤\*                         |
| [行動應用程式的用戶端]     | 錯誤\*                             | 確定                              |

\*這可以藉由指定**MS_SkipVersionCheck**控制。


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>行動電話服務用戶端和伺服器

下表中的用戶端 Sdk 是相容於**行動電話服務**。

附註︰ 行動服務用戶端 Sdk*請不要*傳送標頭值`ZUMO-API-VERSION`。 如果服務收到此頁首] 或 [查詢字串值，則會傳回錯誤，除非縮小如上所述明確計。

### <a name="MobileServicesClients"></a>行動*服務*用戶端 Sdk

| 用戶端的平台                   | 版本                                                                   | 版本標頭值 |
| -------------------               | ------------------------                                                  | -------------------  |
| 受管理的用戶端 （Windows、 Xamarin） | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/a                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/a                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/a                  |
| HTML                              | [1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/a     |

### <a name="mobile-services-server-sdks"></a>行動*服務*伺服器 Sdk

| 伺服器平台  | 版本                                                                                                        | 接受的版本標頭 |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* 版本 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **沒有版本標頭** |
| Node.js          | （即將推出）                        | **沒有版本標頭** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>行動電話服務 backends 的行為

| ZUMO API 版本 | MS_SkipVersionCheck 的值 | 回應 |
| ---------------- | ---------------------------- | -------- |
| 未指定    | 任何                          | 200-[確定] |
| 任何值        | 為 true                         | 200-[確定] |
| 任何值        | False/不指定          | 400-錯誤的要求 |

## <a name="2.0.0"></a>Azure Mobile 應用程式的用戶端與伺服器

### <a name="MobileAppsClients"></a>行動*應用程式*用戶端 Sdk

檢查版本推出**Azure Mobile**應用程式開始著手的用戶端 SDK 下列版本︰

| 用戶端的平台                   | 版本                   | 版本標頭值 |
| -------------------               | ------------------------  | -----------------    |
| 受管理的用戶端 （Windows、 Xamarin） | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>在行動*應用程式*伺服器 Sdk

檢查版本包含在追蹤 SDK 的伺服器版本︰

| 伺服器平台  | SDK                                                                                                        | 接受的版本標頭 |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node.js          | [azure-mobile-應用程式）](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>行動應用程式 backends 的行為

| ZUMO API 版本 | MS_SkipVersionCheck 的值 | 回應 |
| ---------------- | ---------------------------- | -------- |
| x.y.z 或 Null    | 為 true                         | 200-[確定] |
| Null             | False/不指定          | 400-錯誤的要求 |
| 1.x.y            | False/不指定          | 400-錯誤的要求 |
| 2.0.0-2.x.y      | False/不指定          | 200-[確定] |
| 3.0.0-3.x.y      | False/不指定          | 400-錯誤的要求 |


## <a name="next-steps"></a>後續步驟

- [Azure 應用程式服務移轉行動訊息服務]


[行動電話服務用戶端]: #MobileServicesClients
[行動應用程式的用戶端]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Azure 應用程式服務移轉行動訊息服務]: app-service-mobile-migrating-from-mobile-services.md

