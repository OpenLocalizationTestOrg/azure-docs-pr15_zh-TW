<properties 
   pageTitle="使用 [Azure 入口網站的 azure SQL 資料庫顧問 |Microsoft Azure" 
   description="您可以使用 Azure SQL 資料庫顧問 Azure 入口網站中，檢閱及實作您現有的 SQL 資料庫，可提升目前的查詢效能的建議。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>SQL 資料庫顧問使用 Azure 入口網站

> [AZURE.SELECTOR]
- [SQL 資料庫顧問概觀](sql-database-advisor.md)
- [入口網站](sql-database-advisor-portal.md)

您可以使用 Azure SQL 資料庫顧問 Azure 入口網站中，檢閱及實作您現有的 SQL 資料庫，可提升目前的查詢效能的建議。

## <a name="viewing-recommendations"></a>檢視建議

[建議] 頁面會讓您檢視的頂端建議根據他們可能影響以提升效能。 您也可以檢視歷史作業的狀態。 選取 [若要查看更多詳細資料的建議或狀態]。

若要檢視並套用建議，您需要 Azure 中正確的[角色型存取控制](../active-directory/role-based-access-control-configure.md)」 權限。 **閱讀程式**， **SQL DB 參與者**] 權限，才能檢視建議和**擁有者**、 **SQL DB 參與者**] 權限，才能執行任何動作。建立或卸除索引並取消建立索引。

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下 [**更多服務** > **SQL 資料庫**]，然後選取您的資料庫。
5. 按一下 [檢視可用的建議，選取資料庫的**效能的建議**]。

> [AZURE.NOTE] 若要取得建議資料庫必須有一天的使用狀況]，需要有一些活動。 也需要有一些一致的活動。 SQL 資料庫顧問可以更輕鬆地最佳化以用於一致的查詢模式比它的活動的隨機 spotty 高速量。 如果無法使用 [建議，則**效能的建議**] 頁面應提供訊息說明原因。

![建議](./media/sql-database-advisor-portal/recommendations.png)

以下是 Azure 入口網站中的 「 修正結構描述問題 」 建議的範例。

![修正結構描述問題](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

建議被依據可能影響效能為下列四種類別︰

| 影響 | 描述 |
| :--- | :--- |
| 高 | 高影響建議應提供最重要的效能影響。 |
| 媒體 | 中度的影響建議改善效能，但不是大幅。 |
| 低 | 低影響建議應提供比較佳的效能不，但改良項目可能不是有效。 


### <a name="removing-recommendations-from-the-list"></a>從清單移除建議

如果您的建議清單中包含您想要從清單中移除的項目，您可以捨棄建議︰

1. 選取 [**建議**的清單中的 [建議]。
2. 按一下 [**詳細資料**刀上的 [**捨棄**]。


如有需要，您可以新增捨棄的項目，回到 [**建議**] 清單︰

1. 按一下 [**建議**刀的 [**檢視捨棄**]。
1. 捨棄的項目清單中選取來檢視其詳細資料。
1. 您也可以按一下 [若要返回至主**建議**清單中新增索引的 [**復原放棄**]。



## <a name="applying-recommendations"></a>套用建議

SQL 資料庫顧問可讓您控制建議的都是完整啟用使用任何的下列三個選項︰ 

- 一次套用個別建議項目。
- 啟用會自動套用 [建議顧問 （目前適用於索引建議僅限）。
- 若要手動實作建議，請針對資料庫執行建議的 T SQL 指令碼。

選取 [檢視詳細資料，然後按一下 [**檢視指令碼**來檢閱確切的詳細資料的建議的建立方式的任何建議。

資料庫保持線上狀態時顧問適用於建議-使用 SQL 資料庫顧問永遠不會資料庫離線。

### <a name="apply-an-individual-recommendation"></a>套用個別的建議

您可以檢閱並接受建議一次。

1. 在 [**建議**刀中，按一下 [建議]。
2. 在**詳細資料**刀上按一下 [**套用**]。

    ![套用建議](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>啟用自動索引管理

您可以設定自動實作建議 SQL 資料庫顧問。 隨著建議會自動套用它們。 為所有如果的效能影響是負數，管理服務的索引作業與建議將還原。

1. 按一下 [**建議**刀，**自動化**︰

    ![Advisor 設定](./media/sql-database-advisor-portal/settings.png)

2. 將顧問設為 [自動**建立**或**卸除**索引︰

    ![建議的索引](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>手動執行建議的 T SQL 指令碼

選取任何建議，然後按一下 [**檢視指令碼**。 若要手動套用建議您資料庫執行這個指令碼。

*手動執行的索引不監看與驗證服務的效能影響*，建議您之後建立驗證他們提供提升效能及調整，或是在必要時，請刪除這些監控這些索引。 如需建立索引的詳細資訊，請參閱[建立索引 (SQL)](https://msdn.microsoft.com/library/ms188783.aspx)。


### <a name="canceling-recommendations"></a>取消建議

您可以取消**擱置**]、 [**確認**或 [**成功**狀態的建議。 無法取消的**執行**狀態為 [建議。

1. 開啟**建議詳細資料**刀**調整歷程記錄**] 區域中選取建議。
2. 按一下 [**取消**中止套用建議的程序。



## <a name="monitoring-operations"></a>監控作業

套用建議可能不會發生立即。 入口網站提供有關建議作業的狀態。 以下是可能可以是索引的狀態︰

| 狀態 | 描述 |
| :--- | :--- |
| 擱置 | 套用建議命令已收到及排程來執行。 |
| 執行 | 已套用的建議。 |
| 成功 | 已成功套用建議。 |
| 錯誤 | 套用 [建議的程序時，發生錯誤。 這可能是暫時性問題，或可能結構描述變更資料表和指令碼已不再有效。 |
| 還原 | 建議套用，但已非效能，並自動還原。 |
| 還原 | 已還原的建議。 |

按一下 [從清單中處理程序建議，請參閱更多詳細資料︰

![建議的索引](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>還原建議

如果您要套用的建議使用顧問 （這表示您並未手動執行 T SQL 指令碼） 它會自動還原，如果找到要負數的效能影響。 如果您只想要還原建議任何原因，您可以下列動作︰


1. **調整歷程記錄**] 區域中選取的成功套用的建議。
2. 按一下 [**建議的詳細資料**刀上的 [**還原**]。

![建議的索引](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>監控效能影響索引建議

建議的成功實作之後 （目前編製索引作業，參數化查詢建議只） 您可以按一下 [開啟[查詢效能深入資訊](sql-database-query-performance.md)，並查看您的主要查詢的效能影響建議詳細資料刀的 [**查詢深入資訊**。

![監控效能的影響](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>摘要

SQL 資料庫顧問提供改善 SQL 資料庫效能的建議。 藉由提供 T SQL 指令碼，以及個別和完整自動 （目前僅限索引），顧問提供最佳化您的資料庫，以及最後改進查詢效能很有幫助的協助。



## <a name="next-steps"></a>後續步驟

監控您的建議，繼續套用改善效能。 資料庫工作負載的動態，並持續變更。 SQL 資料庫顧問會繼續監視，並提供可改善您的資料庫效能的建議。 

 - 如需 SQL 資料庫顧問的概觀，請參閱[SQL 資料庫顧問](sql-database-advisor.md)。
 - 若要瞭解如何檢視您的主要查詢的效能影響的[查詢效能深入資訊](sql-database-query-performance.md)，請參閱。

## <a name="additional-resources"></a>其他資源

- [查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx)
- [建立索引](https://msdn.microsoft.com/library/ms188783.aspx)
- [角色型存取控制](../active-directory/role-based-access-control-configure.md)






