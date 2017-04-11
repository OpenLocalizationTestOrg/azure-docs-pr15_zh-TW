<properties
    pageTitle="處理方式的 Azure 服務中斷影響 Azure 虛擬機器 |Microsoft Azure"
    description="瞭解如何進行的 Azure 服務中斷影響 Azure 虛擬機器。"
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>處理方式的 Azure 服務中斷影響 Azure 虛擬機器

在 Microsoft，我們使用實，請確定在需要時都可使用我們的服務。 我們控制強制有時候會影響我們會導致非預期的服務中斷的方式。

Microsoft 提供其服務存留時間與連線的認可與服務等級協定 (SLA)。 個別 Azure 服務 SLA 位於[Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

Azure 已經有許多內建的平台功能支援高度可用的應用程式。 如需這些服務的相關的詳細資訊，請閱讀[損毀修復與 Azure 應用程式的可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。

本文會說明 true 損毀修復狀況，整個區域發生主要自然損毀或廣泛的服務中斷中斷時。 這些是少見的項目，但您必須先準備是整個區域的中斷的可能性。 如果的整個區域發生服務中斷，您資料的本機多餘的複本會暫時無法使用。 如果您已啟用地理複寫，Azure 儲存體 blob 與資料表的三個額外的複本會儲存在不同區域中。 完成的地區資料或在其中的主要區域是無法復原損毀，事件 Azure 重新對應所有地理複製區域的 DNS 項目。

>[AZURE.NOTE]請注意，您不需要任何控制此程序，才會發生的整個區域的服務中斷。 因此，您必須也依賴達成最高層級的可用性其他特定應用程式的備份策略。 如需詳細資訊，請參閱一節的[資料損毀修復策略](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery)。

若要可協助您處理這些少見的項目，我們會提供下列指導 Azure 虛擬機器若是位置 Azure 虛擬機器部署應用程式的整個區域的服務中斷。

##<a name="option-1-wait-for-recovery"></a>選項 1︰ 等待復原
在此情況下，不不需要任何動作，在您的組件。 知道，我們正在努力還原服務的可用性。 您可以查看我們[Azure 服務健康狀況儀表板](https://azure.microsoft.com/status/)目前的服務狀態。

>[AZURE.NOTE]如果沒有設定 Azure 網站修復、 虛擬機器備份、 讀取權限地理多餘的儲存空間或之前中斷地理多餘的儲存空間，這是最佳選擇。 如果您已設定地理多餘的儲存空間或儲存帳戶的讀取權限地理多餘的儲存空間 VM 虛擬硬碟機 (Vhd) 的儲存位置，您可以復原基底圖像 VHD 看起來，並嘗試佈建從其新 VM。 因為不沒有同步處理資料的任何保證 Azure VM 備份或 Azure 網站復原會使用，否則，這是不慣用的選項。 因此，此選項不保證搭配使用。

如果客戶想要立即存取虛擬機器，下列兩個選項可供使用。  

>[AZURE.NOTE]請注意，這兩個下列選項有部分資料遺失的可能性。     

##<a name="option-2-restore-a-vm-from-a-backup"></a>選項 2︰ 從備份檔案還原 VM
已設定 VM 備份的客戶，您可以從備份及復原點還原 VM。

若要從 Azure 備份還原新 VM，請參閱[還原中 Azure 虛擬機器](../backup/backup-azure-restore-vms.md)。

若要協助您規劃 Azure 虛擬機器備份基礎結構，請參閱[規劃 Azure 中您 VM 備份基礎結構](../backup/backup-azure-vms-introduction.md)。

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>選項 [3︰ 使用 Azure 網站復原啟動容錯移轉
如果您已設定使用受影響 Azure 虛擬機器 Azure 網站復原，您可以還原您的 Vm 從他們的複本。 Azure 或內部部署，可以存放這些複本。 在此情況下，您可以建立新的 VM 從現有的複本。 若要還原您的 Vm 從 Azure 網站復原複本，請參閱[Azure 網站復原 Azure 區域之間的移轉 Azure IaaS 虛擬機器](../site-recovery/site-recovery-migrate-azure-to-azure.md)。

>[AZURE.NOTE]雖然 Azure 虛擬機器作業系統及資料磁碟會複製到第二個 VHD，地理多餘的儲存空間或讀取權限地理多餘的儲存帳戶時，每個 VHD 是獨立複寫。 此層級的複寫不保證的一致性跨複寫 Vhd。 如果您的應用程式及/或使用這些資料磁碟的資料庫有相依性彼此，不保證為其中一個快照複寫的所有 Vhd。 它也不保證開機 VM 應用程式一致快照集就會導致 VHD 複本從地理多餘的儲存空間或讀取權限地理多餘的儲存空間。

##<a name="next-steps"></a>後續步驟
若要瞭解如何實作損毀修復和可用性策略詳細資訊，請參閱[損毀修復和 Azure 應用程式的可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。

若要開發技術深入的瞭解雲端平台的功能，請參閱[Azure 恢復技術指南](../resiliency/resiliency-technical-guidance.md)。

若要瞭解如何備份 Vm，請參閱[備份 Azure 虛擬機器](../backup/backup-azure-vms.md)。

瞭解如何使用 Azure 網站復原協調和自動化保護的實際 （和虛擬） Windows 和 Linux 電腦的 VMWare 和 HYPER-V Vm 上執行，請參閱[Azure 網站復原](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)。

如果其指示都不會清除，或是如果您想要執行的作業，代表您的 Microsoft，連絡[客戶支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
