<properties 
    pageTitle="DocumentDB.NET API 及 SDK |Microsoft Azure" 
    description="瞭解所有的.NET API 及 SDK 包含發行日期、 退休日期和 DocumentDB.NET SDK 的每個版本之間所做的變更。" 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB Api 和 Sdk 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [其餘](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>DocumentDB.NET API 及 SDK

<table>
<tr><td>**SDK 下載**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**API 文件**</td><td>[.NET API 參照文件](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**範例**</td><td>[.NET 程式碼範例](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**快速入門**</td><td>[快速入門 DocumentDB.NET SDK](documentdb-get-started.md)</td></tr>
<tr><td>**Web 應用程式教學課程**</td><td>[使用 DocumentDB web 應用程式開發](documentdb-dotnet-application.md)</td></tr>
<tr><td>**目前支援的架構**</td><td>[Microsoft.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>版本資訊

> [AZURE.IMPORTANT] 開始使用版本 1.9.2 版本，您可能會收到 System.NotSupportedException 時查詢分割的集合。 若要避免此錯誤，請確定您主機處理程序是 64 位元。 可執行專案，這可以完成取消核取 [專案屬性] 視窗中，在 [建立] 索引標籤上的 [」 偏好 32 位元 「] 選項。

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - 新增直接連線支援分割的集合。
  - 改良的效能限制過時一致性層級。
  - 新增多邊形和 LineString 資料類型時指定原則地理圍欄空間查詢編製索引的集合。
  - 加入的 LINQ 支援 StringEnumConverter、 IsoDateTimeConverter 和 UnixDateTimeConverter 時翻譯述詞。
  - 各種 SDK 錯誤修正。

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - 修正造成下列 NotFoundException 問題︰ 了解工作階段不適用於輸入工作階段權杖。 此時發生例外狀況在某些情況下地理分佈的帳戶已讀取] 區域的查詢。
  - 公開 ResponseStream 類別中的屬性 ResourceResponse，讓直接存取基礎資料流從回應。

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - 修改 ResourceResponse、 FeedResponse、 StoredProcedureResponse 和 MediaResponse 類別實作的相對應的公用介面，讓他們可以模仿測試導向部署 (TDD)。
  - 修正導致不正確的磁碟分割的金鑰標題序列化資料中使用自訂的 JsonSerializerSettings 物件時的問題。

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - 修正導致失敗長時間執行查詢與錯誤的問題︰ 授權權杖不是有效在目前的時間。
  - 修正問題的移除從原始 SqlParameterCollection 交叉分割頂端/依查詢。

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - 加入的支援分割集合平行查詢。
  - 加入的支援交叉分割集合的磁碟分割順序和上方查詢。
  - 修正遺漏參照 DocumentDB.Spatial.Sql.dll 和 Microsoft.Azure.Documents.ServiceInterop.dll 所需的參考 DocumentDB Nuget 套件參照 DocumentDB 專案時。
  - 固定 LINQ 中使用使用者定義函數時，請使用不同類型的參數的能力。 
  - 固定全域複寫帳戶 bug 位置了通話已被導向至閱讀，而不是寫入位置的位置。
  - 新增的方法 IDocumentClient 介面已遺失︰ 
      - UpsertAttachmentAsync 方法 mediaStream 和做為參數的選項
      - 做為參數的選項 CreateAttachmentAsync 方法
      - CreateOfferQuery querySpec 做為參數的方法。
  - 非密封公用類別中 IDocumentClient 介面公開。

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - 新增多區域資料庫帳戶支援。
  - 重試流速控制要求加入的支援。  使用者可以自訂數重試和最大等待時間設定 ConnectionPolicy.RetryOptions 屬性。
  - 加入新的 IDocumentClient 介面定義所有 DocumenClient 屬性和方法的簽章。  這項變更的一部分，同時變更建立 IQueryable 和 IOrderedQueryable 上 DocumentClient 類別本身的方法延伸方法。
  - 新增設定指定 DocumentDB 端點 Uri ServicePoint.ConnectionLimit 設定選項。  若要變更預設值，其設定為 50 使用 ConnectionPolicy.MaxConnectionLimit。
  - 已遭取代的 IPartitionResolver 及其實作。  現在已過時 IPartitionResolver 支援。 建議您用於分割集合較高的儲存空間及處理量。


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - 新增至 Uri 超載基礎做為參數 RequestOptions ExecuteStoredProcedureAsync 方法。
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - 新增的時間至 live (TTL) 支援文件。

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - 封裝為 Azure 雲端服務方案的一部分的.NET SDK 的 Nuget 封裝修正錯誤。
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - 實作的[分割的集合](documentdb-partition-data.md)及[使用者定義的效能等級](documentdb-performance-levels.md)。 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[固定]**查詢 DocumentDB 端點擲回: 「 System.Net.Http.HttpRequestException︰ 將內容複製到資料流時，發生錯誤。

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - 展開的 LINQ 支援包括新的運算子的分頁，條件運算式，而範圍比較。
    - 需要運算子來啟用在 LINQ 選取頂端行為
    - 若要啟用字串範圍比較 CompareTo 運算子
    - 設定條件 （？） 和聯合運算子 （？）
  - **[固定]**合併與模型預測時的 ArgumentOutOfRangeException 位置集 linq 查詢。  [# 81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[固定]**如果不選取最後一個運算式 LINQ 提供者假設沒有預測和產生選取 * 不正確。  [# 58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - 實作的了，新增 UpsertXXXAsync 方法
 - 所有要求提升效能
 - 設定的條件，LINQ 提供者支援聯合和 CompareTo 字串的方法
 - **[固定]**LINQ 提供者--> 實作包含產生 IEnumerable 及陣列的相同 SQL 清單的方法
 - **[固定]**BackoffRetryUtility 使用相同的 HttpRequestMessage 一次，而不是建立一個新重試
 - **[已過時]**1--UriFactory.CreateCollection 現在應該使用 UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[固定]**已進行本地化問題時使用非短文化特性資訊，例如 nl NL 等。 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID 路由
    - 若要協助建構識別碼的新 UriFactory 協助依據資源的連結
    - 新的多載上 DocumentClient 才能 URI
  - 新增 IsValid() 和 IsValidDetailed() linq 的休士頓
  - 增強型 LINQ 提供者支援
    - **數學**Abs，Acos，Asin Atan，Ceiling Cos Exp、 樓面、 記錄、 Log10、 Pow、 Round、 符號、 Sin、 Sqrt，Tan，則捨去
    - **字串**concat 函數，包含 EndsWith IndexOf、 Count、 ToLower、 TrimStart、 取代、 反向 TrimEnd、 StartsWith、 子字串 ToUpper
    - **陣列**concat 函數，包含計數
    - **IN**運算子

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - 新增的支援修改編製索引的原則
    - DocumentClient 中的新 ReplaceDocumentCollectionAsync 方法
    - 新的 IndexTransformationProgress 屬性中 ResourceResponse<T>追蹤索引原則變更的百分比的進度
    - DocumentCollection.IndexingPolicy 現在已可變動
  - 新增的支援空間編製索引和查詢
    - 新 Microsoft.Azure.Documents.Spatial 命名空間序列化/序列化空間類型等點及多邊形
    - 新 SpatialIndex 類別 GeoJSON 資料儲存在 DocumentDB 編製索引
  - **[固定]** : 從 linq 運算式[#38](https://github.com/Azure/azure-documentdb-net/issues/38)產生不正確的 SQL 查詢

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Newtonsoft.Json v5.0.7 之間的相依性 
- 變更順序的支援
  - OrderBy() 或 OrderByDescending() LINQ 提供者支援
  - Order By 支援 IndexingPolicy 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- 使用新的 HashPartitionResolver 和 RangePartitionResolver 類別和 IPartitionResolver 分割資料的支援
- DataContract 序列化
- Guid 支援 LINQ 提供者中
- 在 LINQ UDF 支援

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]
預覽及 GA.之間 NuGet 套件名稱變更時 我們從**Microsoft.Azure.Documents.Client**移到**Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- [已過時] 預覽 Sdk

## <a name="release--retirement-dates"></a>發行版本與淘汰網站日期
Microsoft 提供通知的至少開始前事先淘汰 SDK 才能平滑轉換為較新/受支援版本的**12 個月**。

新功能和最佳化的功能僅限新增目前 sdk 的變更，因此我們建議您永遠升級為最新 SDK 版本早越好]。 

使用已停用的 SDK DocumentDB 任何要求會被拒絕服務。

> [AZURE.WARNING]
Azure DocumentDB SDK.net 之前版本**1.0.0**所有版本會在**2016 年 2 月 29 日**上已停都用。 
 
<br/>
 
| 版本 | 發行日期 | 退休日期 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 2016 年 9 月 27日日 |---
| [1.9.5](#1.9.5) | 2016 年 9 月 01 日 |---
| [1.9.4](#1.9.4) | 2016 年 8 月 24 |---
| [1.9.3](#1.9.3) | 2016 年 8 月 15日日 |---
| [1.9.2](#1.9.2) | 2016 年 7 月 23日日 |---
| 1.9.1 | 已遭取代 |---
| 1.9.0 | 已遭取代 |---
| [1.8.0](#1.8.0) | 2016 年 6 月 14日日 |---
| [1.7.1](#1.7.1) | 2016 可能 06， |---
| [1.7.0](#1.7.0) | 2016 年 4 月 26 |---
| [1.6.3](#1.6.3) | 2016 年 4 月 08 日 |---
| [1.6.2](#1.6.2) | 2016 年 3 月 29日日 |---
| [1.5.3](#1.5.3) | 2016 年 2 月 19 日 |---
| [1.5.2](#1.5.2) | 2015 年 12 月 14日日 |---
| [1.5.1](#1.5.1) | 2015 年 11 月 23日日 |---
| [1.5.0](#1.5.0) | 2015 年 10 月 05 日 |---
| [1.4.1](#1.4.1) | 2015 年 8 月 25 日 |---
| [1.4.0](#1.4.0) | 2015 年 8 月 13日日 |---
| [1.3.0](#1.3.0) | 2015 年 8 月 05 日 |---
| [1.2.0](#1.2.0) | 2015 年 7 月 06 日 |---
| [1.1.0](#1.1.0) | 2015 年 4 月 30日日 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 08 日 |---
| [0.9.3-prelease](#0.9.x-preview) | 2015 年 3 月 12日日 | 2016 年 2 月 29日日
| [0.9.2-prelease](#0.9.x-preview) | 2015 年 1 月日 | 2016 年 2 月 29日日
| [.9.1-prelease](#0.9.x-preview) | 2014 年 10 月 13日日 | 2016 年 2 月 29日日
| [0.9.0-prelease](#0.9.x-preview) | 2014 年 8 月 21 日 | 2016 年 2 月 29日日

## <a name="faq"></a>常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>另請參閱

若要進一步瞭解 DocumentDB，請參閱[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)服務] 頁面。 
