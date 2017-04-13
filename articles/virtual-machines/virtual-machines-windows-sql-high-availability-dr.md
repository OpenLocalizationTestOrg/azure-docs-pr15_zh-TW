<properties
    pageTitle="高可用性和 SQL server 損毀修復 |Microsoft Azure"
    description="SQL Server 執行 Azure 虛擬機器中的商務連絡人的 HADR 策略各種類型的討論區。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>SQL Server Azure 虛擬機器中的商務連絡人的高可用性和損毀復原

## <a name="overview"></a>概觀

使用 SQL Server 的 Microsoft Azure 虛擬機器 (Vm) 可協助降低高可用性和損毀修復 (HADR) 資料庫解決方案的成本。 大部分的 SQL Server HADR 解決方案支援 Azure 虛擬機器，同時設為僅 Azure 和混合式解決方案。 Azure 專用的解決方案，請在整個 HADR 系統執行 Azure 中。 在混合式組態中，解決方案的組件會在 Azure 及其他組件執行內部部署組織中執行。 Azure 環境的彈性，可讓您將部分或全部 Azure 以移至符合預算和 SQL Server 資料庫系統的 HADR 需求。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>了解需要 HADR 解決方案

是由您合作以確保您的資料庫系統擁有服務等級協定 (SLA) 需要 HADR 功能。 Azure 提供高可用性機制，例如治療雲端服務及服務的虛擬機器復原偵測事實並本身不保證您可以符合所要的 SLA。 這些機制保護 Vm 的但不是執行 Vm 內的 SQL Server 的可用性。 很可能會失敗時 VM 器在線上並且健全的 SQL Server 執行個體。 此外，甚至的可用性停機時間的狀況的事件，例如從軟體或硬體失敗及作業系統升級復原 Vm 允許 Azure 所提供的機制。

此外，地理多餘儲存空間 (GRS)，在 Azure 實作與稱為 「 地理複寫的功能時，可能無法資料庫的適當損毀修復解決方案。 因為地理複寫非同步傳送資料，新的更新可能會遺失損毀事件。 更多關於地理複寫限制覆蓋[地理複寫不支援在不同磁碟上的資料和記錄檔](#geo-replication-support)] 區段中。

## <a name="hadr-deployment-architectures"></a>HADR 部署架構

Azure 中支援的 SQL Server HADR 技術包括︰

- [一律在可用性群組](https://technet.microsoft.com/library/hh510230.aspx)
- [資料庫鏡像](https://technet.microsoft.com/library/ms189852.aspx)
- [記錄傳送](https://technet.microsoft.com/library/ms187103.aspx)
- [備份與還原 Azure Blob 儲存服務](https://msdn.microsoft.com/library/jj919148.aspx)
- [一律在容錯移轉叢集執行個體](https://technet.microsoft.com/library/ms189134.aspx)

若要合併在一起，以執行 SQL Server 解決方案有可用性和損毀修復功能技術可能是。 根據您使用的技術，混合式部署可能會要求要有 vpn 才能通道與 Azure 虛擬網路。 以下各節說明您範例部署架構的部分。

## <a name="azure-only-high-availability-solutions"></a>Azure 專用︰ 可用性解決方案

您可以可用性解決方案的 SQL Server 資料庫中使用永遠在可用性群組 Azure 或資料庫鏡像。

| 技術                               | 範例架構                    |
| ---------------------------------------- | ---------------------------------------- |
| **一律在可用性群組**        | 在 Azure Vm 執行高可用性相同的區域內的所有可用性複本。 您需要設定網域控制站 VM，因為 Windows Server 容錯移轉叢集 (WSFC) 需要 Active Directory 網域。<br/> ![一律在可用性群組](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>如需詳細資訊，請參閱[設定一律上顯示狀態群組中 Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。 |
| **一律在容錯移轉叢集執行個體** | 容錯移轉叢集執行個體 (FCI)，需要進行共用的儲存空間，可以建立 2 不同的方式。<br/><br/>1.FCI 上兩個節點 WSFC 執行 Azure Vm 中支援的協力廠商叢集解決方案的儲存空間。 使用 SIOS DataKeeper 的特定範例，請參閱[使用 WSFC 和第 3 廠商軟體 SIOS Datakeeper 檔案共用的可用性](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。<br/><br/>2.在執行中 Azure Vm 遠端 iSCSI 目標兩個節點 WSFC FCI 共用透過 ExpressRoute 區塊儲存空間。 例如，NetApp 私人儲存空間 (NPS) 公開透過使用 Equinix 至 Azure Vm ExpressRoute iSCSI 目標。<br/><br/>第三方共用的儲存空間及資料複寫解決方案，您應該連絡有關存取容錯移轉資料的任何問題的廠商。<br/><br/>請注意， [Azure 檔案儲存空間](https://azure.microsoft.com/services/storage/files/)的上方 FCI 不支援使用，因為此方案不會使用進階版儲存空間。 我們正在支援此推出。 |

## <a name="azure-only-disaster-recovery-solutions"></a>Azure 專用︰ 損毀修復解決方案

您可以有損壞修復解決方案 Azure 使用永遠上顯示狀態群組中的 SQL Server 資料庫、 資料庫鏡像，或備份和還原 blob 儲存體。

| 技術                               | 範例架構                    |
| ---------------------------------------- | ---------------------------------------- |
| **一律在可用性群組**        | 透過 Azure Vm 在多個資料中心的損毀修復執行可用性複本。 此跨地區解決方案會防止完成網站中斷。 <br/> ![一律在可用性群組](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>區域，內所有複本都應該在相同的雲端服務，相同的 VNet。 每個區域會有個別的 VNet，因為這些解決方案需要 VNet VNet 連線。 如需詳細資訊，請參閱[設定網站-VPN Azure 傳統入口網站中](../vpn-gateway/vpn-gateway-site-to-site-create.md)。 |
| **資料庫鏡像**                   | 本金和左右對稱及執行的不同的資料中心的損毀修復伺服器。 您必須部署使用伺服器憑證，因為 active directory 網域無法橫跨多個資料中心。<br/>![資料庫鏡像](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **備份與還原 Azure Blob 儲存服務** | 資料庫備份直接在不同的資料中心的損毀修復 blob 儲存體。<br/>![備份與還原](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>如需詳細資訊，請參閱[備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-backup-recovery.md)。 |

## <a name="hybrid-it-disaster-recovery-solutions"></a>混合式 IT︰ 損毀修復解決方案

您可以有損壞修復解決方案的 SQL Server 資料庫使用永遠在可用性群組、 資料庫鏡像、 記錄傳送和備份混合式 IT 環境中，並還原與 Azure 部落格的儲存空間。

| 技術                               | 範例架構                    |
| ---------------------------------------- | ---------------------------------------- |
| **一律在可用性群組**        | Azure Vm 和其他執行內部部署的跨網站損毀修復的複本中執行的一些可用性複本。 生產網站可能是內部部署或 Azure 資料中心。<br/>![一律在可用性群組](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>所有的可用性複本必須使用相同的 WSFC 叢集，因為 WSFC 叢集必須跨兩個網路 （多重子網路 WSFC 叢集）。 此設定會需要 VPN 連線 Azure 及內部部署網路之間。<br/><br/>成功損毀修復您的資料庫，您也應該安裝複本網域控制站損毀復原網站。<br/><br/>就可以使用 [新增複本精靈 SSMS 中將 Azure 的複本新增至現有永遠上可用的群組。 如需詳細資訊，請參閱教學課程︰ 將您永遠在可用性群組延伸到 Azure。 |
| **資料庫鏡像**                   | 執行 Azure VM 和其他執行中的一個合作夥伴內部部署的跨網站損毀修復使用伺服器憑證。 不需要合作夥伴位於相同的 Active Directory 網域，並沒有 VPN 連線時需要。<br/>![資料庫鏡像](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>另一個資料庫鏡像案例涉及執行 Azure VM 和其他執行內部部署中的跨網站損毀修復相同的 Active Directory 網域中的一個合作夥伴。 [Azure 虛擬網路與內部部署網路之間的 VPN 連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)時需要。<br/><br/>成功損毀修復您的資料庫，您也應該安裝複本網域控制站損毀復原網站。 |
| **記錄傳送**                         | 執行 Azure VM 和其他執行中的一個伺服器的內部部署的跨網站損毀修復。 記錄傳送取決於 Windows 檔案共用，因此不用 Azure 虛擬網路與內部部署網路之間的 VPN 連線。<br/>![記錄傳送](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>成功損毀修復您的資料庫，您也應該安裝複本網域控制站損毀復原網站。 |
| **備份與還原 Azure Blob 儲存服務** | 內部部署生產資料庫備份直接到損毀修復 Azure blob 儲存體。<br/>![備份與還原](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>如需詳細資訊，請參閱[備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-backup-recovery.md)。 |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>SQL Server HADR Azure 中的重要事項

Azure Vm、 儲存及網路會有不同於內部部署，非虛擬化 IT 基礎結構的操作特性。 Azure 中 HADR SQL Server 解決方案的成功實作，您必須瞭解這些差異和設計您的方案，以包含它們。

### <a name="high-availability-nodes-in-an-availability-set"></a>設定高可用性節點可用性

Azure 中的可用性組可讓您將可用性節點放置到另一個錯誤網域 (FDs) 和更新的網域 (UDs)。 Azure Vm 放置在相同的顯示狀態設定為您必須將其部署相同的雲端服務中。 只有在相同的雲端服務中的節點可以參與相同的可用性設定。 如需詳細資訊，請參閱[管理可用性虛擬機器](virtual-machines-windows-manage-availability.md)。

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>Azure 網路中的 WSFC 叢集行為

Azure 中的非 RFC 相容 DHCP 服務可能會導致失敗，因為指派重複的 IP 位址，例如相同的 IP 位址，做為其中一個叢集節點叢集網路名稱特定 WSFC 叢集設定的建立。 當您執行永遠在可用性群組，WSFC 功能而定，這是問題。

建立兩個節點叢集後上線時，請考慮案例︰

1. 叢集上線，然後 NODE1 要求叢集網路名稱的動態指派的 IP 位址。

2. 由於 DHCP 服務會辨識要求來自 NODE1，NODE1 自己的 IP 位址以外的任何 IP 位址由 DHCP 服務，提供本身。

3. Windows 會偵測到重複的地址分派 NODE1 和叢集網路名稱，無法連線的預設叢集群組。

4. 預設叢集群組會移至 NODE2 NODE1 的 IP 位址視為叢集 IP 位址，並顯示預設叢集群組線上。

5. NODE2 嘗試與 NODE1 建立連線時，在 NODE1 導向封包因為它本身解析 NODE1 的 IP 位址，永遠不會保留 NODE2。 NODE2 無法與 NODE1 建立連線]，將會遺失仲裁，並關閉 [叢集。

6. 同時，NODE1 可以封包傳送給 NODE2，但無法回覆 NODE2。 NODE1 仲裁會遺失，然後關閉 [叢集。

指派給叢集網路名稱，以顯示線上叢集網路名稱的 [未使用靜態 IP 位址，例如 169.254.1.1，例如連結本機 IP 位址，可避免這種情況。 若要簡化此程序，請參閱[設定 Windows 容錯移轉叢集 Azure 永遠上顯示狀態群組中](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)。

如需詳細資訊，請參閱[設定一律上顯示狀態群組中 Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。

### <a name="availability-group-listener-support"></a>可用群組接聽支援

執行 Windows Server 2008 R2、 Windows Server 2012、 Windows Server 2012 R2，以及 Windows Server 2016 Azure Vm 可用性群組接聽都支援。 這項支援是因為使用的是可用性] 群組中的節點 Azure Vm 上啟用的負載平衡結束點。 您必須遵循合作，以在 Azure 以及執行內部部署中執行這兩個用戶端應用程式的接聽特殊的設定步驟。

有兩個主要的選項，如需設定接聽程式︰ 外部 （公開） 或內部。 外部 （公開） 接聽使用網際網路圖示的負載平衡器，而且相關聯的公用虛擬 IP (VIP) 可在網際網路上存取。 內部接聽使用內部負載平衡器，而且只支援相同的虛擬網路中的用戶端。 其中一個負載平衡器類型，您必須先啟用直接伺服器傳回。 

如果 [可用性] 群組中包含多個 Azure 的子網路 （例如交叉 Azure 區域的部署），用戶端連線字串必須包含 「**MultisubnetFailover = True**」。 這會導致平行連線嘗試不同的子網路的複本。 如需接聽程式的設定指示，請參閱

- [設定一律上顯示狀態中的群組 Azure ILB 接聽程式](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)。
- [設定一律上顯示狀態群組 Azure 中外部接聽程式](virtual-machines-windows-classic-ps-sql-ext-listener.md)。

您還是可以連接至每個可用性複本分別直接連接到服務執行個體。 另外，由於永遠在可用性群組與資料庫鏡像用戶端回溯相容，您可以連線至可用性複本，例如，只要複本設定類似資料庫鏡像鏡像協力廠商的資料庫︰

- 一個主要複本與一個的次要複本

- 次要複本已設定為非可讀取 （**可讀取次要**選項設定為 [**否**）

對應到使用 ADO.NET 或 SQL Server Native Client 此資料庫鏡像類似設定範例用戶端連線字串是如下︰

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

如需用戶端連線的詳細資訊，請參閱︰

- [使用 SQL Server Native Client 的連接字串關鍵字](https://msdn.microsoft.com/library/ms130822.aspx)
- [用戶端連線至資料庫鏡像工作階段 (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [連線到混合式 IT 中的可用性群組接聽程式](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [可用性群組接聽、 用戶端連線和應用程式容錯移轉 (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [使用資料庫鏡像連線字串包含可用群組](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>在混合式 IT 網路延遲

您應該部署 HADR 方案，其假設可能的內部部署網路和 Azure 之間高網路延遲的時間週期。 在部署複本至 Azure 時，您應該使用，而不是同步認可非同步認可同步處理] 模式。 當部署資料庫鏡像伺服器內部部署和 Azure 中使用高效能模式，而不是高的安全模式。

### <a name="geo-replication-support"></a>地理複寫的支援

地理複寫在 Azure 磁碟不支援的資料檔案和儲存在不同的磁碟上相同的資料庫的記錄檔。 獨立和非同步 GRS 會將複製每個磁碟上的變更。 這個機制保證寫入順序中單一磁碟上的地理複寫的複本，但不是跨多個磁碟地理複寫複本。 如果您設定的資料庫，將其資料檔案和它在另一個磁碟上的記錄檔儲存之後損毀, 的復原的磁碟可能包含最新資料檔案的複本比線預先寫入記錄檔在 SQL Server 和 ACID 屬性的交易記錄檔。 如果您沒有停用儲存帳戶的地理複寫選項，您應該保留所有資料和記錄檔為指定的資料庫相同的磁碟上。 如果您必須使用多個磁碟資料庫的大小，必須先部署損毀修復解決方案，以確保資料重複上述其中一項。

## <a name="next-steps"></a>後續步驟

如果您需要建立 SQL Server Azure 虛擬機器，請參閱[佈建 Azure SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

最佳效能，從 SQL Server Azure VM 上執行，請參閱中[的 SQL Server Azure 虛擬機器中的商務連絡人的效能最佳作法](virtual-machines-windows-sql-performance.md)的指引。

執行 SQL Server Azure Vm 中其他主題，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。

### <a name="other-resources"></a>其他資源

- [安裝新的 Active Directory 樹系 Azure 中](../active-directory/active-directory-new-forest-virtual-machine.md)
- [建立 WSFC 叢集永遠 Azure VM 中的可用性群組](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)
