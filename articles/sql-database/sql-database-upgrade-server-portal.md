<properties
    pageTitle="使用 [Azure 入口網站升級至 Azure SQL 資料庫 V12 |Microsoft Azure"
    description="說明如何升級至 Azure SQL 資料庫 V12 包括如何升級網站和商務資料庫，以及如何升級 V11 伺服器移轉的資料庫，直接將使用 Azure 入口網站的彈性的資料庫集區。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>升級至 Azure SQL 資料庫 V12 使用 Azure 入口網站


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL 資料庫 V12 是最新版本，因此建議您以 SQL 資料庫 V12 升級現有的伺服器。
SQL 資料庫 V12 具有多個[前一版的優點](sql-database-v12-whats-new.md)包括︰

- 提高使用 SQL Server 相容。
- 改良的進階版效能與新的效能層級。
- [彈性的資料庫集區](sql-database-elastic-pool.md)。

本文提供指示以 SQL 資料庫 V12 升級現有的 SQL 資料庫 V11 伺服器和資料庫。

升級至 V12 的程序期間您將會升級任何網頁和商務資料庫為新的服務層，以便升級網站和商務資料庫的說明都包含在內。

此外，移轉至[彈性的資料庫資料庫](sql-database-elastic-pool.md)可成本比更有效單一資料庫升級個別的效能層級 （價格層）。 集區也簡化管理資料庫，因為您只需要管理資料庫，而不是個別管理的個別資料庫的效能層級的效能設定。 如果您將它們移到相同的伺服器，並利用將它們放入資料庫，請考慮在多個伺服器上的資料庫。 您可以輕鬆地[自動移轉直接將彈性的資料庫集區使用 PowerShell V11 伺服器的資料庫](sql-database-upgrade-server-powershell.md)。 您也可以使用 [入口網站，將 V11 資料庫移轉至資料庫，但在入口網站中您必須已經有建立資料庫的 V12 伺服器。 若要建立資料庫伺服器升級之後，如果您有[的資料庫的資料庫中的效益](sql-database-elastic-pool-guidance.md)本文稍後的提供指示。

請注意，資料庫會保持線上狀態，繼續處理整個升級作業。 在新的效能層級暫時卸除資料庫連線的實際的轉場的時間便會發生這通常是大約 90 秒的很小持續時間內，但可以約 5 分鐘。 如果您的應用程式有[暫時性錯誤的處理連線終止](sql-database-connectivity-issues.md)它能充分防範首字結尾的升級的連線。

升級至 SQL 資料庫 V12 無法復原。 升級後伺服器無法還原成 V11。

升級至 V12 之後，[服務層建議](sql-database-service-tier-advisor.md)和[彈性的資料庫效能考量](sql-database-elastic-pool-guidance.md)會立即無法使用直到服務有時間進行評估您新的伺服器上的工作量。 V11 伺服器建議記錄不適用於 V12 伺服器，讓它不會保留。

## <a name="prepare-to-upgrade"></a>準備升級

- **升級所有網頁和企業版的資料庫**︰ 請參閱都下面章節，或請參閱[都升級所有網頁和企業版的資料庫](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases)[監控和管理的彈性的資料庫集區 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)。
- **檢閱及擱置地理複寫**︰ 如果 Azure SQL 資料庫設為地理複寫您應該將文件其目前的設定與[停止地理複寫](sql-database-geo-replication-portal.md#remove-secondary-database)。 升級完成之後重新設定您的資料庫，以進行地理複寫。
- **開啟下列連接埠，如果您的用戶端上 Azure VM**︰ 如果您的用戶端程式連線到 SQL 資料庫 V12 時您的用戶端執行 Azure 虛擬機器 (VM) 上時，您必須開啟連接埠範圍 11000 11999 和 14000-14999 VM 上。 如需詳細資訊，請參閱[SQL 資料庫 V12 的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。



## <a name="start-the-upgrade"></a>開始升級

1. [Azure 入口網站](https://portal.azure.com/)瀏覽至伺服器中您想要升級，選取 [**瀏覽** > **SQL server**，並選取您想要升級 v2.0 伺服器。
2. 選取**最新的 SQL 資料庫更新**]，然後選取 [**升級此伺服器**。

      ![升級伺服器][1]

3. 升級至最新的 SQL 資料庫更新的伺服器是永久、 無法復原。 若要確認升級，請輸入您的伺服器名稱，然後按一下**[確定]**。

## <a name="upgrade-all-web-and-business-databases"></a>升級所有網頁和商務資料庫

如果您的伺服器有任何網頁或企業版的資料庫，您必須升級它們。 升級至 SQL 資料庫 V12 的程序期間您將會更新所有網頁和商務資料庫至新的服務層。    

為協助您升級，SQL 資料庫服務建議以適當的服務層和效能等級 （價格層） 每個資料庫。 服務建議的最佳層執行現有資料庫的工作量來分析資料庫的歷程記錄的使用方式。

3. 在**升級此伺服器**刀選取每個檢閱資料庫並升級至建議價格層的選取。 您隨時都可以瀏覽可用的價格層] 並選取最適合您的環境的。


     ![資料庫][2]


7. 按一下 [建議的層後會顯示以**選擇您的價格層**刀，您可以在其中選取層，然後按一下 [**選取**] 按鈕來變更為該層。 選取新的層每個網頁或企業版的資料庫。

    重要事項請注意是 SQL 資料庫無法鎖定至任何特定服務層] 或 [效能層級，讓資料庫變更您的需求來為您可以輕鬆地變更可用的服務層和效能層級之間。 事實上，Basic、 標準和進階版 SQL 資料庫由小時、 向收費，而且您具有能夠在 24 小時制期間內 4 次向上或向下調整每個資料庫。

    ![建議][6]


在伺服器上的所有資料庫都都有資格之後，您都就可以準備開始升級

## <a name="confirm-the-upgrade"></a>確認升級

3. 在伺服器上的所有資料庫有資格申請升級時必須先**輸入伺服器名稱**以驗證您要執行升級，然後按一下**[確定]**。

    ![驗證升級][3]


4. 升級會啟動並顯示在進度通知。 已啟動升級程序。 根據特定資料庫的詳細資料升級至 V12 可能需要一些時間。 在此期間內所有資料庫伺服器上的會保持線上都狀態，但伺服器和資料庫管理動作會限制。

    ![升級過程中][4]

    在新的效能實際的轉場的時間層級暫時拖放至資料庫的連線就會發生的很小的持續時間內 （通常是以 （秒））。 如果應用程式有連線終止暫時性錯誤處理 （重試邏輯），那麼就可以防範首字結尾的升級的連線。

5. 升級作業完成後**最新的更新**刀會顯示**已啟用**。

    ![啟用 V12][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>將資料庫移到彈性的資料庫資料庫

[Azure 入口網站](https://portal.azure.com/)中瀏覽至 V12 伺服器，然後按一下 [**增益集區**。

-或者-

如果您看到訊息，指出 [**按一下這裡以檢視此伺服器的建議彈性資料庫集區**，請按一下以輕鬆地建立資料庫的最適合您的伺服器的資料庫。 如需詳細資訊，請參閱[的彈性的資料庫資料庫價格與效能考量](sql-database-elastic-pool-guidance.md)。

![新增至伺服器的資料庫][7]

依照[建立彈性的資料庫資料庫](sql-database-elastic-pool.md)文件，完成建立您的資料庫中的指示。

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>監控資料庫升級為 SQL 資料庫 V12 之後

>[AZURE.IMPORTANT] 升級至最新版本的 SQL Server 管理 Studio (SSMS) 以善用新的 v12 功能。 [下載 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。

升級之後，主動監視資料庫，以確保在想要的效能，執行應用程式，然後再最佳化所需的設定。

除了監控個別的資料庫，您可以監視彈性的資料庫集區[監視器，管理及與 Azure 入口網站的彈性的資料庫集區的大小](sql-database-elastic-pool-manage-portal.md)或使用[PowerShell](sql-database-elastic-pool-manage-powershell.md)。


**資源消耗資料︰**基本、 標準和進階版的資料庫的資源消耗資料是透過[sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV 使用者資料庫中。 這個 DMV 提供附近即時耗用的資源資訊在 15 的第二個資料粒度作業的前一個小時。 時間間隔 DTU 百分比消耗計算方式 [最大百分比耗用的 CPU、 IO 和記錄維度。 以下是查詢來計算平均 DTU 百分比消耗移到最後一個小時︰

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

監控的其他資訊︰

- [Azure SQL 資料庫效能指導單一的資料庫](http://msdn.microsoft.com/library/azure/dn369873.aspx)。
- [彈性的資料庫資料庫的價格和效能考量](sql-database-elastic-pool-guidance.md)。
- [使用動態管理檢視監控 Azure SQL 資料庫](sql-database-monitoring-with-dmvs.md)




**通知︰**「 通知 」 中設定 Azure 入口網站升級後的資料庫 DTU 消耗接近特定高層級時通知您。 資料庫通知可以 DTU、 CPU、 IO 及記錄等各種效能眼 Azure 入口網站中的設定。 瀏覽至您的資料庫，然後選取 [**設定**刀中的 [**提醒的規則**。

例如，您可以設定 「 DTU 百分比 」 的電子郵件警示如果 DTU 百分比值與平均值超過 75%移到最後一個 5 分鐘。 若要進一步瞭解如何設定提醒通知，請參閱接收[提醒通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。





## <a name="next-steps"></a>後續步驟

- [檢查集區建議和建立資料庫](sql-database-elastic-pool-create-portal.md)。
- [變更您的資料庫的服務層和效能層級](sql-database-scale-up.md)。



## <a name="related-links"></a>相關的連結

- [在 SQL 資料庫 V12 中的新功能](sql-database-v12-whats-new.md)
- [規劃及準備升級至 SQL 資料庫 V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png
