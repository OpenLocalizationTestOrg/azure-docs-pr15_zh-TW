<properties
    pageTitle="DocumentDB 儲存和效能 |Microsoft Azure" 
    description="深入了解資料儲存區和文件儲存在 DocumentDB 及如何調整 DocumentDB 以符合您的應用程式的需要。" 
    keywords="文件儲存空間"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>進一步瞭解儲存和如預期呈現效能佈建 DocumentDB 中
Azure DocumentDB 是完整的管理、 可調整文件導向 NoSQL 資料庫服務 JSON 文件。 使用 DocumentDB，您不必出租虛擬機器、 部署軟體或監控資料庫。 DocumentDB 營運，而進行全球課程可用性、 效能及資料保護 Microsoft 工程師不斷地監視。  

您可以藉由[建立資料庫帳戶](documentdb-create-account.md)DocumentDB 和入門[DocumentDB 資料庫](documentdb-create-database.md)透過[Azure 入口網站](https://portal.azure.com/)。 DocumentDB 資料庫會提供在單位 solid-state 磁碟機 (SSD) 備份儲存及處理量。 在您的資料庫帳戶，可以在任何時間，以符合您的應用程式的需求調整向上或向下的保留處理量與每個集合中的 [[建立資料庫集合](documentdb-create-collection.md)佈建這些儲存的單位。 

如果您的應用程式超過一或多個集合您保留處理能力，邀請僅限於每個集合為基礎。 這表示的部分應用程式要求可能失敗時可能會降低，其他人。

本文提供資源和指標可用來管理容量和計劃資料的儲存空間的概觀。 

## <a name="database-account"></a>資料庫帳戶
Azure 的訂閱者，您可以提供管理您的資料庫資源的一或多個 DocumentDB 資料庫帳戶。 每個訂閱是單一 Azure 訂閱相關聯。 

透過[Azure 入口網站](documentdb-create-account.md)，或使用[ARM 範本或 Azure CLI](documentdb-automation-resource-manager-cli.md)，就可以建立 DocumentDB 帳戶。

## <a name="databases"></a>資料庫
單一 DocumentDB 資料庫幾乎可以包含量不受限制的文件組成集合的儲存空間。 集合提供效能隔離-可以佈建與處理量的不與其他集合共用相同的資料庫或帳戶中的每個集合。 彈性的大小，範圍從 Gb 到 TBs 的 SSD，基礎的文件儲存和能夠處理量 DocumentDB 資料庫。 與傳統 RDBMS 資料庫，不同資料庫中 DocumentDB 不限定為一部電腦，並可以橫跨多個機器或叢集。  

使用 DocumentDB，視需要調整您的應用程式，您可以建立更多的集合、 資料庫或兩者。 透過[Azure 入口網站](documentdb-create-database.md)或任何一項[DocumentDB Sdk](documentdb-dotnet-samples.md)，您可以建立資料庫。   

## <a name="database-collections"></a>資料庫集合
每個 DocumentDB 資料庫可以包含一個或多個集合。 集合做為好的資料磁碟分割區的文件儲存及處理。 每個集合可以儲存異質性結構描述與文件。 DocumentDB 的自動編製索引和查詢功能可讓您輕鬆地篩選並擷取文件。 集合提供之文件儲存和查詢執行的範圍。 集合也是交易網域其包含的所有文件。 集合配置處理量 Azure 入口網站中，或透過 Sdk 設定的值。 

將一或多個實體伺服器 DocumentDB 來自動分割集合。 當您建立的集合時，您可以指定要求單位的磁碟分割屬性及每能夠處理量。 DocumentDB 使用此屬性的值，發佈磁碟分割區與傳送邀請，例如查詢之間的文件。 分割索引鍵值也做為交易的預存程序及界限引動程序。 每個集合有特定的集合，未共用的同一個帳戶中的其他集合處理量保留的量。 因此，您可以調整查看您的應用程式從儲存及處理量。 

透過[Azure 入口網站](documentdb-create-collection.md)或任何一項[DocumentDB Sdk](documentdb-sdk-dotnet.md)，您可以建立集合。   
 
## <a name="request-units-and-database-operations"></a>要求單位] 及 [資料庫作業

當您建立的集合時，您會保留處理[要求單位 (RU)](documentdb-request-units.md)秒的能力。 請改為思考和管理硬體資源，您可以想像**要求單位**的單一資源的量值的所需執行各種不同的資料庫作業與服務應用程式要求。 1 KB 文件的讀取使用相同的 1 RU 無論儲存的集合或同時的要求，同時執行數項目數。 針對 DocumentDB，包括複雜的作業等 SQL 查詢有可以在開發時間決定如預期呈現 RU 值的所有要求。 如果您知道您的文件的大小，以及每個作業 （讀取、 寫入和查詢） 至您的應用程式支援的頻率，您可以佈建確實所需的處理量，以符合您的應用程式的需求，並不按比例縮放、 下移資料庫，您的效能需求的改變。 

每個集合可以保留與最數百萬秒的要求單位百秒的 100 要求單位的區塊處理量。 在整個週期配合變更處理需求和存取您的應用程式的圖樣集合調整能夠處理量。 如需詳細資訊，請參閱[DocumentDB 效能層級](documentdb-performance-levels.md)。 

>[AZURE.IMPORTANT] 集合是計費的實體。 成本會決定能夠處理量，以要求單位，以及使用儲存在 gb 每秒的集合。 

插入、 刪除、 查詢或預存程序執行等特定作業會耗用要求單位數量？ 要求單位是要求的標準化處理成本。 1 KB 文件的讀取為 1 RU，但要求來插入、 取代或刪除同一份文件所要使用的更多處理從服務，藉此邀請單位。 每個服務的回應包含自訂標頭 (`x-ms-request-charge`) 的報表要求單位耗用的要求。 這個標題是也可透過[Sdk](documentdb-sdk-dotnet.md)存取。 .NET sdk，您可以在[RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge)會是[ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx)物件的屬性。 如果您想要評估您處理量的需求單一打電話之前，您可以使用[容量規劃](documentdb-request-units.md#estimating-throughput-needs)，以協助進行此估計。 

>[AZURE.NOTE] 比較基準 1 要求單位 1 KB 文件的會對應到[工作階段](documentdb-consistency-levels.md)一致性簡單取得文件。 

有數種影響要求單位耗用的 DocumentDB 資料庫帳戶的作業的因素。 這些因素包括︰

- 文件大小。 文件大小會增加讀取或寫入也會增加資料使用的單位。
- 屬性計數。 假設預設編製索引的所有內容，若要撰寫文件中使用的單位會增加屬性的計數增加。
- 資料的一致性。 時使用資料的強式或限制過時的一致性層級，將會用其他單位，閱讀文件。
- 索引的屬性。 每個集合上的索引原則會決定哪些內容已預設編製索引。 您可以藉由限制索引屬性縮減您要求單位消耗。 
- 編製索引的文件。 根據預設，每份文件會自動建立索引，您會使用較少的要求單位，如果您選擇不編製索引的文件部分。

如需詳細資訊，請參閱[DocumentDB 要求單位](documentdb-request-units.md)。 

例如，以下是顯示多少佈建要求單位，以三個不同的文件大小 （1 篇知識庫文章、 4 KB 及 64 KB） 和兩個不同的效能層級的表格 （500 讀取第二 + 100 寫入第二和 500 讀取第二 + 500 寫入第二）。 在工作階段，設定資料的一致性，編製索引的原則設為 [無]。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>文件大小</strong></p></td>
            <td valign="top"><p><strong>讀取/第二個</strong></p></td>
            <td valign="top"><p><strong>寫入每秒</strong></p></td>
            <td valign="top"><p><strong>要求單位</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 篇知識庫文章</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1000 的 RU s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 篇知識庫文章</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

查詢與預存程序引動程序取用要求單位根據所執行的作業的複雜性。 當您開發應用程式，檢查 [若要進一步瞭解如何每一項作業分成要求單位容量要求收費標頭]。  


## <a name="choice-of-consistency-level-and-throughput"></a>選擇的一致性層級和處理量
預設的一致性層級的選項有處理量及延遲的影響。 以程式設計方式及透過 Azure 入口網站，您可以設定預設的一致性層級。 您也可以覆寫每個要求為基礎的一致性層級。 根據預設，會一致性層級設定為**工作階段**，提供 monotonic 讀/寫，並讀取您寫的保證。 工作階段的一致性相當適合用於使用者中心的應用程式，並提供理想的餘額的一致性和效能折衷方案。    

變更您的一致性層級 Azure 入口網站上的指示，請參閱[如何管理 DocumentDB 帳戶](documentdb-manage-account.md#consistency)。 或者，如需有關一致性層級的詳細資訊，請參閱[使用一致性層級](documentdb-consistency-levels.md)。

## <a name="provisioned-document-storage-and-index-overhead"></a>提供的文件儲存和索引負荷
DocumentDB 支援建立的單一資料分割和分割的集合。 在 DocumentDB 中的每個資料分割支援 10 GB 的備份 SSD 儲存空間。 10 GB 的文件儲存包含文件以及索引的儲存空間。 根據預設，DocumentDB 集合會設為自動將所有文件索引不明確需要任何次要索引或結構描述。 一般索引開銷根據使用 DocumentDB 應用程式，介於 2 20%。 DocumentDB 所使用的編製索引技術可確保的屬性的值，不論索引開銷不超過大小的預設設定文件的 80%以上。 

預設的所有文件的索引是由 DocumentDB 自動。 不過，如果您想要微調索引開銷，您可以選擇從[DocumentDB 編製索引原則](documentdb-indexing-policies.md)所述插入或取代文件，在索引中移除特定文件。 您可以設定 DocumentDB 集合排除集合內的所有文件索引。 您也可以設定 DocumentDB 集合選擇性地索引某些內容] 或 [使用萬用字元 JSON 文件，路徑中[設定的集合編製索引的原則](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection)所述。 不包括內容] 或 [文件時，也可以改善寫入處理 – 這表示您所要使用的較少的要求單位的能力。   

## <a name="next-steps"></a>後續步驟

若要繼續學習如何 DocumentDB 的運作方式，請參閱[Partitioning 和 Azure DocumentDB 中的縮放比例](documentdb-partition-data.md)。

監控效能層級 Azure 入口網站上的指示，請參閱[監視器 DocumentDB 帳戶](documentdb-monitor-accounts.md)。 如需有關如何選擇集合的效能層級的詳細資訊，請參閱[DocumentDB 中的效能層級](documentdb-performance-levels.md)。
 
