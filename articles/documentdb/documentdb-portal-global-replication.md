<properties
    pageTitle="DocumentDB 全域資料庫複寫 |Microsoft Azure"
    description="瞭解如何管理您的 DocumentDB 帳戶透過 Azure 入口網站的全域複寫。"
    services="documentdb"
    keywords="全域資料庫、 複寫"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>如何執行 DocumentDB 全域資料庫複寫使用 Azure 入口網站

瞭解如何使用 Azure 入口網站複製全域可用性 Azure DocumentDB 中資料的多個區域中的資料。

有關如何全域資料庫複寫 DocumentDB 功能的運作，請參閱[全面使用 DocumentDB 等距分佈資料](documentdb-distribute-data-globally.md)。 以程式設計方式執行全域資料庫複寫的相關資訊，請參閱[開發多區域 DocumentDB 帳戶](documentdb-developing-with-multiple-regions.md)。

> [AZURE.NOTE] 全域通訊群組的 DocumentDB 資料庫是推出並自動啟用的任何新建立的 DocumentDB 帳戶。 我們正在啟用全域通訊上所有的現有帳戶，但過渡，如果您想要全域通訊啟用您的帳戶，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，我們將會啟用您現在。

## <a id="addregion"></a>新增全域資料庫的區域

DocumentDB 適用於大部分[Azure 區域] [azureregions]。 選取後您資料庫的帳戶的預設一致性層級，您可以建立一或多個區域關聯 （根據您所選擇的預設一致性層級和全域通訊的需求）。

1. 在[Azure 入口網站](https://portal.azure.com/)，在 Jumpbar 中，按一下 [ **DocumentDB 帳戶**]。
2. 在**DocumentDB 帳戶**刀中，選取要修改的資料庫帳戶。
3. 在帳戶刀中，按一下 [**全域複製資料**功能表。
4. **全域複製資料**刀，選取 [新增或移除的區域，然後按一下**儲存**。 若要加入區域的成本，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/documentdb/)或[散發資料全域 DocumentDB](documentdb-distribute-data-globally.md)文章，如需詳細資訊。

    ![按一下區域] 中新增或移除地圖][1]

### <a name="selecting-global-database-regions"></a>選取通用資料庫區域

在設定兩個或多個區域時，建議區域根據所述的地區組選取[業務連續性與損毀復原 (BCDR): Azure 配對區域] [bcdr]文章。

明確地說時設定多個區域，請確定，從每個成對的區域中選取數相同的區域 （+ / 奇數/偶數 1)。 例如，如果您想要部署至此四個美國地區，您選取兩個美國地區從 [左] 欄，並從右邊兩個。 因此，下列是適當的一組︰ 西美國、 東亞美國、 北美美國中部及南部美國中部。

本指南務必遵循當只有兩個區域的設定為損毀修復狀況。 兩個以上的區域，下列本指南最好，但不是重要，只要選取區域部分依循此配對。

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>後續步驟

瞭解如何管理您的全域複寫帳戶的一致性閱讀[DocumentDB 中的一致性層級](documentdb-consistency-levels.md)。

有關如何全域資料庫複寫 DocumentDB 功能的運作，請參閱[全面使用 DocumentDB 等距分佈資料](documentdb-distribute-data-globally.md)。 以程式設計方式複製多個區域中的資料的相關資訊，請參閱[開發多區域 DocumentDB 帳戶](documentdb-developing-with-multiple-regions.md)。

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
