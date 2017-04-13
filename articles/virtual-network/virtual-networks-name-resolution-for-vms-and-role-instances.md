<properties 
   pageTitle="解決方法 Vm 和角色執行個體"
   description="Azure IaaS，混合式解決方案，之間不同的雲端服務、 Active Directory 和使用您自己的 DNS 伺服器名稱解析案例 "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Vm 和角色執行個體名稱解析

根據您如何使用 Azure 裝載 IaaS、 PaaS，以及混合式解決方案，您可能需要允許 Vm 和建立彼此的角色執行個體。 雖然這種通訊，就可以完成使用的 IP 位址是更容易使用的名稱，可以很容易記住，不會變更。 

當角色執行個體和 Vm 裝載於 Azure 需要將網域名稱解析內部 IP 位址時，他們可以使用兩種方法之一︰

- [Azure 所提供的名稱解析](#azure-provided-name-resolution)

- [使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)（這可能查詢轉寄至的 Azure 所提供的 DNS 伺服器） 

您使用的名稱解析的類型而定如何您 Vm 和角色執行個體需要彼此。

**下表說明案例和對應的名稱解析方案︰**

| **案例** | **解決方案** | **後置字元** |
|--------------|--------------|----------|
| 角色執行個體或 Vm 位於相同的雲端服務或虛擬網路之間的名稱解析 | [Azure 所提供的名稱解析](#azure-provided-name-resolution)| 主機名稱或 FQDN |
| 角色執行個體或 Vm 位於不同的虛擬網路之間的名稱解析 | 客戶管理 DNS 伺服器轉接之間 vnets 解析 Azure (DNS proxy) 的查詢。  請參閱[使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)| 僅限 FQDN |
| 從角色執行個體或 Vm Azure 中的內部部署電腦和服務名稱解析 | 客戶管理 DNS 伺服器 （例如內部部署網域控制站、 本機唯讀的網域或 DNS 次要同步處理使用區域轉送）。  請參閱[使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)|僅限 FQDN |
| Azure hostname 從內部部署電腦上的解析度 | 轉寄查詢中對應的 vnet 客戶管理 DNS proxy 伺服器 proxy 伺服器送給查詢 Azure 的解析度。 請參閱[使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)| 僅限 FQDN |
| 反向 DNS 內部 IPs | [使用您自己的 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server) | n/a |
| 之間 Vm 或位於不同的雲端服務，不在虛擬網路中的角色執行個體名稱解析| 不適用。 外部虛擬的網路不支援 Vm 與不同的雲端服務中的角色例項之間的連線。| n/a |



## <a name="azure-provided-name-resolution"></a>Azure 所提供的名稱解析

公用 DNS 名稱解析，以及 Azure 提供內部名稱解析 Vm 和位於相同的虛擬網路或雲端服務中的角色執行個體。  雲端服務中的 Vm/執行個體共用相同的尾碼 （以便主機名稱時還大不足） 但傳統的虛擬網路不同雲端服務讓不同 DNS 尾碼 FQDN 需要解決之間不同的雲端服務的名稱。  在虛擬資源管理員部署模型中的網路，DNS 尾碼是一致的虛擬網路上 （因此不需要 FQDN），Nic 和 Vm，可以指定 DNS 名稱。 雖然 Azure 所提供的名稱解析時，不需要任何設定，但不適當的選項，所有的部署案例中，如上述資料表上所示。

> [AZURE.NOTE] 若是網頁和工作者角色，您也可以存取角色執行個體根據角色名稱] 和 [執行個體數目使用 Azure 服務管理 REST API 內部的 IP 的位址。 如需詳細資訊，請參閱[服務管理 REST API 參考](https://msdn.microsoft.com/library/azure/ee460799.aspx)。

### <a name="features-and-considerations"></a>功能及考量

**功能︰**

- 使用方便性︰ 不需要設定，才能使用 Azure 所提供的名稱解析]。

- 高度使用，儲存您的需求來建立和管理您自己的 DNS 伺服器叢集 Azure 所提供的名稱解析服務。

- 可搭配使用您自己的 DNS 伺服器以解決內部部署和 Azure hostname。

- 名稱解析提供之間角色執行個體/Vm 中相同的雲端服務，而不需要的 FQDN。

- 名稱解析提供之間 Vm 虛擬有的網路使用的資源管理員部署模型，而不需要的 FQDN。 在傳統的部署模型中的虛擬網路需要 FQDN 時解決不同的雲端服務中的名稱。 

- 您可以使用 [主機名稱描述您的部署，而不是使用自動產生的名稱。

**考量︰**

- 無法修改 Azure 建立的尾碼。

- 您無法手動註冊您自己的記錄。

- 不支援獲勝和 NetBIOS。 （您無法看到您在 Windows 檔案總管] 中的 Vm）。

- 主機名稱必須是 DNS 相容 (他們必須使用只 0-9 a 到 z 和 '-」，並無法開頭或結尾 '-'。 請參閱 RFC 3696 第 2 節）。

- DNS 查詢的流量會針對每個 VM 經流速控制。 這不應影響大部分的應用程式。  如果觀察要求節流，確定已啟用 [用戶端快取。  如需詳細資訊，請參閱[從 Azure 所提供的名稱解析最](#Getting-the-most-from-Azure-provided-name-resolution)。

- 只在第一個 180 雲端服務中的 Vm 登錄傳統部署模型中的每一個虛擬網路。 這會套用至虛擬網路資源管理員部署模型中。


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>從 Azure 所提供的名稱解析最取得
**用戶端快取︰**

並非每個 DNS 查詢需要透過網路傳送。  用戶端快取，可協助減少延遲，以解決週期性 DNS 查詢從本機快取提升網路音效恢復功能。  DNS 記錄包含存留時間 (TTL) 可讓儲存盡可能久的記錄，而不會影響記錄的有效期限，用戶端快取為適用於大多數情況快取。

預設 Windows DNS 用戶端有內建的 DNS 快取。  Distros 不包含預設的快取一些 Linux，建議一個會新增至每個 Linux VM （後，還沒有本機快取的檢查）。

有許多不同的 DNS 快取套件的可用，例如 dnsmasq，以下是最常見的 distros 上安裝 dnsmasq 的步驟︰

- **Ubuntu （使用 resolvconf）**︰
    - 只安裝 dnsmasq 套件 (「 sudo 引起取得安裝 dnsmasq 」)。
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

> [AZURE.NOTE] 「 Dnsmasq' 封裝是其中一個可用的 Linux 許多 DNS 快取。  再使用它，請檢查其是否符合您特定需求及已安裝其他的快取。

**用戶端重試︰**

DNS 是主要 UDP 通訊協定。  為 UDP 通訊協定不保證郵件傳遞，重試邏輯被處理 DNS 通訊協定本身。  每個 DNS 用戶端 （作業系統） 可以呈現不同的重試邏輯根據的建立者無法看見喜好設定︰

 - Windows 作業系統後重試 1 第二個和再選一次之後，另一個 2，4，另一個 4 秒鐘。 
 - 預設 Linux 設定重試 5 秒後。  建議您變更此 5 的時間間隔 1 第二個再試一次。  

若要查看上 Linux VM，「 貓 /etc/resolv.conf' 及外觀，在 [選項] 列中的目前設定，例如所示︰

    options timeout:1 attempts:5

Resolv.conf 檔案通常會自動產生，並不應加以編輯。  依 distro，而定，新增 [選項] 列的特定步驟︰

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
您的名稱解析需求可能移超出所提供的功能 Azure，例如時使用 Active Directory 網域的位置，或您所需的 DNS 解析虛擬網路 (vnets) 之間，有數字的情況。  若要涵蓋這些案例，Azure 會提供的功能，您可以使用您自己的 DNS 伺服器。  

虛擬網路中的 DNS 伺服器可以轉寄給 Azure 的遞迴解析程式，來解決 hostname 的虛擬網路中的 DNS 查詢。  例如 Azure 中執行網域控制站 (DC) 可以回應其網域的 DNS 查詢，並將所有其他查詢轉寄給 Azure。  這個選項可讓 Vm 若要查看您內部部署資源 （透過 DC) 」 和 「 Azure 提供 hostname （透過轉寄）。  透過虛擬 IP 168.63.129.16 提供 Azure 的遞迴解析程式存取。

DNS 轉寄也可讓內部 vnet DNS 解析，並允許以解決提供 Azure hostname 您內部部署電腦。  求得 VM 的主機名稱，DNS 伺服器 VM 必須位於相同的虛擬網路，然後設定轉寄 hostname Azure 查詢。  每個 vnet 的不同 DNS 尾碼現狀，您可以使用條件轉寄規則 DNS 查詢傳送到正確的 vnet 的解析度。  下圖顯示兩個 vnets 及執行使用這個方法間 vnet DNS 解析內部部署網路。  使用中的[Azure 快速入門範本庫](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/)及[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)範例 DNS 轉寄站。

![間 vnet DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

使用所提供 Azure 名稱解析，內部 DNS 尾碼時 (*。 internal.cloudapp.net) 提供給每個 VM 使用 DHCP。  主機名稱記錄和在 internal.cloudapp.net 區域中，這可讓主機名稱解析。  時使用您自己的名稱解析解決方案，不是提供 IDN 尾碼給 Vm 因為它會干擾 （例如網域案例） 其他 DNS 架構。  請改為我們提供無法運作版面配置區 (reddog.microsoft.com)。  

如有需要可以使用 PowerShell 或 API 決定內部 DNS 後置字元︰

-  資源管理員部署模型中的虛擬網路，尾碼是可透過[網路介面卡](https://msdn.microsoft.com/library/azure/mt163668.aspx)資源，或透過[取得 AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx)指令程式。    
-  傳統的部署模型中的尾碼是可透過[取得部署 API](https://msdn.microsoft.com/library/azure/ee460804.aspx)通話，或透過[取得 AzureVM-偵錯](https://msdn.microsoft.com/library/azure/dn495236.aspx)指令程式。


如果查詢送給 Azure 不符合您的需求，您必須提供您自己的 DNS 解決方案。  將需要 DNS 方案︰

-  提供適當的主機名稱解析，例如透過[DDNS](virtual-networks-name-resolution-ddns.md)。  請注意，如果使用的 DDNS 您可能需要停用 DNS 記錄清除 Azure 的 DHCP 租用很長及清除所可能會永久移除的 DNS 記錄。 
-  提供適當的遞迴解析允許外部網域名稱的解析度。
-  無障礙 （TCP 和 UDP 連接埠 53） 從其所做的用戶端，可以存取網際網路。
-  受到保護，針對存取從網際網路]，以減少威脅外部代理程式。

> [AZURE.NOTE] 為獲得最佳效能，IPv6 時使用的 DNS 伺服器 Azure Vm，應該停用，並[執行個體層級的公用 IP](virtual-networks-instance-level-public-ip.md)應該要指派給每個 DNS 伺服器 VM。  如果您選擇為您的 DNS 伺服器使用 Windows Server，[本文](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx)會提供額外的效能分析及最佳化。


### <a name="specifying-dns-servers"></a>指定 DNS 伺服器

使用您自己的 DNS 伺服器時, Azure 提供指定每個虛擬網路或網路介面 （資源管理員） 的多個 DNS 伺服器 / 雲端服務 （傳統） 的功能。  指定雲端服務/網路介面的 DNS 伺服器可以取得所指定的虛擬網路的優先順序。

> [AZURE.NOTE] 網路連線內容，例如 DNS 伺服器 IPs，不應加以編輯直接在 Windows Vm 為其取得刪除服務期間治療虛擬網路介面卡會取代時。 


使用資源管理員部署模型時，可以在入口網站，API 範本 ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx)， [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) 或 PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx)， [nic](https://msdn.microsoft.com/library/mt619370.aspx)) 指定 DNS 伺服器。

使用傳統的部署模型時，可以在入口網站或[*網路設定*檔](https://msdn.microsoft.com/library/azure/jj157100)中指定的虛擬網路的 DNS 伺服器。  如需雲端服務，透過 [[*服務設定*檔](https://msdn.microsoft.com/library/azure/ee758710)，或在 PowerShell ([新增 AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)) 中指定 DNS 伺服器。

> [AZURE.NOTE] 如果您變更已部署的虛擬網路/虛擬機器中的 [DNS 設定，您需要重新啟動每個受影響的 VM，變更才會生效。


## <a name="next-steps"></a>後續步驟

資源管理員部署模型︰

- [建立或更新虛擬網路](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [建立或更新網路介面卡](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [新 AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [新 AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
傳統的部署模型︰

- [Azure 服務設定結構描述](https://msdn.microsoft.com/library/azure/ee758710)
- [虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100)
- [使用網路的設定檔設定虛擬網路](virtual-networks-using-network-configuration-file.md) 

