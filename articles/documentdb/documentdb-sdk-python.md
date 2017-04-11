<properties 
    pageTitle="DocumentDB Python API 及 SDK |Microsoft Azure" 
    description="了解 Python API 及 SDK 包含發行日期、 退休日期和 DocumentDB Python SDK 的每個版本之間所做的變更。" 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB Api 和 Sdk

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [其餘](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>DocumentDB Python API 及 SDK

<table>
<tr><td>**下載 SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**API 文件**</td><td>[Python API 參照文件](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**SDK 的安裝指示**</td><td>[Python SDK 的安裝指示](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**參與 sdk 的變更**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**快速入門**</td><td>[快速入門 Python SDK](documentdb-python-application.md)</td></tr>
<tr><td>**目前支援的平台**</td><td>[Python 2.7](https://www.python.org/downloads/)和[Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>版本資訊

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- 加入的支援 Python 3.5。
- 加入的支援連接共用使用要求模組。
- 加入工作階段的一致性支援。
- 加入的支援分割集合的頂端/ORDERBY 查詢。


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- 新增重試流速控制要求的原則支援。 （流速控制的要求會收到要求工資率太大的例外狀況，出現錯誤碼 429）。根據預設，DocumentDB 重試九個時間為每個要求發生時，出現錯誤碼 429，接受回應標頭 retryAfter 時間。 固定重試間隔時間可以立即設定屬於 RetryOptions 屬性 ConnectionPolicy 物件上如果您想要忽略伺服器所傳回的重試之間的 retryAfter 時間。 現在 30 秒的最大值等候要求的 DocumentDB （不管資料重試計數） 正在經流速控制，並傳回錯誤碼 429 回應。 這次也會覆寫 ConnectionPolicy 物件上的 [RetryOptions] 屬性中。

- DocumentDB 現在傳回 x-ms-流速-重試-count 和 x-ms-throttle-retry-wait-time-ms 回應標頭中每個要求來表示流速重試 count 和 cummulative 時間等待重試之間的要求。

- 移除 RetryPolicy 類別和公開 document_client 類別上的對應屬性 (retry_policy)，而且改為公開 ConnectionPolicy 類別，可以用來覆寫預設重試] 選項的部分的 RetryOptions 屬性 RetryOptions 課程。

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - 新增多區域資料庫帳戶支援。

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- 新增時間至 Live(TTL) 文件的功能支援。

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- 伺服器端分割 partitionkey 路徑中允許的特殊字元相關錯誤修正。

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- 實作的[分割的集合](documentdb-partition-data.md)及[使用者定義的效能等級](documentdb-performance-levels.md)。 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- 新增雜湊與範圍分割解析程式來協助跨多個資料分割 sharding 應用程式。

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- 實作了。 新增至支援了功能的新 UpsertXXX 方法。
- 實作 ID 依據路由。 沒有公用 API 的變更，所有變更內部。

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- 支援休士頓索引。
- 驗證之所有資源的 [識別碼] 屬性。 資源識別碼不能包含 ?，/ #，\,字元或結尾空格。
- 新增 ResourceResponse 新增標題 」 索引轉換進度]。

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- 實作 V2 編製索引的原則。

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- 支援 proxy 連線。

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK。

## <a name="release--retirement-dates"></a>發行版本與淘汰網站的日期
Microsoft 提供通知的至少開始前事先淘汰 SDK 才能平滑轉換為較新/受支援版本的**12 個月**。

新功能和功能及最佳化只會新增至目前 SDK，因此其會建議您永遠升級為最新 SDK 版本早越好]。 

使用已停用的 SDK DocumentDB 任何要求會被拒絕服務。

> [AZURE.WARNING]
Azure DocumentDB SDK python 之前版本**1.0.0**所有版本會在**2016 年 2 月 29 日**上已停都用。 

<br/>

| 版本 | 發行日期 | 退休日期 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | 2016 年 9 月 29日日 |---
| [1.9.0](#1.9.0) | 2016 年 7 月 07 日 |---
| [1.8.0](#1.8.0) | 2016 年 6 月 14日日 |---
| [1.7.0](#1.7.0) | 2016 年 4 月 26 |---
| [1.6.1](#1.6.1) | 2016 年 4 月 08 日 |---
| [1.6.0](#1.6.0) | 2016 年 3 月 29日日 |---
| [1.5.0](#1.5.0) | 2016 年 1 月 03 日 |---
| [1.4.2](#1.4.2) | 2015 年 10 月 06 日 |---
| [1.4.1](#1.4.1) | 2015 年 10 月 06 日 |---
| [1.2.0](#1.2.0) | 2015 年 8 月 06 日 |---
| [1.1.0](#1.1.0) | 2015 年 7 月 09 日 |---
| [1.0.1](#1.0.1) | 25 2015 年 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 07 日 |---
| 0.9.4-prelease | 2015 年 1 月 14日日 | 2016 年 2 月 29日日
| 0.9.3-prelease | 2014 年 12 月 09 日 | 2016 年 2 月 29日日
| 0.9.2-prelease | 2014 年 11 月 25 日 | 2016 年 2 月 29日日
| 0.9.1-prelease | 2014 年 9 月 23日日 | 2016 年 2 月 29日日
| 0.9.0-prelease | 2014 年 8 月 21 日 | 2016 年 2 月 29日日

## <a name="faq"></a>常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>另請參閱

若要進一步瞭解 DocumentDB，請參閱[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)服務] 頁面。 
