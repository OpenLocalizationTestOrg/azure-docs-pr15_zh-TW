<properties
   pageTitle="散發資料全域 DocumentDB |Microsoft Azure"
   description="瞭解使用全域資料庫從 Azure DocumentDB，完全受管理的 NoSQL 資料庫服務地球比例地理複寫、 容錯移轉，以及資料復原。"
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>發佈 DocumentDB 全域的資料

> [AZURE.NOTE] 全域通訊群組的 DocumentDB 資料庫是推出並自動啟用的任何新建立的 DocumentDB 帳戶。 我們正在啟用全域散佈所有現有帳戶，但過渡，如果您想要在您的帳戶啟用的全域通訊請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，我們會將它啟用您現在。

Azure DocumentDB 是設計用來滿足 IoT 組成數百萬全域分散式的裝置與傳遞給使用者的快速回應的體驗，世界各地的網際網路比例應用程式的應用程式的需求。 這些資料庫系統面對達成低延遲 access 應用程式的資料，從多個與明確定義的資料一致性和可用性保證的地理區域的挑戰。 以全域分散式的資料庫系統，DocumentDB 簡化全域的通訊群組的資料提供完整的管理、 多區域資料庫帳戶提供之間的一致性、 可用性和效能，所有對應的保證與清除必須做的取捨。 DocumentDB 資料庫帳戶會提供高可用性、 單一數字 ms 延遲、 多個[定義完善的一致性層級] [consistency]，透明區域的容錯移轉與多隸屬 Api，還可將 elastically 縮放地球上的 [處理量，儲存空間。 

  
## <a name="configuring-multi-region-accounts"></a>設定多區域帳戶

一分鐘之內透過[Azure 入口網站](documentdb-portal-global-replication.md)中可以完成設定 DocumentDB 帳戶全球的縮放比例。 您只需要是選取多個的支援明確定義的一致性層級之間的右邊的一致性層級，與您的資料庫帳戶建立關聯的 Azure 區域的任何數字。 DocumentDB 一致性層級提供清除取捨特定的一致性保證與效能。 

![DocumentDB 提供多個，以及定義選擇的一致性 （寬鬆） 模型][1]

DocumentDB 提供多個，以及定義選擇 （寬鬆） 的一致性模型。

選取正確的一致性層級取決於資料一致性保證應用程式的需求。 DocumentDB 自動在所有的指定區域就會複製資料，並可確保您已選取您的資料庫帳戶的一致性。 


## <a name="using-multi-region-failover"></a>使用多區域容錯移轉 

Azure DocumentDB 可無障礙容錯移轉資料庫帳戶跨多個 Azure 區域 – 新的[多重隸屬 Api] [developingwithmultipleregions]保證的應用程式可以繼續使用邏輯結束點，而是不受干擾的容錯移轉。 容錯移轉由您來控制，提供隸屬資料庫的彈性帳戶事件中任何一種情況可能失敗的範圍發生，包括應用程式、 基礎結構、 服務或地區失敗 （實數或模擬）。 失敗 DocumentDB 地區，服務會直接失敗在您的資料庫帳戶並應用程式持續存取資料，而不遺失的可用性。 雖然 DocumentDB 提供[99.99%可用性 Sla][sla]，您可以藉由兩個，[以程式設計方式]模擬地區失敗測試您的應用程式 [結束] 結束可用性屬性[arm]也透過 Azure 入口網站。


## <a name="scaling-across-the-planet"></a>跨地球縮放比例
DocumentDB 可讓您獨立佈建處理量，與在您的資料庫帳戶相關聯的所有區域全面使用任何縮放]，在每個 DocumentDB 集合的儲存空間。 自動全域分散式並管理您的資料庫帳戶相關聯的區域的所有 DocumentDB 集合。 您的資料庫帳戶內的集合可以分佈於目前的任何 Azure 區域中的[DocumentDB 服務有][serviceregions]。 

處理量購買並使用每個 DocumentDB 集合的儲存空間已自動佈建在所有區域平均。 這個選項可讓您的應用程式，若要順利進行縮放過[支付僅適用於處理量，您使用的每個小時內的儲存空間]地球[pricing]。 例如，如果您有佈建 2 百萬 RUs DocumentDB 集合，然後每個資料庫帳戶相關聯的區域取得 2 百萬 RUs 該集合。 此如下所示。

![DocumentDB 集合跨四個區域的縮放比例處理量][2]

DocumentDB 保證 < 10 ms 讀取及 < 15 ms 撰寫在 P99 延遲。 讀取的要求不可跨越資料中心邊界，以確保[您已選取的一致性需求][consistency]。 寫入一律是前他們通知用戶端本機認可仲裁。 每個資料庫帳戶是以寫入地區優先順序設定。 指定的最高優先順序的地區做為目前寫入範圍的帳戶。 所有 Sdk 會直接將資料庫帳戶寫入都路由至目前的寫入範圍。 

最後，由於 DocumentDB 完全[結構描述無關][ vldb] -您永遠不必擔心管理/更新的結構描述或次要索引跨多個資料中心。 您的[SQL 查詢][sqlqueries]繼續工作時繼續發展您應用程式和資料模型。 


## <a name="enabling-global-distribution"></a>啟用全域通訊群組 

您可以決定要讓您在本機或全域分散式有關聯的資料與 DocumentDB 的一或多個 Azure 區域資料庫帳戶。 您可以新增或移除您的資料庫帳戶的區域，隨時。 若要使用入口網站中啟用全域通訊群組，請參閱[如何執行 DocumentDB 全域資料庫複寫使用 Azure 入口網站](documentdb-portal-global-replication.md)。 若要以程式設計方式啟用全域通訊群組，請參閱[開發多區域 DocumentDB 帳戶](documentdb-developing-with-multiple-regions.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解散發資料全域 DocumentDB 下列文章中︰

* [佈建處理量，集合的儲存空間] [throughputandstorage]
* [多重隸屬透過 REST Api。.NET、 Java、 Python 和節點 Sdk] [developingwithmultipleregions]
* [DocumentDB 中的一致性層級] [consistency]
* [可用性 Sla] [sla]
* [管理資料庫的帳戶] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

