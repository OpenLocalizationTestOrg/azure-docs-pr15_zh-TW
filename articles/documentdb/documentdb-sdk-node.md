<properties
    pageTitle="DocumentDB Node.js API 及 SDK |Microsoft Azure"
    description="瞭解所有的 Node.js API 及 SDK 包含發行日期、 退休日期和 DocumentDB Node.js SDK 的每個版本之間所做的變更。"
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB Api 和 Sdk

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [其餘](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>DocumentDB Node.js API 及 SDK

<table>
<tr><td>**下載 SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**API 文件**</td><td>[Node.js API 參考文件](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**SDK 的安裝指示**</td><td>[安裝指示](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**參與 sdk 的變更**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**範例**</td><td>[Node.js 程式碼範例](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**開始使用教學課程**</td><td>[快速入門 Node.js SDK](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Web 應用程式教學課程**</td><td>[建立使用 DocumentDB Node.js web 應用程式](documentdb-nodejs-application.md)</td></tr>
<tr><td>**目前支援的平台**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>版本資訊

###<a name="1.10.0"/>1.10.0</a>

- 加入的支援交叉分割平行查詢。
- 頂端/ORDER BY 集合的查詢分割加入的支援。

###<a name="1.9.0"/>1.9.0</a>

- 新增重試流速控制要求的原則支援。 （流速控制的要求會收到要求工資率太大的例外狀況，出現錯誤碼 429）。根據預設，DocumentDB 重試九個時間為每個要求發生時，出現錯誤碼 429，接受回應標頭 retryAfter 時間。 固定重試間隔時間可以立即設定屬於 RetryOptions 屬性 ConnectionPolicy 物件上如果您想要忽略伺服器所傳回的重試之間的 retryAfter 時間。 現在 30 秒的最大值等候要求的 DocumentDB （不管資料重試計數） 正在經流速控制，並傳回錯誤碼 429 回應。 這次也會覆寫 ConnectionPolicy 物件上的 [RetryOptions] 屬性中。

- DocumentDB 現在傳回 x-ms-流速-重試-count 和 x-ms-throttle-retry-wait-time-ms 回應標頭中每個要求來表示流速重試 count 和 cummulative 時間等待重試之間的要求。

- 已新增 RetryOptions 類別，公開可以用來覆寫預設重試] 選項的一些 ConnectionPolicy 類別的 RetryOptions 屬性。

###<a name="1.8.0"/>1.8.0</a>

 - 新增多區域資料庫帳戶支援。

###<a name="1.7.0"/>1.7.0</a>

- 新增時間至 Live(TTL) 文件的功能支援。

###<a name="1.6.0"/>1.6.0</a>

- 實作的[分割的集合](documentdb-partition-data.md)及[使用者定義的效能等級](documentdb-performance-levels.md)。

###<a name="1.5.6"/>1.5.6</a>

- 固定的 RangePartitionResolver.resolveForRead bug 位置，也不傳回連結，因為不正確的 concat 函數的結果。

###<a name="1.5.5"/>1.5.5</a>

- 固定 hashParitionResolver resolveForRead()︰ 時提供沒有分割索引鍵擲回例外狀況，而不會傳回所有已註冊的連結清單。

###<a name="1.5.4"/>1.5.4</a>

- 修正問題[#100](https://github.com/Azure/azure-documentdb-node/issues/100) -專用的 HTTPS 代理程式︰ 避免修改 DocumentDB 用途的全域代理程式。 使用專用的代理程式的所有文件庫的要求。

###<a name="1.5.3"/>1.5.3</a>

- 修正問題[#81](https://github.com/Azure/azure-documentdb-node/issues/81)正確控點虛線媒體識別碼中。

###<a name="1.5.2"/>1.5.2</a>

- 修正問題[#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter 接聽遺漏警告。

###<a name="1.5.1"/>1.5.1</a>

- 修正問題[# 頁，共 92](https://github.com/Azure/azure-documentdb-node/issues/90)重新命名資料夾雜湊雜湊區分大小寫的系統。

### <a name="1.5.0"/>1.5.0</a>

- 藉由新增雜湊與範圍的磁碟分割解析程式實作 sharding 支援。

### <a name="1.4.0"/>1.4.0</a>

- 實作了。 新 upsertXXX documentClient 方法。

### <a name="1.3.0"/>1.3.0</a>

- 略過中與其他 Sdk 的對齊方式將版本號碼。

### <a name="1.2.2"/>1.2.2</a>

- 分割問承諾包裝紙到新的儲存機制。
- 更新 npm 登錄的套件檔案。

### <a name="1.2.1"/>1.2.1</a>

- 實作識別碼根據路由。
- 修正問題[#49](https://github.com/Azure/azure-documentdb-node/issues/49) -目前屬性與方法 current() 衝突。

### <a name="1.2.0"/>1.2.0</a>

- 加入的支援休士頓索引。
- 驗證之所有資源的 [識別碼] 屬性。 資源識別碼不能包含？，/ #，與 #47; 與 #47;，字元或結尾空格。
- 新增 ResourceResponse 新增標題 」 索引轉換進度]。

### <a name="1.1.0"/>1.1.0</a>

- 實作 V2 編製索引的原則。

### <a name="1.0.3"/>1.0.3</a>

- 問題 [#40] (https://github.com/Azure/azure-documentdb-node/issues/40)-實作 eslint 和步兵組態核心和品 SDK 中。

### <a name="1.0.2"/>1.0.2</a>

- 問題[#45](https://github.com/Azure/azure-documentdb-node/issues/45) -承諾包裝紙不包含錯誤的標題。

### <a name="1.0.1"/>1.0.1</a>

- 實作的能夠藉由新增 readConflicts、 readConflictAsync 和 queryConflicts 衝突的查詢的詳細資訊。
- 更新的 API 文件。
- 議題[#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync 錯誤。

### <a name="1.0.0"/>1.0.0</a>

- GA SDK。

## <a name="release--retirement-dates"></a>發行版本與淘汰網站日期
Microsoft 提供通知的至少開始前事先淘汰 SDK 才能平滑轉換為較新/受支援版本的**12 個月**。

新功能和功能及最佳化只會新增至目前 SDK，因此其會建議您永遠升級為最新 SDK 版本早越好]。

使用已停用的 SDK DocumentDB 任何要求會被拒絕服務。

> [AZURE.WARNING]
之前版本**1.0.0** Node.js Azure DocumentDB SDK 的所有版本會在**2016 年 2 月 29 日**上已停都用。

<br/>

| 版本 | 發行日期 | 退休日期
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 2016 年 10 月 03 日 |---
| [1.9.0](#1.9.0) | 2016 年 7 月 07 日 |---
| [1.8.0](#1.8.0) | 2016 年 6 月 14日日 |---
| [1.7.0](#1.7.0) | 2016 年 4 月 26 |---
| [1.6.0](#1.6.0) | 2016 年 3 月 29日日 |---
| [1.5.6](#1.5.6) | 2016 年 3 月 08 日 |---
| [1.5.5](#1.5.5) | 2016 年 2 月 02 日 |---
| [1.5.4](#1.5.4) | 2016 年 2 月 01 日 |---
| [1.5.2](#1.5.2) | 2016 年 1 月 26 |---
| [1.5.2](#1.5.2) | 2016 年 1 月 22日日 |---
| [1.5.1](#1.5.1) | 2016 年 1 月 4日日 |---
| [1.5.0](#1.5.0) | 2015 年 12 月 31日日 |---
| [1.4.0](#1.4.0) | 2015 年 10 月 06 日 |---
| [1.3.0](#1.3.0) | 2015 年 10 月 06 日 |---
| [1.2.2](#1.2.2) | 2015 年 9 月 10日日 |---
| [1.2.1](#1.2.1) | 2015 年 8 月 15日日 |---
| [1.2.0](#1.2.0) | 2015 年 8 月 05 日 |---
| [1.1.0](#1.1.0) | 2015 年 7 月 09 日 |---
| [1.0.3](#1.0.3) | 2015 年 6 月 04 日 |---
| [1.0.2](#1.0.2) | 2015 年月 23日日 |---
| [1.0.1](#1.0.1) | 2015 年月 15日日 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 08 日 |---
| 0.9.4-prerelease | 2015 年 4 月 06 日 | 2016 年 2 月 29日日
| 0.9.3-prerelease | 2015 年 1 月 14日日 | 2016 年 2 月 29日日
| 0.9.2-prerelease | 2014 年 12 月 18 日 | 2016 年 2 月 29日日
| 0.9.1-prerelease | 2014 年 8 月 22日日 | 2016 年 2 月 29日日
| 0.9.0-prerelease | 2014 年 8 月 21 日 | 2016 年 2 月 29日日


## <a name="faq"></a>常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>另請參閱

若要進一步瞭解 DocumentDB，請參閱[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)服務] 頁面。
