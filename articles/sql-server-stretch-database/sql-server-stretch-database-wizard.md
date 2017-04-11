<properties
    pageTitle="開始執行 [伸展] 精靈的 [啟用資料庫 |Microsoft Azure"
    description="瞭解如何設定資料庫伸展資料庫執行 [伸展] 精靈的 [啟用資料庫。"
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
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>開始執行 [伸展] 精靈的 [啟用資料庫

若要設定伸展資料庫的資料庫，請伸展精靈執行啟用資料庫。  本主題即說明您必須輸入的資訊和選項，您必須在精靈中。

若要進一步瞭解伸展資料庫，請參閱[伸展資料庫](sql-server-stretch-database-overview.md)。

 >   [AZURE.NOTE] 之後，如果您停用伸展資料庫，請記住，如果是資料表，或為資料庫停用伸展資料庫並不會刪除遠端物件。 如果您想要刪除遠端資料表或遠端資料庫，您必須放使用 Azure 管理入口網站。 遠端物件繼續產生 Azure 成本，直到您手動將其刪除。 

## <a name="launch-the-wizard"></a>啟動精靈

1.  在 [物件總管，SQL Server Management Studio 中選取您要啟用伸展的資料庫。

2.  向右\-按一下及選取 [**工作**]，然後選取 [**伸展**]、 [，然後選取啟動精靈的 [**啟用**。

## <a name="Intro"></a>簡介
檢閱精靈及必要條件的目的。

將重要的必要條件包括下列各項︰

-   您必須是系統管理員，才能變更資料庫設定。
-   您必須具備 Microsoft Azure 訂閱。
-   SQL Server 有能夠與遠端 Azure 伺服器進行通訊。

![[伸展資料庫精靈] 的簡介頁面][StretchWizardImage1]

## <a name="Tables"></a>選取表格
選取您想要啟用伸展的資料表。

包含許多列的表格會顯示在頂端的 [排序] 清單中。 精靈會顯示表格的清單之前，分析其目前不支援伸展資料庫的資料類型。

![選取 [伸展資料庫精靈] 的 [資料表] 頁面][StretchWizardImage2]

|資料行|描述|
|----------|---------------|
|（沒有標題）|核取核取方塊，在 [此欄啟用伸展所選的資料表。|
|**名稱**|指定表格中的資料行的名稱。|
|（沒有標題）|在 [此欄的符號代表警告並不表示\'t，您無法從啟用伸展所選的資料表。 它也可能表示封鎖的問題，防止您為 [伸展] 啟用所選的資料表\-，例如，因為資料表使用不受支援的資料類型。 將游標停留在 [要顯示在工具提示的詳細資訊的符號。 取得詳細資訊，請參閱[伸展資料庫的限制](sql-server-stretch-database-limitations.md)。|
|**延伸**|指出是否伸展已啟用表格。|
|**移轉**|您可以將整個表格 （**整個表格**） 移轉，或者您可以指定在表格中的現有資料行的篩選。 如果您想要使用不同的篩選函數來選取要移轉的列，請執行 ALTER TABLE 陳述式之後結束精靈指定 filter 函數。 Filter 函數的詳細資訊，請參閱[選取要使用篩選函數移轉的資料列](sql-server-stretch-database-predicate-function.md)。 如需如何套用函數的詳細資訊，請參閱[啟用伸展資料庫的資料表](sql-server-stretch-database-enable-table.md)或[ALTER TABLE (SQL)](https://msdn.microsoft.com/library/ms190273.aspx)。|
|**資料列**|在資料表中指定列的數。|
|**(Kb)**|指定 KB 的表格大小。|

## <a name="Filter"></a>您也可以提供資料列篩選

如果您想要提供篩選函數以選取要移轉的列，請執行下列項目，在 [**選取表格]**頁面上。

1.  **選取您要調整的資料表**清單中，按一下**整個表格**列中的資料表。 **選取 [伸展的資料列**] 對話方塊隨即開啟。

    ![定義篩選函數][StretchWizardImage2a]

2.  在**選取列縮放**] 對話方塊中，選取 [**選擇資料列**。

3.  在**名稱] 欄位**中，提供 filter 函數的名稱。

4.  **Where**子句，挑選的資料表的資料行、 挑選運算子，和提供的值。

5. 按一下 [**檢查**測試函數。 如果資料表的函數會傳回的結果就是，如果沒有符合條件的移轉資料列測試報告**成功**。

    >   [AZURE.NOTE] 顯示篩選查詢文字方塊是唯讀的。 您無法編輯文字方塊中的查詢。

6.  按一下 [完成] 以返回 [**選取資料表**] 頁面。

只有在您完成精靈時，會建立在 SQL Server filter 函數。 之前，您可以返回 [**選取表格]**頁面來變更或重新命名 filter 函數。

![選取後定義篩選函數的 [資料表] 頁面][StretchWizardImage2b]

如果您想要使用不同類型的 filter 函數以選取要移轉的列，請執行其中一個下列項目。  

-   結束精靈，並執行 ALTER TABLE 陳述式以啟用伸展的資料表，並指定 filter 函數。 取得詳細資訊，請參閱[啟用伸展資料庫的資料表](sql-server-stretch-database-enable-table.md)。  

-   執行指定 filter 函數後結束精靈 ALTER TABLE 陳述式。 所需的步驟，請參閱[執行精靈之後將篩選函數](sql-server-stretch-database-predicate-function.md#addafterwiz)。

## <a name="Configure"></a>設定 Azure 部署

1.  使用 Microsoft 帳戶登入 Microsoft Azure。

    ![登入 Azure-伸展資料庫] 精靈][StretchWizardImage3]

2.  選取要用於伸展資料庫 Azure 現有訂閱。

3.  選取 Azure 的地區。
    -   如果您建立新的伺服器時，伺服器會建立在此區域中。  
    -   如果您選取的區域中有現有的伺服器，精靈會列出這些當您選擇**現有伺服器**。

    若要最小化延遲，挑選 Azure SQL Server 所在的區域。 如需更多相關區域的詳細資訊，請參閱[Azure 區域](https://azure.microsoft.com/regions/)。

4.  指定您是否要使用現有的伺服器或建立新的 Azure 伺服器。

    如果您的 SQL Server 上 Active Directory 與 Azure Active Directory 同盟，您可以選擇性地使用同盟的服務帳戶的 SQL Server，與遠端 Azure 伺服器進行通訊。 這個選項的需求的詳細資訊，請參閱[變更資料庫設定選項 (SQL)](https://msdn.microsoft.com/library/bb522682.aspx)。

    -   **建立新的伺服器**

        1.  建立伺服器管理員以登入和密碼。

        2.  您也可以使用同盟的服務帳戶的 SQL Server 與遠端 Azure 伺服器進行通訊。

        ![建立新的 Azure 伺服器-伸展資料庫] 精靈][StretchWizardImage4]

    -   **現有的伺服器**

        1.  選取現有的 Azure 伺服器。

        2.  選取驗證方法。

            -   如果您選取**SQL Server 驗證**，提供的管理員登入和密碼。

            -   選取 [**作用中的目錄整合式驗證**使用同盟的服務帳戶的 SQL Server，與遠端 Azure 伺服器進行通訊。 如果選取的伺服器不與 Azure Active Directory 整合，才會出現這個選項。

        ![選取現有的 Azure 伺服器-伸展資料庫] 精靈][StretchWizardImage5]

## <a name="Credentials"></a>安全認證
您必須有資料庫主索引鍵安全性伸展資料庫連線到遠端資料庫所使用的認證。  

如果資料庫主索引鍵已存在，請輸入密碼。  

![伸展資料庫精靈] 的 [安全的認證] 頁面][StretchWizardImage6b]

如果資料庫沒有任何現有的主索引鍵，輸入強式密碼來建立資料庫的主索引鍵。  

![伸展資料庫精靈] 的 [安全的認證] 頁面][StretchWizardImage6]

資料庫的主索引鍵的詳細資訊，請參閱[建立主索引鍵 (SQL)](https://msdn.microsoft.com/library/ms174382.aspx)和[建立資料庫的主索引鍵](https://msdn.microsoft.com/library/aa337551.aspx)。 如需更多相關的認證，精靈會建立的詳細資訊，請參閱[建立資料庫範圍認證 (SQL)](https://msdn.microsoft.com/library/mt270260.aspx)。

## <a name="Network"></a>選取 [IP 位址
建立可讓 SQL Server 進行通訊與遠端 Azure 伺服器的 Azure 防火牆規則，使用子網路 IP 位址範圍 （建議使用） 或您的公用 IP 位址。

IP 位址或您在此頁面提供的地址告知 Azure 伺服器允許傳入資料、 查詢和 SQL server 通過 Azure 防火牆發起的租用戶管理作業。 精靈不會變更在 SQL Server 的防火牆設定中的任何項目。

![選取 [伸展資料庫精靈] 的 IP 位址頁面][StretchWizardImage7]

## <a name="Summary"></a>摘要
檢閱您輸入的值與您在精靈] 和 [Azure 的估計的成本所選取的選項。 然後選取 [**完成**]，以啟用 [伸展]。

![摘要伸展資料庫精靈] 頁面][StretchWizardImage8]

## <a name="Results"></a>結果
檢閱結果。

若要監視的資料移轉狀態，請參閱[監視器和疑難排解資料移轉 （伸展資料庫）](sql-server-stretch-database-monitor.md)。

![[伸展資料庫精靈] 的 [結果] 頁面][StretchWizardImage9]

## <a name="KnownIssues"></a>疑難排解精靈
**無法自動縮放資料庫精靈。**
如果伸展資料庫尚未啟用伺服器層級，您執行精靈沒有系統管理員權限，若要啟用它，精靈會失敗。 要求系統管理員啟用伸展資料庫的本機伺服器執行個體，，然後再執行一次精靈。 如需詳細資訊，請參閱[必要︰ 若要啟用伸展資料庫伺服器上的權限](sql-server-stretch-database-enable-database.md#EnableTSQLServer)。

## <a name="next-steps"></a>後續步驟
伸展資料庫中啟用其他表格。 監視資料移轉及管理伸展\-啟用資料庫及表格。

-   若要啟用其他資料表[啟用伸展資料庫的資料表](sql-server-stretch-database-enable-table.md)。

-   [監視器和疑難排解資料移轉](sql-server-stretch-database-monitor.md)若要查看的資料移轉狀態。

-   [將游標暫停並繼續伸展資料庫](sql-server-stretch-database-pause.md)

-   [管理及疑難排解伸展資料庫](sql-server-stretch-database-manage.md)

-   [備份伸展啟用資料庫](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>另請參閱

[啟用伸展資料庫的資料庫](sql-server-stretch-database-enable-database.md)

[啟用伸展資料庫表格](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png
