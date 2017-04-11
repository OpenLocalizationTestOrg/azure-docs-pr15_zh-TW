
<properties
    pageTitle="DocumentDB Java API 及 SDK |Microsoft Azure"
    description="瞭解所有的 Java API 及 SDK 包含發行日期、 退休日期和 DocumentDB Java SDK 的每個版本之間所做的變更。"
    services="documentdb"
    documentationCenter="java"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB Api 和 Sdk

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [其餘](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-java-api-and-sdk"></a>DocumentDB Java API 及 SDK

<table>
<tr><td>**SDK 下載**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>
<tr><td>**API 文件**</td><td>[Java API 參照文件](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**參與 sdk 的變更**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**快速入門**</td><td>[Java SDK 快速入門](documentdb-java-application.md)</td></tr>
<tr><td>**目前支援執行階段**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>版本資訊

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)

  - 加入的支援 BoundedStaleness 一致性層級。
  - 加入的支援直接連線的 CRUD 作業分割的集合。
  - 固定查詢的 SQL 資料庫中的錯誤。
  - 在其中工作階段權杖可能設定不正確的工作階段快取修正錯誤。

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)

  - 加入的支援交叉分割平行查詢。
  - 頂端/ORDER BY 集合的查詢分割加入的支援。
  - 加入的支援強式的一致性。
  - 使用直接連線時，新增的支援名稱會以要求。
  - 固定進行 ActivityId 所有邀請重試過保持一致。
  - 固定的工作階段快取時重新建立具有相同名稱的集合相關的錯誤。
  - 新增多邊形和 LineString 資料類型時指定原則地理圍欄空間查詢編製索引的集合。
  - 固定的問題 java 1.8 Java 文件。

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - 快取單一磁碟分割集合，然後不進行額外的擷取分割鍵要求 PartitionKeyDefinitionMap 修正錯誤。
  - 修正不重試提供不正確的磁碟分割索引鍵值時的錯誤。

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - 新增多區域資料庫帳戶支援。
  - 自動重試] 上的選項來自訂最大嘗試與最大重試等候時間流速控制要求加入的支援。  請參閱 RetryOptions 和 ConnectionPolicy.getRetryOptions()。
  - 已遭取代的 IPartitionResolver 依據自訂分割的程式碼。 請使用更高的儲存空間，處理量分割的集合。

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- 新增重試原則支援節流設定。  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- 新增的時間至 live (TTL) 支援文件。

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- 實作的[分割的集合](documentdb-partition-data.md)及[使用者定義的效能等級](documentdb-performance-levels.md)。

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- 以產生由小到大必須與其他 Sdk 雜湊值 HashPartitionResolver 修正錯誤。

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- 新增雜湊與範圍分割解析程式來協助跨多個資料分割 sharding 應用程式。

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- 實作了。 新增至支援了功能的新 upsertXXX 方法。
- 實作 ID 依據路由。 沒有公用 API 的變更，所有變更內部。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
- 以與其他 Sdk 的對齊方式中顯示版本號碼略過的版本

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- 支援休士頓索引
- 驗證之所有資源的 [識別碼] 屬性。 資源識別碼不能包含 ?，/ #，\,字元或結尾空格。
- 新增 ResourceResponse 新增標題 」 索引轉換進度]。

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- 實作 V2 編製索引的原則

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## <a name="release--retirement-dates"></a>發行版本與淘汰網站日期
Microsoft 提供通知的至少開始前事先淘汰 SDK 才能平滑轉換為較新/受支援版本的**12 個月**。

新功能和功能及最佳化只會新增至目前 SDK，因此其會建議您永遠升級為最新 SDK 版本早越好]。

使用已停用的 SDK DocumentDB 任何要求會被拒絕服務。

> [AZURE.WARNING]
Azure DocumentDB SDK java 之前版本**1.0.0**所有版本會在**2016 年 2 月 29 日**上已停都用。

<br/>

| 版本 | 發行日期 | 退休日期
| ---     | ---          | ---
| [1.9.1](#1.9.1) | 2016 年 10 月 28日日 |---
| [1.9.0](#1.9.0) | 2016 年 10 月 03 日 |---
| [1.8.1](#1.8.1) | 2016 年 6 月 30日日 |---
| [1.8.0](#1.8.0) | 2016 年 6 月 14日日 |---
| [1.7.1](#1.7.1) | 2016 年 4 月 30日日 |---
| [1.7.0](#1.7.0) | 2016 年 4 月 27日日 |---
| [1.6.0](#1.6.0) | 2016 年 3 月 29日日 |---
| [1.5.1](#1.5.1) | 2015 年 12 月 31日日 |---
| [1.5.0](#1.5.0) | 2015 年 12 月 4日日 |---
| [1.4.0](#1.4.0) | 2015 年 10 月 05 日 |---
| [1.3.0](#1.3.0) | 2015 年 10 月 05 日 |---
| [1.2.0](#1.2.0) | 2015 年 8 月 05 日 |---
| [1.1.0](#1.1.0) | 2015 年 7 月 09 日 |---
| [1.0.1](#1.0.1) | 2015 年月 12日日 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 07 日 |---
| 0.9.5-prelease | 2015 年 3 月 9日日 | 2016 年 2 月 29日日
| 0.9.4-prelease | 2015 年 2 月 17 | 2016 年 2 月 29日日
| 0.9.3-prelease | 2015 年 1 月 13日日 | 2016 年 2 月 29日日
| 0.9.2-prelease | 2014 年 12 月 19日日 | 2016 年 2 月 29日日
| 0.9.1-prelease | 2014 年 12 月 19日日 | 2016 年 2 月 29日日
| 0.9.0-prelease | 2014 年 12 月 10日日 | 2016 年 2 月 29日日

## <a name="faq"></a>常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>另請參閱

若要進一步瞭解 DocumentDB，請參閱[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)服務] 頁面。
