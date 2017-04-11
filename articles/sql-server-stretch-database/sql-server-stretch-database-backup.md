<properties
    pageTitle="備份伸展啟用資料庫 |Microsoft Azure"
    description="瞭解如何將備份伸展\-啟用資料庫。"
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
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>備份伸展啟用資料庫

資料庫備份可協助您復原有許多類型的錯誤、 錯誤，以及損毀。  

-   您必須先備份您伸展\-啟用 SQL Server 資料庫。  

-   Microsoft Azure 會自動備份伸展資料庫已從 SQL Server 移轉 Azure 遠端資料。  

>    [AZURE.NOTE] 備份是只有一個完整的可用性和業務連續性解決方案。 可用性的詳細資訊，請參閱[高可用性解決方案](https://msdn.microsoft.com/library/ms190202.aspx)。

## <a name="back-up-your-sql-server-data"></a>備份您的 SQL Server 資料  

若要備份您伸展\-啟用 SQL Server 資料庫，您可以繼續使用您目前使用的 SQL Server 備份方法。 詳細資訊，請參閱[備份與還原的 SQL Server 資料庫](https://msdn.microsoft.com/library/ms187048.aspx)。

啟用伸展的 SQL Server 資料庫的備份只包含本機資料和資料合格時執行備份的時間點移轉。 \(符合資格的資料是具有尚未遷移，但將也會移轉至 Azure 移轉設定的資料表為基礎的資料。\) 這就是**[淺層**備份，並不包含資料已被移轉到 Azure。  

## <a name="back-up-your-remote-azure-data"></a>備份遠端 Azure 資料   

Microsoft Azure 會自動備份伸展資料庫已從 SQL Server 移轉 Azure 遠端資料。  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure 減少使用自動備份資料遺失的風險  
Azure 的 SQL Server 伸展資料庫服務保護與自動儲存快照遠端資料庫，至少每個 8 小時。 它會保留在每個快照，為您提供可能的還原點範圍 7 天。  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure 減少與地理資料遺失的風險\-重複  
Azure 資料庫備份儲存在地理\-多餘 Azure 儲存體 (RA\-GRS)，因此地理\-預設重複。 地理\-多餘的儲存空間會將您的資料複製到下的數百個主要區域離英哩的第二個區域。 在主要和次要區域中，您的資料複製三次，每過個別故障網域和升級的網域。 如此一來，可確保您的資料是長期即使完成的地區中斷或損毀的轉譯 Azure 區域的其中一個無法使用。

### <a name="stretchRPO"></a>延伸資料庫減少 Azure 資料的資料遺失的風險暫時保留移轉的資料列
伸展資料庫可符合資格的資料列從 SQL Server 移轉 Azure 之後，它會保留暫存資料表中的最小值，則那些列的 8 小時。 如果您還原 Azure 資料庫的備份，伸展資料庫會使用儲存在臨時資料表中的資料列核對 SQL Server 和 Azure 資料庫。

還原備份的 Azure 資料之後，您必須執行重新連線伸展預存程序[sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) \-啟用遠端 Azure 資料庫的 SQL Server 資料庫。 當您執行**sys.sp_rda_reauthorize_db**時，伸展資料庫自動協調 SQL Server 和 Azure 資料庫。

若要增加移轉伸展資料庫暫時保留暫存的表格中的資料的時數，執行預存程序[sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) ]，並指定一個數字的時數大於 8。 若要決定要保留的資料量，請考慮下列因素︰
-   自動 Azure 備份 （至少每 8 小時） 的頻率。
-   若要識別問題，並決定要還原的備份之後問題, 所需的時間。
-   Azure 還原作業期間。

> [AZURE.NOTE] 增加伸展資料庫暫時保留暫存的表格中的資料量會增加 SQL Server 上所需的間距的量。

若要檢查的時數伸展資料庫目前保留暫時暫存的表格中的資料，請執行預存程序[sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx)。

## <a name="see-also"></a>另請參閱

[管理及疑難排解伸展資料庫](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[備份及還原的 SQL Server 資料庫](https://msdn.microsoft.com/library/ms187048.aspx)
