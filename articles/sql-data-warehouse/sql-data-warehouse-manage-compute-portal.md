<properties
   pageTitle="管理 Azure SQL Data Warehouse （Azure 入口網站） 中的計算 power |Microsoft Azure"
   description="Azure 入口網站的工作，管理計算 power。 縮放比例調整 DWUs 計算資源。 或者，將游標暫停於，然後繼續儲存成本的計算資源。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>管理 Azure SQL Data Warehouse （Azure 入口網站） 中的計算 power

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-manage-compute-overview.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [其餘](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


藉由出縮放比例效能計算資源和記憶體以符合您的工作量的變更要求。 儲存成本縮放比例返回資源峰時間或暫停計算期間完全。 

此工作的集合使用 Azure 入口網站︰

- 小數位數計算
- 暫停運算
- 履歷表運算

如需詳細資訊，請參閱[管理計算概觀][]。

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>縮放比例運算能力

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更計算資源︰

1. 開啟[Azure 入口網站][]，開啟您的資料庫，然後按一下 [**縮放比例**。

    ![按一下 [縮放比例][1]

1. 向左或向 DWU 設定變更，請在 [縮放比例防禦，以移動滑桿。

    ![將滑桿][2]

1. 按一下 [**儲存**]。 出現確認訊息。 按一下 [ **]**以確認或**沒有**取消]。

    ![按一下 [儲存]][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停運算

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要將游標停留在資料庫︰

1. 開啟[Azure 入口網站][]，並開啟您的資料庫。 請注意，狀態**Online**。 

    ![線上狀態][6]

1. 若要暫停計算和記憶體資源，請按一下 [**暫停**]，然後確認訊息會出現。 按一下 [ **]**以確認或**沒有**取消]。

    ![確認暫停][7]

1. SQL Data Warehouse 啟動資料庫時，雖然狀態就會是**Pausing**。
2. **暫停**狀態時，[暫停] 作業完成後，而且您不會再被會 DWUs 的。

    ![暫停狀態][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>履歷表運算

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]若要繼續資料庫︰

1. 開啟[Azure 入口網站][]，並開啟您的資料庫。 請注意的狀態是 [**暫停**。 

    ![暫停資料庫][4]

1. 若要繼續資料庫按一下 [**開始**]，就會顯示確認訊息。 按一下 [ **]**以確認或**沒有**取消]。

    ![確認履歷表][5]

1. SQL Data Warehouse 啟動資料庫時, 的狀態是 「 Resuming 」。
2. **線上**狀態時，就資料庫了。

    ![線上狀態][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱[管理概觀][]。

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[管理計算概觀]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure 入口網站]: http://portal.azure.com/
