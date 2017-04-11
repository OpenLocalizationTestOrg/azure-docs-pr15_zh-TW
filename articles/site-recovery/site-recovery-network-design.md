<properties
    pageTitle="設計您的網路基礎結構的損毀修復 |Microsoft Azure"
    description="本文將討論 Azure 網站復原的網路設計考量"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>設計損毀修復您的網路基礎結構

本文會導向至 IT 專業人員負責架構，實作，並支援業務連續性和損壞修復 (BCDR) 基礎結構，而且想要運用 Microsoft Azure 網站修復 (ASR) 至支援加強其 BCDR 服務。 本文將討論的系統管理中心虛擬機器管理員伺服器部署、 優缺點延伸子網路的子網路容錯移轉時，與實際的考量，以及如何結構損毀修復，在 Microsoft Azure 虛擬網站。

## <a name="overview"></a>概觀

[Azure 網站修復 (ASR)](https://azure.microsoft.com/services/site-recovery/)是協調保護與修復您的業務連續性損毀修復 (BCDR) 用途的虛擬應用程式的 Microsoft Azure 服務。 這份文件的目的是引導讀者完成程序的設計網路，將焦點放在架構 IP 範圍和子網路損毀復原網站上，使用網站復原的虛擬機器 (Vm) 廖。

此外，本文將示範網站復原可讓架構與實作多站台的虛擬資料中心，以測試或損毀的每次支援 BCDR 服務。

在何處，都是 24/7 連線世界，則更重要比以往基礎結構和應用程式，而且正在執行。 業務連續性與損毀復原 (BCDR) 的目的是讓組織可以快速地繼續正常運作，還原失敗的元件。 開發損毀修復策略處理不太、 毀滅性事件是很容易。 這是因為固有困難預測未來，特別是相關 improbable 事件，以及提供適當的量值，防範深遠無知高成本。 

重要 BCDR 規劃，修復時間目標 (RTO) 並修復點目標 (RPO) 必須定義損毀復原計劃的一部分。 當損毀，可達到客戶的資料中心，快速地使用 Azure 網站修復，客戶可以 (低 RTO) 連線的最小值的資料遺失 (低 RPO) 位於 [次要資料中心] 或 [Microsoft Azure 其複寫的虛擬機器。 

容錯移轉是因為 ASR 最初複製指定的虛擬機器從主要的資料中心次要資料中心或 Azure （根據狀況），並定期更新複本。 規劃基礎結構，網路設計應視為可能造成您的會議公司 RTO 和 RPO 目標的瓶頸。  

系統管理員計劃部署損壞修復解決方案，其中一個脫離金鑰的問題時如何容錯移轉完成後虛擬機器就會連線到。 ASR 可讓您選擇的虛擬機器會連線到後移轉後的網路系統管理員。 如果這用來達成網路對應然後 VMM 伺服器會管理主網站。 如需詳細資訊，請參閱[網路對應的準備](site-recovery-network-mapping.md)。

設計復原網站網路，時系統管理員具有兩個選擇︰

- 使用不同的 IP 位址範圍復原網站的網路。 在這個案例中虛擬機器後移轉後會收到新的 IP 位址，管理員就必須執行 DNS 更新。 閱讀更多有關如何執行 DNS 更新[以下](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- 復原網站的網路使用相同的 IP 位址範圍。 在某些情況下管理員偏好保留在主要的網站有更後移轉後的 IP 位址。 在標準的情況下系統管理員必須更新路由表示的 IP 位址的新位置。 但在案例中延伸虛擬區域網路之間的主要及復原網站的部署位置，保留 IP 位址的虛擬機器變成美觀的選項。 保留相同的 IP 位址簡化修復程序離開採取任何網路相關文章容錯移轉步驟。


在系統管理員規劃部署損壞修復解決方案時，其記住金鑰的問題是如何應用程式會連線到容錯移轉完成後。 新的應用程式與幾乎都某些時候，因此實際移動到另一個網站的服務代表網路的挑戰的網路連線而定。 有兩種主要方式，此問題會損毀修復解決方案內處理完畢。 若要維持固定的 IP 位址是第一種方法。 儘管存在服務移動和在不同的實體位置主控伺服器] 應用程式會需要新的位置與他們的 IP 位址設定。 第二個方法涉及完全變更轉換到網站中復原時的 [IP 位址。 每一種方法都有數種實作變化的彙總] 下方。

設計復原網站網路，時系統管理員具有兩個選擇︰

## <a name="option-1-retain-ip-addresses"></a>選項 1︰ 保留 IP 位址 

從損毀的修復程序觀點來使用固定的 IP 位址似乎實作，最簡單的方法，但它有一些可能的挑戰，可在練習中最常用的方法。 Azure 網站復原提供這項功能可以保留所有的案例中的 IP 位址。 其中一個決定要保留 IP 之前，應該它呈現的容錯移轉功能的條件約束指定適當的想法。 讓我們看看的因素，可協助您決定要或不保留的 IP 位址。 這可以達成兩種方式，使用延伸子網路或執行完整的子網路容錯移轉。

### <a name="stretched-subnet"></a>延伸子網路

以下子網路可同時在主要與 DR 位置。 簡單來說，這表示您可以將伺服器和其 IP (圖層 3) 設定移到第二個和網路會將流量路由傳送至新位置自動。 這是從伺服器的觀點來處理一般，但它有數字的挑戰︰

- 從 [階層 2 （資料連結層） 觀點來看，需要管理延伸的名為的網路設備，但這有變小問題現在已經廣泛用於。 第二個和更加困難問題是失敗的縮放虛擬區域網路可能的錯誤網域延伸至這兩個網站，基本上成為單一。 雖然這是太可能發生時，它會發生廣播的大量啟動，但無法隔離。 我們找到有關這個問題的混合式的意見，而且看到許多成功實作以及 「 我們不會實作這種技術 」。
- 如果您使用的 Microsoft Azure DR 網站，延伸子網路無法。


### <a name="subnet-failover"></a>子網路容錯移轉

實作以發揮延伸子網路解決方案上述不伸展跨多個網站的子網路的子網路容錯移轉可能是。 以下任何指定子網路就會出現在網站 1 或網站 2，但不是在這兩個網站同時。 若要維持 IP 位址空間容錯移轉時，可能是以程式設計方式排列路由器基礎結構，以便將子網路移到另一個網站。 在子網路想要移動的相關聯的容錯移轉案例保護 Vm。 這種方法的主要缺點發生失敗，您必須將整個子網路，這可能是 [確定]，但可能會影響容錯移轉資料粒度考量。 

讓我們逐行查看如何為 「 contoso 」 虛構企業的能夠將其 Vm 複寫整個子網路上失敗時復原位置。 我們會先查看如何 Contoso 是可以管理其子網路，而兩個之間複製 Vm 內部部署的位置，然後將討論子網路容錯移轉如何運作時[Azure 會用來作為損毀復原網站](#failover-to-azure)。

#### <a name="failover-to-a-secondary-on-premises-site"></a>在第二個內部部署網站的容錯移轉

讓我們看看情況我們要保留的每個 Vm IP 和容錯移轉完成子網路放在一起的位置。 主要網站有子網路 192.168.1.0/24 中執行應用程式。 發生錯誤後移轉種情況，屬於此子網路的所有虛擬機器會在無法復原網站，並保留它們的 IP 位址。 路由有適當修改，以反映的所有虛擬機器屬於子網路 192.168.1.0/24 現在都移至復原網站。 

下列圖例中主網站並修復網站、 第三個網站和主網站和第三個網站和復原網站之間的路由會有適當的修正。 

從下列圖片會顯示容錯移轉前的子網路。 子網路 192.168.0.1/24 作用中之前容錯移轉的主要網站上，而後移轉後會變成作用中的 [復原網站 

![容錯移轉之前](./media/site-recovery-network-design/network-design2.png)

容錯移轉之前


下圖顯示後移轉後的網路和子網路。
    
![後移轉後](./media/site-recovery-network-design/network-design3.png)

後移轉後

在您的次要網站是內部部署管理它是 VMM 伺服器然後 ASR 當啟用保護特定的虛擬機器時，會配置根據下列工作流程的網路資源︰

- ASR 從每個系統管理中心 VMM 執行個體相關的網路上定義的靜態 IP 位址集區中每個網路介面虛擬機器上配置 IP 位址。
- 如果系統管理員所定義網路的同一 IP 位址集區復原網站的 IP 位址集區的主要的網站上的網路上時配置複本虛擬機器 ASR 的 IP 位址會配置為主要的虛擬機器相同的 IP 位址。  IP VMM 中保留，但未設定為容錯移轉 IP。 容錯移轉 IP 設定之前容錯移轉。

![保留 IP 位址](./media/site-recovery-network-design/network-design4.png)
    
圖 5

圖 5 顯示複本虛擬機器的容錯移轉 TCP/IP 設定 （在 HYPER-V 主控台）。 想要填入這些設定，虛擬機器後移轉後開始之前

如果無法使用相同的 IP，ASR 會從已定義的 IP 位址集區配置一些其他可用的 IP 位址。 

保護啟用 VM 後您可以使用下列範例指令碼來確認已配置虛擬機器的 IP。 相同的 IP 會設定為容錯移轉 IP 並指派的 vm 容錯移轉時︰

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] 在虛擬機器位置使用 DHCP 案例中，管理的 IP 位址是完全控制的 ASR。 若要確保 DHCP 伺服器做復原網站上的 IP 位址可做從相同的範圍，為主要網站的管理員。

#### <a name="failover-to-azure"></a>Azure 容錯移轉

Azure 網站修復 (ASR) 可讓 Microsoft Azure 成為您的虛擬機器損毀復原網站。 在此情況下，您必須使用處理更多的限制式。 

讓我們逐行查看其中虛構公司名稱為 Woodgrove 銀行有裝載商務應用程式，其線條的內部部署基礎結構的案例，他們會裝載上 Azure 其行動應用程式。 以網站為網站 (S2S) 虛擬私人網路 (VPN) 提供之間 Woodgrove 銀行 Vm Azure 及內部部署伺服器的連線。 S2S 要有 vpn 才能看到 Woodgrove 銀行內部網路的副檔名為 Azure 中允許 Woodgrove 銀行虛擬網路。 S2S 要有 vpn 才能啟用此通訊 Woodgrove 銀行邊緣與 Azure 虛擬網路之間。 現在 Woodgrove 想要使用 ASR 複寫 Azure 中執行中的資料中心的工作量。 這個選項符合 Woodgrove 想經濟 DR 選項，並可在公用雲端環境中儲存資料的需求。 Woodgrove 處理應用程式與設定的硬式編碼 IP 位址而定，因此有要求之後無法透過 Azure 保留其應用程式的 IP 位址。

從 IP 位址範圍 （172.16.1.0/24、 172.16.2.0/24） 指派的 IP 位址已決定 Woodgrove Azure 中執行的資源。

若要能夠將其虛擬機器複寫到 Azure，但保留 IP 位址 Woodgrove，Azure 虛擬網路需要建立。 讓應用程式可以從內部部署網站容錯移轉至 Azure 完美應內部網路的副檔名。 Azure 可讓您新增至網站，以及點為網站 VPN 連線到虛擬 Azure 中建立的網路。 設定您的網站的連線，Azure 的網路可讓您的 IP 位址範圍是從內部部署 IP 位址範圍不同，因為 Azure 不支援自動縮放子網路時，才，將流量路由傳送至內部部署的位置 （Azure 會將其本機網路）。  這表示，如果您有子網路 192.168.1.0/24 內部部署，您無法新增本機網路 192.168.1.0/24 Azure 網路中。 這是因為 Azure 不知道子網路中有沒有作用中的 Vm 並建立子網路僅適用於 DR 用途可預期。 若要能夠正確網路將流量路由傳送 Azure 的網路不在 [網路和本機網路的子網路必須發生衝突。 

![子網路容錯移轉之前](./media/site-recovery-network-design/network-design7.png)

容錯移轉之前

若要協助 Woodgrove 滿足其商務需求，我們需要執行下列工作流程︰

- 建立其他的網路，請讓我們呼叫復原網路，就會建立無法透過虛擬機器。
- 若要確保 vm IP，保留後移轉後，請移至在 VM 內容] 下的 [設定] 索引標籤指定相同的 IP VM 已於內部部署，然後再按一下 [儲存。 當 VM 移轉時，Azure 網站復原會指派提供的 IP 虛擬機器。 

![網路內容](./media/site-recovery-network-design/network-design8.png)

觸發容錯移轉並修復網路中的所需的 IP 建立虛擬機器之後，就可以使用[Vnet 到 Vnet 連線](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)來建立此網路的連線。 如有必要這個動作可以建立指令碼。  如前述前一節中瞭解子網路容錯移轉，即使容錯移轉至 Azure，而有適當的修正以反映該 192.168.1.0/24 現在已移至 Azure。 

![後子網路移轉後](./media/site-recovery-network-design/network-design9.png)

後移轉後

如果您沒有 [Azure 網路] 上方的圖片所示。 您可以建立 「 主網站 」 和 「 復原網路 」 之間網站 vpn 連線後移轉後。  


## <a name="option-2-changing-ip-addresses"></a>選項 2︰ 變更 IP 位址

此方法似乎是最常見根據我們看到的內容。 需要變更每個 VM 容錯移轉中的 IP 位址的表單。 這種方法的缺點需要 「 學習 ' IPx 在應用程式現在位於 IPy 內送的網路。 即使 IPx 和 IPy 邏輯的名稱，將變更或清除整個網路，通常有 DNS 項目和網路表格中的快取項目有更新或清除，因此停機時間可能會看到如何 DNS 基礎結構已設定而定。 使用低 TTL] 值在內部網路應用程式的情況下，使用[Azure 流量管理員 ASR 以](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)網際網路為基礎的應用程式，可以減輕這些問題

### <a name="changing-the-ip-addresses---illustration"></a>變更 IP 位址的圖例

讓我們看看案例您打算主要及復原網站間全面使用不同的 Ip。 在下列範例中我們也有第三個網站的應用程式裝載於主要或復原的位置可以存取網站。

![不同的 IP-容錯移轉之前](./media/site-recovery-network-design/network-design10.png)

圖 11

圖 11 有裝載於在主要的網站上的 [子網路 192.168.1.0/24 子網路部分應用程式，有更多，以在子網路 172.16.1.0/24 復原網站上後移轉後設定。 VPN 連線或網路路由已設定妥當之後，好讓所有的三個網站可以存取彼此。
 
為圖表 12 所顯示，容錯移轉一或多個應用程式之後，他們可以還原復原子網路中。 在此情況下我們不一定要容錯移轉整個子網路一次。 不必須重新設定 VPN 或網路路由任何變更。 容錯移轉與一些 DNS 更新會確定應用程式仍然可以存取。 如果設定為允許動態更新 DNS 然後虛擬機器想註冊後移轉後開始使用新的 IP。 

![不同的 IP-後移轉後](./media/site-recovery-network-design/network-design11.png)

圖 12

失敗暫留在上之後複本虛擬機器可能不相同，主要的虛擬機器的 IP 位址的 IP 位址。 虛擬機器時都會更新他們使用 DNS 伺服器開始後。 若要變更或清除整個網路，通常有 DNS 項目和快取的項目網路表格中有更新或清除，很少發生下列狀態變更時，會面對停機時間。 可以減輕此問題︰

- 用於內部網路應用程式中的低 [TTL] 值。
- Azure 流量管理員使用的網際網路 ASR 依據應用程式。
- 使用下列指令碼修復計劃中的更新 DNS 伺服器，以確保及時的更新 （指令碼不需要如果動態 DNS 登錄設定）

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>變更 IP 位址 – DR 至 Azure

[Microsoft Azure 損毀復原網站的網路基礎結構安裝](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)部落格文章說明如何設定必要的 Azure 網路基礎結構時保留 IP 位址無法要求。 它第一句是描述應用程式，然後查看如何設定內部網路和 Azure 及如何進行測試容錯移轉與計劃的容錯移轉的。



## <a name="next-steps"></a>後續步驟

[瞭解](site-recovery-network-mapping.md)如何用於網站復原地圖來源及目標網路時 VMM 伺服器管理主網站。 
