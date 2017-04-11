<properties
    pageTitle="模型中 Azure 搜尋 Multitenancy |Microsoft Azure |裝載的雲端搜尋服務"
    description="使用 Azure 搜尋時，深入了解 multitenant SaaS 應用程式的一般設計模式。"
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>設計模式 multitenant SaaS 應用程式和 Azure 搜尋

Multitenant 的應用程式是提供他們無法看到或共用的任何其他租用戶資料的租用戶的任何數字的同一個服務和功能。 這份文件討論 Azure 搜尋的 multitenant 應用程式的租用戶隔離策略。

## <a name="azure-search-concepts"></a>Azure 搜尋概念
作為搜尋-為-的-服務解決方案，Azure 搜尋 」 可讓開發人員新增應用程式沒有管理任何基礎結構，或成為搜尋中的專家豐富的搜尋體驗。 資料服務上傳並再儲存在雲端。 使用簡單要求 Azure 搜尋 API，可以再修改與資料搜尋。 可以在[本文](http://aka.ms/whatisazsearch)中找到服務的概觀。 設計模式之前，是瞭解一些概念 Azure 搜尋中。

### <a name="search-services-indexes-fields-and-documents"></a>搜尋服務、 索引、 欄位和文件
使用 Azure 搜尋，其中一個訂閱_搜尋服務_。 當資料上傳至 Azure 搜尋，它會儲存於搜尋服務中的_索引_。 可以有單一服務中的索引的數字。 若要使用之資料庫熟悉的概念，搜尋服務可以 likened 至資料庫時可以 likened 服務中的索引，在資料庫中的資料表。

搜尋服務中的每個索引有自己的結構描述，可所定義之數字的可自訂的_欄位_。 資料會新增至 Azure 搜尋索引中的個別_文件_的表單。 每個文件必須將它上傳至特定的索引，而且必須符合該索引的結構描述。 在搜尋時使用 Azure 搜尋資料，全文檢索搜尋查詢會發出針對特定的索引。  若要比較資料庫的概念，欄位可以 likened 表格中的資料行，文件可以 likened 資料列。

### <a name="scalability"></a>延展性
在標準[價格層](https://azure.microsoft.com/pricing/details/search/)的任何 Azure 搜尋服務可以在兩個維度縮放︰ 儲存空間及可用。
* _磁碟分割區_可以新增至增加之搜尋服務的儲存空間。
* 若要增加可以處理搜尋服務要求的服務可以加入_複本_。

新增和移除磁碟分割區複本，可讓搜尋服務的資料量有變大和流量應用程式要求的容量。 為了讓達成讀取[SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)之搜尋服務，需要兩個複本。 為了讓服務進行讀寫[SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)，需要三個的複本。


### <a name="service-and-index-limits-in-azure-search"></a>Azure 搜尋索引的服務與限制
有一些不同[價格層](https://azure.microsoft.com/pricing/details/search/)Azure 搜尋] 中，每個層級都有不同的[限制和配額](search-limits-quotas-capacity.md)。 這些限制的部分是在服務等級、 部分會在索引層級，而有些則在分割層級。


|                                  | 基本     | Standard1   | 標準版 2   | Standard3   | Standard3 HD  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| 每個服務的最大複本     | 3         | 12          | 12          | 12          | 12            |
| 最大的磁碟分割區每個服務   | 1         | 12          | 12          | 12          | 1             |
| 搜尋最大單位 (複本 * 磁碟分割區) 每個服務 | 3         | 36          | 36          | 36          | 36 （最多 3 分割）            |
| 每個服務的最大文件    | 有 1 百萬個 | 180 百萬 | 720 百萬 | 1.4 億 | 600 百萬   |
| 最大值的儲存空間，每個服務      | 2 GB      | 300 GB      | 1.2 TB      | 2.4 TB      | 600 GB        |
| 每個資料分割的最大文件  | 有 1 百萬個 | 15 百萬  | 60 百萬  | 120 百萬 | 200 百萬   |
| 最大值的儲存空間，每個資料分割    | 2 GB      | 25 GB       | 100 GB      | 200 GB      | 200 GB        |
| 每個服務的最大索引      | 5         | 50          | 200         | 200         | 3000 （最大 1000年索引/磁碟分割）          |


#### <a name="s3-high-density"></a>S3 密度 」
在 Azure 搜尋 S3 價格層沒有高密度 (HD) 模式專為 multitenant 案例設計的選項。 在許多情況下，就必須支援大量的較小的租用戶下單一服務進行簡單和成本效率的優點。

S3 HD 可讓您可以擴充索引使用裝載單一服務中的其他索引的磁碟分割區的功能，您可以在單一搜尋服務的管理封裝許多小索引。

具體，S3 服務可能會有 1 和 200 可能主控最 1.4 億文件放在一起的索引。 S3 HD 另一方面會允許個別的索引，只到最多有 1 百萬個文件，但它可以處理每個資料分割 （進位至每個服務 3000) 的每個資料分割 200 百萬的文件總計數超過 1000 個索引 （最多每個服務的 600 百萬）。



## <a name="considerations-for-multitenant-applications"></a>Multitenant 應用程式的考量
Multitenant 應用程式有效必須同時保留某些層級的各種不同的租用戶之間的隱私權散發間的租用戶的資源。 設計架構，這樣的應用程式時，有一些考量︰

* _租用戶隔離︰_應用程式開發人員需要採取適當的量值，以確保沒有租用戶有未經授權，或不想要的其他租用戶資料的存取權。 除了資料隱私權的觀點來看，租用戶隔離策略需要有效地管理共用的資源及保護從雜訊其他例項。
* _雲端資源成本︰_使用任何其他應用程式，如軟體解決方案必須保持競爭力元件 multitenant 應用程式的成本。
* _輕鬆作業︰_開發的 multitenant 架構，對應用程式的作業和複雜度的影響時，很重要的考量。 Azure 搜尋有[99.9 %sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。
* _所需的全域管理員︰_有效率地提供分佈球體的租用戶，可能需要 multitenant 應用程式。
* _延展性︰_應用程式開發人員必須考量維護充分低層級的應用程式的複雜度和設計要不按比例縮放的租用戶的數字與大小的租用戶的資料與工作負載的應用程式之間如何重新同步化。

Azure 搜尋提供幾個可用於隔離租用戶的資料與工作負載的邊界。

## <a name="modeling-multitenancy-with-azure-search"></a>模型使用 Azure 搜尋 multitenancy
Multitenant 案例中，如果應用程式開發人員使用一或多個搜尋服務，並將其服務、 索引或兩者之間的租用戶。 Azure 搜尋建立模型 multitenant 情況時，有幾個常見的模式︰

1. _每一租用戶的索引︰_每個租用戶的搜尋服務中共用的其他租用戶中自己索引。
1. _服務每一租用戶︰_每個租用戶有自己專屬的 Azure 搜尋服務，提供高的資料與工作負載的分隔。
1. _混合兩者︰_大的更多作用中的租用戶時較小的租用戶指派服務應用程式中的個別索引指派專用的服務。

## <a name="1-index-per-tenant"></a>1.索引每一租用戶
![每個租用戶的索引模型的 portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

每個租用戶的索引模型中，多個租用戶佔用單一 Azure 搜尋服務每個租用戶有自己的索引的位置。

租用戶達到獨立資料，因為所有的搜尋要求及文件作業發出 Azure 搜尋索引層級。 應用程式層級]，則需要線上狀態，同時也在所有的租用戶管理服務層級的資源將適當的索引的各種不同的租用戶流量。

每個租用戶的索引模型索引鍵屬性是應用程式開發人員 oversubscribe 搜尋服務應用程式的租用戶之間的容量的能力。 如果租用戶的工作量不平均分配的租用戶的最佳組合被由搜尋服務的索引，以容納時同時處理較少的作用中的租用戶冗長的高度作用中的資源的租用戶的數字。 取捨的是模型無法處理的情況每個租用戶同時高度作用中。

每個租用戶的索引模型提供是變動成本模型，整個 Azure 搜尋服務購買預付的基礎後續填用租用戶。 這可讓您指定的試用版和免費的帳戶未使用的容量。

使用全域所需的應用程式，每個租用戶的索引模型可能不是最有效。 如果應用程式的租用戶分佈地球，則可能需要可能會在每個重複成本的每個區域的不同的服務。

Azure 搜尋可讓個別的索引和索引總數的刻度變大。 如果適當的價格層選定磁碟分割區和複本可整個搜尋服務時新增服務中的個別的索引，會儲存或流量太大。

如果索引總數成長的單一服務太大，另一個服務必須佈建以容納新的租用戶。 如果索引有要加入新服務時搜尋服務之間移動，請從索引的資料有手動複製一個索引到其他為 Azure 搜尋並不允許要移動的索引。


## <a name="2-service-per-tenant"></a>2。 每個租用戶的服務
![每個租用戶的服務模型的 portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

在每個租用戶的服務架構中，每個租用戶會有自己的搜尋服務。

此模型的應用程式已達到最大適用於其承租人的隔離的等級。 每個服務具有專屬儲存及處理搜尋要求，以及另一個 API 金鑰處理量。

位置每個租用戶所需的大型或工作量小變化租用戶承租人的應用程式，每個租用戶的服務模型會是透過各種租用戶負載不會共用資源時有效的選項。

每個租用戶模型服務也會提供可預測的固定成本模型的優點。 沒有可觀整個搜尋服務中的，直到有租用戶填滿，然而每個承租人成本大於索引每個租用戶模型。

每個租用戶的服務模型是有效的選擇有全域所需的應用程式。 使用散布租用戶，您可以輕鬆有適當的區域中的每個租用戶的服務。

個別的租用戶 outgrow 其服務時，就會發生這個模式按比例縮放的挑戰。 Azure 搜尋目前不支援升級的搜尋服務的價格層，讓所有的資料必須手動複製到新的服務。

## <a name="3-mixing-both-models"></a>3.混合兩種模型
建立模型 multitenancy 另一種模式混合索引每個租用戶和服務每個租用戶的策略。

混合兩種模式，而不是作用中的較小的租用戶的冗長可佔用索引中的共用服務應用程式的最大的租用戶時，可佔用專用的服務。 此模型確保最大的租用戶有一致高效服務時協助保護較小的租用戶，從任何雜訊其他例項。

不過，實作此策略依賴 foresight 預測的租用戶都需要專用的服務與共用服務中的索引。 應用程式的複雜度增加的需求管理這兩個這些 multitenancy 模型。

## <a name="achieving-even-finer-granularity"></a>結案更進一步的資料粒度
上述的設計模式，來建立模型中 Azure 搜尋 multitenant 案例假設統一範圍的每個租用戶為整個應用程式的執行個體的位置。 不過，應用程式有時可以處理許多較小的範圍。

如果每個租用戶的服務與索引每個租用戶模型不是細微的完全小型範圍，可能是細微的建立索引以獲得更進一步度的模型。

若要讓單一索引會有不同行為的不同的用戶端端點，功能變數可以新增至索引的每個可能的用戶端指定為特定的值。 用戶端呼叫 Azure 搜尋查詢，或修改索引，每次從用戶端應用程式的程式碼指定在查詢時使用 Azure 搜尋[篩選](https://msdn.microsoft.com/library/azure/dn798921.aspx)功能該欄位的適當的值。

這個方法可以用來達成的另一個使用者帳戶，不同的權限等級的功能，並完全將應用程式。

## <a name="next-steps"></a>後續步驟
Azure 搜尋是許多應用程式中[進一步瞭解服務的強大功能](http://aka.ms/whatisazsearch)令人讚嘆的選擇。 當評估各種不同的設計模式 multitenant 應用程式，請考慮[各價格層](https://azure.microsoft.com/pricing/details/search/)與個別[服務限制](search-limits-quotas-capacity.md)，以最佳量身訂做 Azure 搜尋，以符合應用程式工作負載與各種架構。

Azure 搜尋與 multitenant 案例相關的任何問題可以會導向至azuresearch_contact@microsoft.com。
