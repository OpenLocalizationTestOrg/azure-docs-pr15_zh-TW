<properties 
   pageTitle="DNS 名稱解析] 選項的 Linux Vm Azure 中"
   description="名稱解析案例中 Azure IaaS，包括 Linux Vm 提供 DNS 服務、 混合式外部的 DNS 並將您自己的 DNS 伺服器。"
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Linux Vm Azure 中的 DNS 名稱解析] 選項

Azure 包含在單一虛擬網路中的所有 Vm 預設都提供 DNS 名稱解析。 您就可以藉由您 Azure 主控 Vm 中設定您自己的 DNS 服務實作您自己的 DNS 名稱解析解決方案。 下列情況應能助您選擇哪一個更適合您的情況。

- [Azure 所提供的名稱解析](#azure-provided-name-resolution)

- [使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server) 

您使用的名稱解析的類型而定如何您 Vm 和角色執行個體需要彼此。

**下表說明案例和對應的名稱解析方案︰**

| **案例** | **解決方案** | **後置字元** |
|--------------|--------------|----------|
| 角色執行個體或 Vm 位於相同的虛擬網路之間的名稱解析 | [Azure 所提供的名稱解析](#azure-provided-name-resolution)| 主機名稱或 FQDN |
| 角色執行個體或 Vm 位於不同的虛擬網路之間的名稱解析 | 客戶管理 DNS 伺服器轉接之間 vnets 解析 Azure (DNS proxy) 的查詢。  請參閱[使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)| 僅限 FQDN |
| 從角色執行個體或 Vm Azure 中的內部部署電腦和服務名稱解析 | 客戶管理 DNS 伺服器 （例如，內部部署網域控制站、 本機唯讀網域控制站或 DNS 次要同步處理使用區域轉送）。  請參閱[使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)|僅限 FQDN |
| Azure hostname 從內部部署電腦上的解析度 | 轉寄查詢中對應的 vnet 客戶管理 DNS proxy 伺服器 proxy 伺服器送給查詢 Azure 的解析度。 請參閱[使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)| 僅限 FQDN |
| 反向 DNS 內部 IPs | [使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server) | n/a |

## <a name="azure-provided-name-resolution"></a>Azure 所提供的名稱解析

公用 DNS 名稱解析，以及 Azure 提供內部名稱解析 Vm 和相同的虛擬網路中的角色執行個體。  在 arm 虛擬網路 DNS 尾碼是一致的虛擬網路上 （因此不需要 FQDN），Nic 和 Vm，可以指定 DNS 名稱。 雖然 Azure 所提供的名稱解析時，不需要任何設定，不適當的選項，所有的部署案例中，如上述資料表上所示。

### <a name="features-and-considerations"></a>功能及考量

**功能︰**

- 使用方便性︰ 任何設定，才能使用 Azure 所提供的名稱解析]。

- 高度使用，儲存您的需求來建立和管理您自己的 DNS 伺服器叢集 Azure 所提供的名稱解析服務。

- 可以使用您自己的 DNS 伺服器以解決內部部署和 Azure hostname。

- 在虛擬網路中不需要使用 FQDN Vm 之間提供名稱解析。 

- 您可以使用 [主機名稱描述您的部署，而不是使用自動產生的名稱。

**考量︰**

- 無法修改 Azure 建立的尾碼。

- 您無法手動註冊您自己的記錄。

- 不支援獲勝和 NetBIOS。

- 主機名稱必須是 DNS 相容 (他們必須使用只 0-9 a 到 z 和 '-」，並無法開頭或結尾 '-'。 請參閱 RFC 3696 第 2 節）。

- DNS 查詢的流量會針對每個 VM 經流速控制。 這不應影響大部分的應用程式。  如果觀察要求節流，確定已啟用 [用戶端快取。  如需詳細資訊，請參閱[從 Azure 所提供的名稱解析最](#Getting-the-most-from-Azure-provided-name-resolution)。


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>從 Azure 所提供的名稱解析最取得
**用戶端快取︰**

並非每個 DNS 查詢會傳送在網路上。  用戶端快取，可協助減少延遲，以解決週期性 DNS 查詢從本機快取提升網路音效恢復功能。  DNS 記錄包含存留時間 (TTL) 可讓儲存為久的記錄，而不會影響記錄的有效期限快取。  因此，用戶端快取是適用於大部分的情況。

某些 Linux distros 不包含預設的快取。  建議您加入至每個 Linux VM （後，還沒有本機快取的檢查）。

有數種不同 DNS 快取的套件可用，例如 dnsmasq，以下是最常見的 distros 上安裝 dnsmasq 的步驟︰

- **Ubuntu （使用 resolvconf）**︰
    - 安裝 dnsmasq 套件 (「 sudo 引起取得安裝 dnsmasq 」)。
- **SUSE （使用 netconf）**︰
    - 安裝 dnsmasq 套件 (「 sudo zypper 安裝 dnsmasq 」) 
    - 啟用 dnsmasq 服務 (「 systemctl 啟用 dnsmasq.service 」) 
    - 啟動 dnsmasq 服務 (「 systemctl 開始 dnsmasq.service 」) 
    - 編輯 「 / 等/sysconfig/網路/設定 」 變更 NETCONFIG_DNS_FORWARDER = 」 」 以 「 dnsmasq 」
    - 為本機 DNS 解析程式更新為快取 resolv.conf （「 netconfig 更新 」）
- **OpenLogic （使用 NetworkManager）**︰
    - 安裝 dnsmasq 套件 (「 sudo yum 安裝 dnsmasq 」)
    - 啟用 dnsmasq 服務 (「 systemctl 啟用 dnsmasq.service 」)
    - 啟動 dnsmasq 服務 (「 systemctl 開始 dnsmasq.service 」)
    - 新增 「 在前面加上網域名稱伺服器 127.0.0.1; 」 以 「 /etc/dhclient-eth0.conf 」
    - 重新啟動網路服務 （「 服務網路重新啟動 」） 若要將本機 DNS 解析程式快取設定

> [AZURE.NOTE]: 'Dnsmasq' 封裝是其中一個可用的 Linux 許多 DNS 快取。  使用，之前，先檢查其是否符合您特定需求及已安裝其他的快取。

**用戶端重試︰**

DNS 是主要 UDP 通訊協定。  為 UDP 通訊協定不保證郵件傳遞，重試邏輯被處理 DNS 通訊協定本身。  每個 DNS 用戶端 （作業系統） 可以呈現不同的重試邏輯根據的建立者無法看見喜好設定︰

 - Windows 作業系統後重試之後第二個和再選一次其他兩個四與另一個四秒數。 
 - 五秒後預設 Linux 設定重。  您應該變更這五個時間間隔一秒再試一次。  

若要查看上 Linux VM，「 貓 /etc/resolv.conf' 及外觀，在 [選項] 列中的目前設定，例如所示︰

    options timeout:1 attempts:5

Resolv.conf 檔案自動產生，而不應加以編輯。  依 distro，而定，新增 [選項] 列的特定步驟︰

- **Ubuntu**（使用 resolvconf）︰
    - 新增要的選項行 ' / etc/resolveconf/resolv.conf.d/head 」 
    - 執行 「 resolvconf u' 更新
- **SUSE**（使用 netconf）︰
    - 新增 NETCONFIG_DNS_RESOLVER_OPTIONS ' timeout:1 嘗試︰ 5 「 = 」 」 參數的 '/ 等/sysconfig/網路/config' 
    - 執行 「 netconfig 更新 」 更新
- **OpenLogic**（使用 NetworkManager）︰
    - 新增 「 回應 」 選項 timeout:1 嘗試︰ 5 」' ' / etc/NetworkManager/dispatcher.d/11-dhclient 」 
    - 執行 「 服務網路重新啟動 」 更新

## <a name="name-resolution-using-your-own-dns-server"></a>使用您自己的 DNS 伺服器的名稱解析
有數種情況下，您的名稱解析需求可能會移至何處之外的功能提供的 Azure，例如當您需要 DNS 解析虛擬網路 (vnets) 之間。  若要涵蓋這種情況下，Azure 會提供的功能，您可以使用您自己的 DNS 伺服器。  

虛擬網路中的 DNS 伺服器可以轉寄給 Azure 的遞迴解析程式，來解決 hostname 的虛擬網路中的 DNS 查詢。  比方說，Azure 中執行的 DNS 伺服器可以回應 DNS 查詢，以自己的 DNS 區域檔案，並將所有其他查詢轉寄給 Azure。  這個選項可讓 Vm 與檢視兩個區域的檔案與提供 Azure hostname （透過轉寄） 項目。  透過虛擬 IP 168.63.129.16 提供 Azure 的遞迴解析程式存取。

DNS 轉寄也可讓內部 vnet DNS 解析，並允許以解決提供 Azure hostname 您內部部署電腦。  若要解決 VM 的主機名稱，DNS 伺服器 VM 必須位於相同的虛擬網路，並設定為 Azure 轉寄 hostname 查詢。  每個 vnet 的不同 DNS 尾碼現狀，您可以使用條件轉寄規則 DNS 查詢傳送到正確的 vnet 的解析度。  下圖顯示兩個 vnets 及執行使用這個方法間 vnet DNS 解析內部部署網路︰

![間 vnet DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

使用 Azure 所提供的名稱解析時, 的內部 DNS 尾碼提供給每個 VM 使用 DHCP。  在使用您自己的名稱解析解決方案時，不是提供這個後置字元給 Vm 因為它會影響其他 DNS 架構提供。  若要在電腦上執行的 FQDN，請參閱或設定您的 Vm 後置字元，就可以使用 PowerShell 或 API 來決定後置字元︰

-  Azure 資源管理管理 vnets、 尾碼是可透過[網路介面卡](https://msdn.microsoft.com/library/azure/mt163668.aspx)資源，或您可以執行命令`azure network public-ip show <resource group> <pip name>`以顯示您的公用 IP，包括在 nic 的 FQDN 的詳細資料    


如果查詢送給 Azure 不符合您的需求，您需要提供您自己的 DNS 解決方案。  您的 DNS 解決方案需要︰

-  提供適當的主機名稱解析，例如透過[DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md)。  請注意，如果使用的 DDNS 您可能需要停用 DNS 記錄清除 Azure 的 DHCP 租用很長及清除所可能會永久移除的 DNS 記錄。 
-  提供適當的遞迴解析允許外部網域名稱的解析度。
-  無障礙 （TCP 和 UDP 連接埠 53） 從其所做的用戶端，可以存取網際網路。
-  受到保護，針對存取從網際網路]，以減少威脅外部代理程式。

> [AZURE.NOTE] 為獲得最佳效能，IPv6 時使用的 DNS 伺服器 Azure Vm，應該停用，並[執行個體層級的公用 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)應該要指派給每個 DNS 伺服器 VM。  

