<properties
    pageTitle="保護 Active Directory 和 Azure 網站復原 DNS |Microsoft Azure"
    description="本文將說明如何使用 Azure 網站復原的 Active Directory 實作損壞修復解決方案。"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>保護 Active Directory 和 Azure 網站復原 DNS

企業應用程式，例如 SharePoint、 Dynamics AX 和 SAP 取決於 Active Directory 和 DNS 基礎結構正常運作。 當您建立損壞修復解決方案的應用程式時，請務必記住您需要保護及其他應用程式元件，以確保項目正常運作時發生損毀之前復原 Active Directory 和 DNS。

網站修復是藉由損毀修復來協調複寫、 錯誤移轉及復原的虛擬機器 Azure 服務。 網站復原支援複寫案例一致的方式保護，並流暢地容錯移轉虛擬機器及私人]、 [公用] 或 [主雲朵應用程式。

使用網站復原，您可以建立一個完整的自動化損毀修復計劃適用於 Active Directory。 中斷發生時，您可以從任何地方數秒內啟動容錯移轉，並在幾分鐘取得 Active Directory 和使用。 如果您已在您主要的網站中的多個應用程式，例如 SharePoint 及 SAP 部署 Active Directory 和您想要完整的網站上失敗，您可能會失敗透過 Active Directory 第一次使用網站復原，然後再透過使用特定應用程式修復計劃的其他應用程式。

本文說明如何建立損壞修復解決方案的 Active Directory、 如何執行計劃、 尚未計劃，以及使用單鍵復原計劃、 支援的設定和先決條件測試容錯移轉。  在您開始之前，您應該熟悉 Active Directory 和 Azure 網站復原。

有兩個建議的選項，根據您的環境的複雜性。

### <a name="option-1"></a>選項 1

如果您有幾個應用程式與單一網域控制站，且您想要在整個網站失敗，我們建議使用網站復原複寫網域控制站次要網站 （是否您無法透過 Azure 或次要網站）。 相同的複寫的虛擬機器可用於測試容錯移轉太。

### <a name="option-2"></a>選項 2

如果您有大量的應用程式，在環境中，有一個以上的網域控制站或如果您打算一次容錯幾個應用程式，我們建議除了複寫網域控制站虛擬機器網站復原您也會設定 （Azure 或第二個內部部署資料中心） 目標網站上的其他網域。

>[AZURE.NOTE] 即使您正在執行您仍會需要複寫網域控制站使用網站修復測試容錯移轉實作選項 2。 如需詳細資訊的 [[測試容錯移轉考量](#considerations-for-test-failover)，請閱讀。


下列各節說明如何啟用網站復原的網域控制站的保護，以及如何設定網域控制站 Azure 中。


## <a name="prerequisites"></a>必要條件

- 在內部部署的 Active Directory 和 DNS 伺服器。
- Microsoft Azure 訂閱中 Azure 網站修復服務保存庫。
- 如果您 Azure 隨複製 Azure 虛擬機器整備評估工具，以確保 Vm 上的就會與 Azure Vm 和 Azure 網站修復服務相容。


## <a name="enable-protection-using-site-recovery"></a>啟用使用網站復原的保護


### <a name="protect-the-virtual-machine"></a>保護虛擬機器

啟用網域控制站/DNS 虛擬機器中網站復原的保護。 設定網站復原根據虛擬機器類型 （HYPER-V 或 VMware） 的 [設定]。 我們建議您 15 分鐘當機一致的複寫的頻率。

###<a name="configure-virtual-machine-network-settings"></a>虛擬機器網路設定

網域控制站/DNS 虛擬機器中，使 VM 會附加至正確的網路後移轉後，在網站復原設定網路設定。 例如，如果您複製 HYPER-V Vm 至 Azure 您可以選取 VM VMM 雲端或設定網路設定，如下所示的 [保護] 群組中

![VM 網路設定](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>使用 Active Directory 複寫保護 Active Directory

### <a name="site-to-site-protection"></a>網站-保護

建立網域控制站的次要網站上，指定當您將網域控制站的伺服器時所使用的主要的網站上相同的網域的名稱。 若要加入網站的網站連結物件上的設定，您可以使用**Active Directory 網站和服務**嵌入式管理單元。 藉由設定的網站連結的設定，您可以控制複寫發生兩個或多個網站之間的時間和頻率。 如需詳細資訊，請參閱[排程網站複寫](https://technet.microsoft.com/library/cc731862.aspx)。

###<a name="site-to-azure-protection"></a>網站-Azure 保護

請依照下列指示[建立 Azure 虛擬網路中的網域](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)。 當您將網域控制站伺服器指定相同的主要的網站使用的網域名稱。

然後[重新設定 DNS 伺服器的虛擬網路](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)，Azure 中使用的 DNS 伺服器。

![Azure 網路](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>測試容錯移轉考量

具有從生產網路隔離，以便有不會影響實際執行工作負載的網路中發生的測試容錯移轉。

大部分的應用程式也需要的網域和 DNS 伺服器功能，目前狀態，因此網域控制站的應用程式無法透過之前，必須建立要用於測試容錯移轉隔離的網路中。 執行此動作的最簡單方法是啟用網站復原網域控制站/DNS 虛擬機器上的保護並執行測試的容錯移轉應用程式的復原方案之前，先執行的虛擬機器，測試容錯移轉。 以下是如何執行的動作︰

1. 啟用網站修復網域控制站/DNS 虛擬機器中的保護。
2. 建立獨立的網路。 與其他網路隔離預設 Azure 中建立任何虛擬網路。 我們建議您為此網路的 IP 位址範圍是以生產網路的相同。 沒有啟用這個網路上的網站的連線。
3. 提供建立，為您的預期 DNS 虛擬機器取得 IP 位址的網路中的 DNS IP 位址。 如果您複製至 Azure，然後用來在 VM 屬性中的**目標 IP**設定的容錯移轉 vm 提供的 IP 位址。 如果您複製到另一個內部部署網站，並使用 DHCP 依照指示[設定 DNS 及 DHCP 測試容錯移轉](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] IP 位址是用於測試容錯移轉網路時，它會在計畫之內的容錯移轉，取得的 IP 位址相同配置虛擬機器測試故障時的 IP 位址。 如果沒有，虛擬機器會收到不同的 IP 位址可供測試容錯移轉網路中。

4. 網域控制站虛擬機器上執行的測試容錯移轉隔離的網路中。 使用最新可用的應用程式一致的復原點網域控制站虛擬機器的測試容錯移轉。 
5. 執行測試容錯移轉應用程式修復計劃。
6. 測試完成之後，標示網域控制站虛擬機器及復原計劃 「 完成 」 網站復原入口網站中的 [**工作**] 索引標籤的測試容錯移轉工作。

### <a name="dns-and-domain-controller-on-different-machines"></a>在不同的電腦上的 DNS 與網域控制站

如果 [DNS 不在相同的網域控制站的虛擬機器必須建立 DNS VM 測試容錯移轉。 如果他們是相同 VM 上，您可以略過此節。

您可以使用全新的 DNS 伺服器，並建立所需的區域。 例如，contoso.com Active Directory 網域時，您可以建立 DNS 區域的名稱 contoso.com。 對應到 Active Directory 的項目必須更新在 [DNS]，如下所示︰

1. 確定這些設定就地之前復原計劃中的任何其他虛擬機器出現於︰

    - 樹系根名稱之後，必須命名區域。
    - 區域必須是支援的檔案。
    - 區域必須啟用安全與非安全更新。
    - 網域控制站虛擬機器解析指向 DNS 虛擬機器的 IP 位址。

2. 網域控制站虛擬機器上，執行下列命令︰

    `nltest /dsregdns`

3. 新增的 DNS 伺服器上的區域、 讓不安全的更新，並為其新增項目 dns:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>後續步驟

閱讀[可以保護哪些負載？](../site-recovery/site-recovery-workload.md)若要深入瞭解保護企業負載 Azure 網站復原。
