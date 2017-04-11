<properties 
    pageTitle="如何實作與 Sdk 的用戶端側分割 |Microsoft Azure" 
    description="瞭解如何在多個集合中使用 Azure DocumentDB Sdk （晶怪） 的磁碟分割的資料與傳送要求" 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="how-to-partition-data-using-client-side-support-in-documentdb"></a>如何分割 DocumentDB 中使用用戶端支援的資料

Azure DocumentDB 支援[自動分割的集合](documentdb-partition-data.md)。 然而，有很有幫助沒問題了粗略的分割行為控制的使用情況。 若要降低分割任務所需的樣板程式碼，我們已新增功能.NET、 Node.js，和 Java Sdk 所建立的應用程式，在多個集合橫向更容易。

本文中，我們會查看的類別和.NET SDK，以及如何使用這些開發分割的應用程式中的介面。 Java、 Node.js Python 等其他 Sdk 支援的用戶端分割的類似的方法與介面。

## <a name="client-side-partitioning-with-the-documentdb-sdk"></a>用戶端分割搭配 DocumentDB SDK

我們深入分割之前，請讓我們複習分割相關的一些基本 DocumentDB 概念。 Azure DocumentDB 資料庫的每一個帳戶所組成的一組資料庫，每個包含多個集合] 中，每一種可包含預存程序、 引動程序、 Udf、 文件和相關的附件。 集合可以是單一資料分割或分割本身和有下列屬性︰

- 集合提供效能隔離。 因此是在自動分頁類似相同集合中的文件的效能提升。 例如時間數列資料，您可能要將資料放在過去一個月的經常查詢時，與較高的能夠處理量集合內而較舊的資料放在與低能夠處理量的集合。
- ACID 交易即預存程序與觸發程序無法橫跨集合。 交易範圍內的單一資料分割索引鍵值集合內。
- 集合不會強制執行結構描述，讓他們可以用於 JSON 的類型相同或不同類型的文件。

開始使用版本[1.5.x 的 Azure DocumentDB Sdk](documentdb-sdk-dotnet.md)，您可以執行直接對資料庫的文件作業。 在內部[DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx)會使用您指定的 PartitionResolver 至適當的集合傳送要求資料庫。

>[AZURE.NOTE] [伺服器端分割](documentdb-partition-data.md)REST API 2015 12 16 和 Sdk 1.6.0+ 中推出 deprecates 簡單的使用案例的用戶端的磁碟分割解決方法。 用戶端分割不過更有彈性，可讓您控制效能隔離在磁碟分割金鑰、 控制平行度從多個資料分割，讀取結果時，以及使用範圍/空間分割與雜湊的方法。

例如，在.NET，每個 PartitionResolver 類別是[IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)介面有三種方法- [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx)、 [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx)和[ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx)的具體實作。 LINQ 查詢和 ReadFeed iterator 使用 ResolveForRead 方法內部，逐一查看所有符合要求的磁碟分割索引鍵的集合。 同樣地，建立作業使用 ResolveForCreate 方法來路由傳送會建立右側的磁碟分割。 沒有取代所需的變更、 刪除及讀取因為他們使用文件，其中已包含對應的集合的參照。

Sdk 也包含兩個標準分割的技術、 雜湊和範圍查閱，透過[HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)與[RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)支援的兩種類別。 您可以使用這些類別以輕鬆地將分割邏輯新增至您的應用程式。  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>新增分割邏輯和註冊 PartitionResolver 

以下是顯示如何建立[HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)並登錄為資料庫 DocumentClient 程式碼片段。

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>建立分割區中的文件  

一旦註冊 PartitionResolver 之後，您可以執行會建立並直接對資料庫查詢，如下所示。 在此範例中，SDK 使用 PartitionResolver 擷取的使用者識別碼與雜湊，然後使用此值將建立的操作傳送到正確的集合。

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>針對磁碟分割區建立查詢  

您可以查詢傳入資料庫及資料分割索引鍵使用[CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)方法。 查詢會傳回單一結果集在所有的集合資料庫中的對應至分割索引鍵。  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>在資料庫中建立針對所有集合的查詢 

您也可以查詢在資料庫中所有的集合，然後列舉結果，略過的磁碟分割索引鍵的引數的如下所示。

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>雜湊分割解析程式
使用雜湊分割，磁碟分割區會被指派根據雜湊功能，可讓您平均分散邀請和資料的磁碟分割之數字的值。 此方法通常是用來分割資料產生，或使用大量不同的用戶端，與適合用來儲存使用者設定檔、 目錄項目，以及 IoT （「 網際網路的物品 」） 遙測資料。 集合內 DocumentDB 的伺服器端分割支援也會使用雜湊資料分割。

**雜湊 Partitioning:**
![說明如何雜湊資料分割平均分散要求磁碟分割區的圖表](media/documentdb-sharding/partition-hash.png)

分割*N*集合配置的簡單雜湊就會採取任何文件，計算*hash(d) mod N*決定具有放在哪個集合。 但是這個簡單的方法有問題，無法運作時新增新的集合，或移除集合，因為這需要執行幾乎所有資料以取得 reshuffled。 [一致雜湊](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) 是已知演算法的解決此問題實作雜湊配置的最小化的 [新增或移除集合時所需移動的資料量。

[HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)類別實作建立一致的雜湊撥打雜湊函數[IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx)介面中指定的邏輯。 根據預設，HashPartitionResolver 使用 MD5 雜湊函數，但您可以交換時使用您自己的雜湊實作。 HashPartitionResolver 內部會建立 16 雜湊或雜湊撥打給每個集合中的 「 虛擬節點] 以獲得更統一的通訊群組的文件集合，但您可以視情況而定的用戶端側計算量資料偏態犧牲這個數字。

**使用 HashPartitionResolver 一致雜湊︰**
![說明如何 HashPartitionResolver 建立雜湊撥打的圖表](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>範圍的磁碟分割解析程式

在範圍磁碟分割，磁碟分割區會根據分割索引鍵為某些範圍內所指派的。 這常用的分割的時間戳記內容 (例如 2015 年 4 月 1 日與 2015 年 4 月 14 日之間的 eventTime)。 [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)類別可協助您維護範圍之間的對應\<T\>及自動連結的集合。 

[範圍\<T\>](https://msdn.microsoft.com/library/azure/mt126048.aspx)是一個簡單的類別，管理範圍中的任何類型的實作 IComparable\<T\>和 IEquatable\<T\>等字串或數字。 為讀取並建立，您可以在任何任意的範圍，傳遞和解析程式識別所有候選集合藉由識別相交的磁碟分割的範圍，以要求的範圍。 執行時間系列資料的範圍查詢時，這項功能很有用。

**分割的範圍︰**  

![ 說明如何範圍分割平均分配的圖表要求跨磁碟分割區](media/documentdb-sharding/partition-range.png)  

特殊範圍分割是範圍只單一不連續的值時，有時也稱為 「 查閱分割 」。 這常用分割區域 （例如斯堪的磁碟分割包含挪威、 丹麥及瑞典） 或分割為多租用戶應用程式的租用戶。

## <a name="samples"></a>範例 

看看[DocumentDB 分割範例 Github 專案](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)含有如何使用這些 PartitionResolvers 與擴充的程式碼片段實作您自己的解析程式，以符合特定的使用情況下，如下所示︰ 

* 如何指定 GetPartitionKey 任意 lambda 運算式，並將其實作分割的複合索引鍵，或是以不同方式分割不同類型的物件。
* 如何建立簡單[LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs)執行分割使用手動查閱表格。 這個模式通常用於分割根據不連續的值，例如地區、 租用戶 ID 或應用程式名稱。
* 如何建立[ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs)建立自動定義命名的配置、 IndexingPolicy 和預存程序必須針對新的集合登錄範本為基礎的集合。
* 若要建立只會建立新的集合，如舊集合填滿配置較[SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs)的方式。
* 如何序列化和還原您 PartitionResolver 狀態序列化 json，讓您可以共用程序，關機。 您可以保留這些設定檔，或甚至 DocumentDB 集合。
* 一致雜湊根據動態新增及移除分割資料庫的磁碟分割區的[DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs)類別。 內部使用[TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs)來傳送讀取和寫入期間使用其中一個四種模式-讀取舊分割配置 (ReadCurrent)，新的移轉 (ReadNext)，合併結果來自兩個 (ReadBoth) 或 （無） 移轉時無法使用。

範例是開啟的來源，我們建議您提交提取要求與可以利用其他 DocumentDB 開發人員的比重。 請參閱如何提供的指導方針的[比重指導方針](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)。  

>[AZURE.NOTE] 集合建立是工資率-受限於 DocumentDB，因此此處所示的範例方法可能需要幾分鐘，才能完成。

##<a name="faq"></a>常見問題集
**不支援伺服器端分割 DocumentDB 嗎？**

是的 DocumentDB 支援[伺服器端分割](documentdb-partition-data.md)。 DocumentDB 也支援用戶端分割透過更進階的使用案例的用戶端的磁碟分割解析程式。

**何時應使用伺服器端和用戶端分割？**
大多數的使用情況下，建議使用伺服器端分割，因為它會處理分割資料和路由要求的系統管理工作。 不過，如果您需要範圍分割或有特殊的使用案例的效能隔離的磁碟分割索引鍵的不同值之間，然後用戶端分割可能是最好的方法。

**如何新增或移除我分割配置集合？**

查看在範例專案中實作 DocumentClientHashPartitioningManager 如需如何實作，索引器的範例。

**如何固定或與其他用戶端共用我的資料分割設定？**

您可以序列化 json partitioner 狀態和儲存設定檔中，或甚至 DocumentDB 集合。 查看 RunSerializeDeserializeSample 方法範例專案的範例。

**如何鍊分割的各種技術？**

您可以藉由實作在內部使用一或多個現有的解析程式自己 IPartitionResolver 鏈結 PartitionResolvers。 看看 TransitionHashPartitionResolver 範例專案的範例。

##<a name="references"></a>參照
* [伺服器端 DocumentDB 中分割](documentdb-partition-data.md)
* [DocumentDB 集合和效能層級](documentdb-performance-levels.md)
* [分割 Github 上的程式碼範例](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [Msdn DocumentDB.NET SDK 文件](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB.NET 範例](https://github.com/Azure/azure-documentdb-net)
* [DocumentDB 限制](documentdb-limits.md)
* [DocumentDB 部落格對於效能的秘訣](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 
