<properties
   pageTitle="SQL 資料庫損毀修復 |Microsoft Azure"
   description="瞭解如何復原資料庫的區域資料中心中斷或失敗 Azure SQL 資料庫作用中地理複寫，與地理還原功能。"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>還原次要 Azure SQL 資料庫或容錯移轉

Azure SQL 資料庫提供中斷從復原下列功能︰

- [作用中的地理複寫](sql-database-geo-replication-overview.md)
- [地理還原](sql-database-recovery-using-backups.md#point-in-time-restore)

若要瞭解業務連續性案例與支援這些案例的功能，請參閱[業務連續性](sql-database-business-continuity.md)。

### <a name="prepare-for-the-event-of-an-outage"></a>準備中斷的事件

復原至另一個使用作用中的地理複寫或地理多餘的備份的資料區域成功，您需要準備伺服器在另一個資料中心成為新的主要伺服器中斷應該需要了，以及具有良好定義記錄並測試，以確保平滑復原的步驟。 包含下列準備步驟︰

- 找出要成為新的主要伺服器的另一個區域中的邏輯伺服器。 這是與作用中地理複寫，至少一個，也許每個第二個伺服器。 地理還原，這通常是您的資料庫所在的區域的[成對的區域](../best-practices-availability-paired-regions.md)中的伺服器。
- 識別，請您也可以定義上所需的使用者存取新的主要資料庫伺服器層級的防火牆規則。
- 決定要如何將使用者至新的主索引伺服器，例如重新導向，藉由變更的連接字串或變更 DNS 項目。
- 識別，請您也可以建立，必須出現在主版資料庫中的新主要伺服器上，並確保這些登入適當的權限在主版的資料庫中，如果有任何的登入。 如需詳細資訊，請參閱[SQL 資料庫安全性之後損毀修復](sql-database-geo-replication-security-config.md)
- 找出通知的規則，需要更新對應到新的主要資料庫。
- 文件上目前的主要資料庫的稽核設定
- 執行[損毀修復切入](sql-database-disaster-recovery-drills.md)。 若要進行地理還原模擬中斷，您可以刪除或重新命名的來源資料庫會導致應用程式連線失敗。 若要模擬中斷的作用中地理複寫，您可以停用導致應用程式 connectity 失敗的 web 應用程式或虛擬機器連線至資料庫或容錯移轉資料庫。

## <a name="when-to-initiate-recovery"></a>啟動修復的時機

修復作業會影響應用程式。 它需要變更 SQL 連接字串或使用 DNS 重新導向，而且可能會造成資料永久遺失。 因此，它應該只何時中斷可能最後一個時間超過您的應用程式復原時間目標。 應用程式部署至生產環境時您應該執行一般監視的應用程式狀況，並使用下列資料點宣告復原因為︰

1.  從應用程式層永久連線失敗至資料庫。
2.  Azure 入口網站與主要影響的區域中顯示事件的提醒。
3.  降級標示 Azure SQL 資料庫伺服器。

根據您的應用程式了停機時間，以及可能商務責任您可以考慮下列復原選項。

使用[取得可修復的資料庫](https://msdn.microsoft.com/library/dn800985.aspx)(*LastAvailableBackupDate*) 取得的最新的地理複寫還原點。

## <a name="wait-for-service-recovery"></a>等待服務修復

Azure 小組工作努力還原快速越好，但根據根目錄導致服務可用性可以需要幾小時或天數。  如果您的應用程式可容許嚴重停機時間，您可以直接等候復原完成。 在此情況下，不不需要任何動作，在您的組件。 您可以查看我們[Azure 服務健康狀況儀表板](https://azure.microsoft.com/status/)目前的服務狀態。 區域的復原之後將會還原應用程式的可用性。

## <a name="failover-to-geo-replicated-secondary-database"></a>容錯移轉至地理複製次要的資料庫

如果您的應用程式當機可能會導致商務責任您應該在應用程式中使用地理複寫的資料庫。 它會讓應用程式快速還原中斷的情況下的不同區域中的可用性。 瞭解如何[設定地理複寫](sql-database-geo-replication-portal.md)。

若要還原的資料庫，您需要啟動使用支援的兩種方法之一地理複寫次要容錯移轉。

使用其中一項動作來容錯移轉到地理複製的第二個資料庫輔助線︰

- [容錯移轉至地理複寫次要使用 Azure 入口網站](sql-database-geo-replication-portal.md)
- [容錯移轉至地理複寫次要使用 PowerShell](sql-database-geo-replication-powershell.md)
- [若要使用 T SQL 地理複寫次要容錯移轉](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>復原使用地理還原

如果您的應用程式當機不會導致商務責任您可以使用方法地理還原復原您的應用程式資料庫。 它會從其最新的地理重複備份建立資料庫的複本。

使用下列其中一項逐步引導至地理還原資料庫至新的區域︰

- [地理還原的資料庫，新的區域，使用 Azure 入口網站](sql-database-geo-restore-portal.md)
- [地理還原的資料庫，新的區域，使用 PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>復原後，設定您的資料庫

如果您使用 [地理複寫錯誤移轉] 或 [地理還原中斷從復原，您必須確定連線至新的資料庫已正確設定，讓可以繼續正常應用程式函數。 這是準備您復原的資料庫實際執行的工作檢查清單。

### <a name="update-connection-strings"></a>更新連線字串

因為您已復原的資料庫會位於不同的伺服器，您需要更新您的應用程式以指向該伺服器的連線字串。

如需變更的連接字串的詳細資訊，請參閱您[連線的文件庫](sql-database-libraries.md)的適當的開發語言。

### <a name="configure-firewall-rules"></a>設定防火牆規則

您需要確認設定和資料庫伺服器上的防火牆規則相符的主要資料庫與主要伺服器上的設定。 如需詳細資訊，請參閱[如何︰ 設定防火牆設定 （Azure SQL 資料庫）](sql-database-configure-firewall-settings.md)。


### <a name="configure-logins-and-database-users"></a>設定登入與資料庫的使用者

您需要，請確定有裝載復原的資料庫伺服器上使用您的應用程式的登入。 如需詳細資訊，請參閱[地理複寫的安全性設定](sql-database-geo-replication-security-config.md)。

>[AZURE.NOTE] 您應該設定並測試期間損毀復原向下切入的伺服器防火牆規則並登入 （和其權限）。 這些伺服器層級物件和其設定可能無法使用時中斷。

### <a name="setup-telemetry-alerts"></a>設定遙測通知

您需要，請確定您現有的警示規則設定會更新對應到復原的資料庫及不同的伺服器。

如需有關資料庫通知規則的詳細資訊，請參閱[接收提醒通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)和[追蹤服務健康狀況](../monitoring-and-diagnostics/insights-service-health.md)。

### <a name="enable-auditing"></a>啟用稽核

如果稽核，才能存取您的資料庫，必須先啟用資料庫復原之後。 稽核是必要的好指標是用戶端應用程式的使用安全連線字串中的圖樣 *。 database.secure.windows.net。 如需詳細資訊，請參閱[開始使用 SQL 資料庫稽核](sql-database-auditing-get-started.md)。


## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要進一步瞭解業務連續性設計和修復分析藍本，請參閱[連續性案例](sql-database-business-continuity.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
