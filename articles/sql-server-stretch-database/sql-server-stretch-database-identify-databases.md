<properties
    pageTitle="識別資料庫及表格伸展資料庫執行伸展資料庫顧問 |Microsoft Azure"
    description="瞭解如何識別資料庫及伸展資料庫的對象的資料表。"
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>藉由執行伸展資料庫顧問伸展資料庫識別資料庫及表格

若要識別資料庫及伸展資料庫的對象的表格，請下載 SQL Server 2016 升級警告，並執行伸展資料庫顧問。 延伸資料庫顧問也會識別封鎖的問題。

## <a name="download-and-install-upgrade-advisor"></a>下載並安裝升級指導
下載並安裝升級顧問從[這裡](http://go.microsoft.com/fwlink/?LinkID=613421)。 這項工具不會包含在 SQL Server 安裝媒體。

## <a name="run-the-stretch-database-advisor"></a>執行伸展資料庫顧問

1.  執行升級顧問。

2.  選取**案例**、，然後選取 [**執行伸展資料庫顧問**。

3.  在 [**執行伸展資料庫顧問**刀中，按一下 [**分析來選取資料庫**]。

4.  在 [**選取資料庫**刀中，輸入或選取伺服器名稱] 和 [驗證資訊。 按一下 [**連線**]。

5.  選取的伺服器上的資料庫清單隨即出現。 選取您要分析的資料庫。 按一下 [**選取**]。

6.  在 [**執行伸展資料庫顧問**刀中，按一下 [**執行**]。  執行分析。

## <a name="review-the-results"></a>檢閱結果

1.  分析完成時，在**Analyzed 資料庫**刀中，選取一個您要顯示**分析結果**刀分析的資料庫。

    **分析結果**刀列出建議的資料庫資料表中選取符合預設建議準則。

2.  在資料表**分析結果**刀清單中，選取其中一個建議來顯示**表格結果**刀資料表。

    如果那里會封鎖問題，**資料表結果**刀清單中選取資料表的封鎖的問題。 偵測到的伸展資料庫顧問封鎖問題的相關資訊，請參閱[伸展資料庫的限制](sql-server-stretch-database-limitations.md)。

3.  在封鎖的問題]**資料表結果**刀清單中，選取其中一個問題，以顯示所選的問題的詳細資訊，並建議補救步驟。 如果您想要設定所選的資料表伸展資料庫，請執行 [建議的降低步驟。

## <a name="next-step"></a>下一步
啟用自動縮放的資料庫。

-   若要在**資料庫**上啟用伸展資料庫，請參閱[啟用伸展資料庫的資料庫](sql-server-stretch-database-enable-database.md)。

-   若要伸展已啟用資料庫時，請在另一個**資料表**，啟用伸展資料庫，請參閱[啟用伸展資料庫的資料表](sql-server-stretch-database-enable-table.md)。

## <a name="see-also"></a>另請參閱

[延伸資料庫的限制](sql-server-stretch-database-limitations.md)

[啟用伸展資料庫的資料庫](sql-server-stretch-database-enable-database.md)

[啟用伸展資料庫表格](sql-server-stretch-database-enable-table.md)

[Azure SQL Server 伸展資料庫服務的所有主題](sql-server-stretch-database-index-all-articles.md)
