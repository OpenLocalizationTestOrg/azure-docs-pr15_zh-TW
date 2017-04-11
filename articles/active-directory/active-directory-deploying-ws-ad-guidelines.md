<properties
   pageTitle="部署 Windows Azure 虛擬機器上的伺服器 Active Directory 的指導方針 |Microsoft Azure"
   description="如果您已經知道如何將 AD 網域服務與 AD Federation Services 內部部署，進一步瞭解其運作 Azure 虛擬機器上的方式。"
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>部署 Azure 虛擬機器上的 Windows Server Active Directory 的指導方針

本文說明部署 Windows Server Active Directory 網域服務 (AD DS) 和 Active Directory Federation Services (AD FS) 內部部署與 Microsoft Azure 虛擬機器上部署間的差異。

## <a name="scope-and-audience"></a>搜尋範圍和對象

本文適用於那些已熟悉部署 Active Directory 內部部署。 矩形部署 Microsoft Azure 虛擬機器/Azure 虛擬網路和傳統的內部部署 Active Directory 部署的 Active Directory 之間的差異。 Azure 虛擬機器和 Azure 虛擬網路是基礎結構-為-的-項服務 (IaaS) 的組織運用雲端中的計算資源的一部分。

對於那些熟悉部署，請參閱[AD DS 部署指南](https://technet.microsoft.com/library/cc753963)或[計劃 AD FS 部署](https://technet.microsoft.com/library/dn151324.aspx)視。

本文假設讀者都熟悉以下的概念︰

- Windows Server AD DS 部署及管理
- 部署及設定 DNS 以支援 Windows Server AD DS 基礎結構
- Windows Server AD FS 部署及管理
- 部署、 設定及管理依賴廠商應用程式 （網站與 web 服務），都可以使用 Windows Server AD FS 權杖
- 一般虛擬機器概念，例如設定虛擬機器、 虛擬磁碟，以及虛擬網路

本文會醒目提示混合式部署中的 Windows Server AD DS 或 AD FS 內部部署只能而只能 Azure 虛擬機器上部署的需求。 文件第一次涵蓋與內部部署，並會影響 [設計] 及 [部署的重要決策 Azure 虛擬機器上執行 Windows Server AD DS 與 AD FS 重要的差異。 紙張的其餘部分說明的指導方針的每個決策點，在 [更多詳細資料，以及如何將準則套用至各種部署案例。

本文不討論的[Azure Active Directory](http://azure.microsoft.com/services/active-directory/)設定，這是雲端應用程式提供身分識別管理和存取控制功能的其餘服務。 Azure Active Directory (Azure AD) 和 Windows Server AD DS，不過，搭配使用來為今天的混合式部署 IT 提供身分識別與存取權的管理解決方案環境和現代化的應用程式。 若要協助瞭解差異，以及 Windows Server AD DS 與 Azure AD 之間的關聯，請考慮下列各項︰

1. 若要將您的內部部署資料中心擴充至雲端使用 Azure 時，可能會在雲端執行 Windows Server AD DS Azure 虛擬機器上。
2. 您可以使用 Azure AD 軟體為-的-服務 (SaaS) 應用程式為您的使用者單一登入。 Microsoft Office 365 使用這項技術，例如 [和 Azure 或其他雲端平台上執行應用程式也可以使用它。
3. 您可以使用，讓使用者登入的 Azure AD （其存取控制服務） 中使用會裝載於內部部署或雲端中的應用程式從 Facebook、 Google、 Microsoft 和其他身分識別提供者的身分識別。

如需有關這些差異的詳細資訊，請參閱[Azure 身分識別](fundamentals-identity.md)。

## <a name="related-resources"></a>相關的資源

您可能會下載並執行[Azure 虛擬機器整備評估](https://www.microsoft.com/download/details.aspx?id=40898)。 評估會自動檢查您的內部部署環境，並產生根據本指南可協助您將環境移轉至 Azure 本主題中找到的自訂的報表。

建議您先也檢閱的教學課程，輔助線] 及視訊涵蓋下列主題︰

- [設定 Azure 入口網站中的雲端專用的虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [設定網站-VPN Azure 入口網站中](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Azure 虛擬網路上安裝新的 Active Directory 樹系](active-directory-new-forest-virtual-machine.md)
- [Azure 上安裝複本 Active Directory 網域控制站](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IT 專業人員 IaaS: (01) 的虛擬機器基本概念](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IT 專業人員 IaaS: (05) 建立虛擬網路與跨內部部署連線](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>簡介

部署 Azure 虛擬機器上的 Windows Server Active Directory 的基本需求差異很少從內部部署虛擬機器中 （，然後在某種程度的實體機器） 部署。 例如，若是 Windows Server AD DS 中，如果您部署 Azure 虛擬機器網域控制站 (Dc) 會將現有的複本內部部署的公司網域/樹系，然後 Azure 部署可以主要被視為相同的方式來您可能會處理其他任何其他 Windows Server Active Directory 的網站。 也就是說，必須在 Windows Server AD DS、 建立網站、 連結至該網站，而連線至其他網站使用適當的網站連結的子網路中定義子網路。 有，不過，一些不同之處，都有所有 Azure 部署的一些特定的部署案例而有所不同。 列出兩個基本差異如下︰

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure 虛擬機器可能需要連線至內部部署的公司網路。

連線至內部部署公司網路的 Azure 虛擬機器需要 Azure 虛擬網路，包括網站為網站或網站-點虛擬私人網路 (VPN) 元件無法順利進行連線 Azure 虛擬機器和內部部署電腦。 此 VPN 元件也可能會讓內部部署網域成員電腦存取其網域控制站裝載於 Azure 虛擬機器 Windows Server Active Directory 網域。 請務必請注意，不過，是否要有 vpn 才能失敗，請驗證]，然後取決於 Windows Server Active Directory 其他作業也會失敗。 當使用者可能無法登入使用現有的快取認證，所有的對等，或為其票證尚未發行，或擁有已過時的用戶端與伺服器驗證嘗試將會失敗。

請參閱[虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)影片示範和逐步解說教學課程，包括[設定網站-VPN Azure 入口網站中](../vpn-gateway/vpn-gateway-site-to-site-create.md)的清單。

> [AZURE.NOTE] 您也可以將 Windows Server Active Directory 部署沒有與內部部署網路連線 Azure 虛擬網路上。 本主題中，準則然而，假設使用 Azure 虛擬網路，因為它提供 IP 位址是基本 Windows server 的功能。

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>使用 PowerShell 的 Azure 必須設定靜態 IP 位址。

動態地址配置根據預設，但使用設定 AzureStaticVNetIP cmdlet 改為指定的靜態 IP 位址。 設定會套用至服務及 VM 關機重新啟動的靜態 IP 位址。 如需詳細資訊，請參閱[虛擬機器內部靜態 IP 位址](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)。

## <a name="BKMK_Glossary"></a>詞彙和定義

以下是各種 Azure 技術會參考本文中的字詞的非完整清單。

- **Azure 虛擬機器**︰ 可讓客戶部署 Vm 執行幾乎任何傳統的 Azure IaaS 產品上內部部署伺服器工作量。

- **Azure 虛擬網路**︰ 可讓客戶建立及管理 Azure 虛擬網路與安全地將其連結至自己的 Azure 中的網路服務內部部署網路基礎結構透過虛擬私人網路 (VPN)。

- **虛擬 IP 位址**︰ 未繫結至特定的電腦或網路介面卡的網際網路的 IP 位址。 雲端服務會指派接收網路流量重新導向至 Azure VM 虛擬 IP 位址。 虛擬的 IP 位址是其中可以包含一或多個 Azure 虛擬機器雲端服務的屬性。 另請注意 Azure 虛擬網路，可包含一或多個雲端服務。 虛擬 IP 位址提供原生負載平衡功能。

- **動態 IP 位址**︰ 這是只有內部 IP 位址。 應該將它設定為靜態 IP 位址 （藉由使用設定 AzureStaticVNetIP cmdlet） 的 Vm 裝載 DC/DNS 伺服器角色。

- **服務治療**︰ 在其中 Azure 自動傳回服務的執行狀態再次偵測到服務已失敗之後的程序。 服務治療是一種支援可用性及恢復 Azure 特性。 雖然不太，追蹤治療事件 dc VM 上執行服務的結果類似非預期的重開機，但有幾個的影響︰

 - 在 VM 虛擬網路介面卡會變更
 - 虛擬網路介面卡的 MAC 地址會變更
 - VM 處理器/CPU 識別碼會變更
 - 虛擬網路介面卡的 IP 設定不會變更，只要 VM 附加至虛擬網路和 VM 的 IP 位址是靜態。

 這些行為都不會影響到 Windows Server Active Directory，因為它有無相依性的 MAC 地址或處理器/CPU ID，而且所有的 Windows Server Active Directory 部署 Azure 上所建議的上述 Azure 虛擬網路上執行。

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>這是安全虛擬化 Windows Server Active Directory 網域控制站？

部署 Azure 虛擬機器上的 Windows Server Active Directory 網域控制站應採用愛爾蘭的相同的指引，為虛擬機器中執行的 Dc 內部部署。 執行虛擬的 Dc 是安全的做法，只要備份和還原 Dc 指導方針的追蹤。 如需有關限制和指導方針執行虛擬的 Dc 的詳細資訊，請參閱[在 HYPER-V 執行網域控制站](https://technet.microsoft.com/library/dd363553)。

Hypervisors 提供，或是 trivialize 技術，可能會導致許多分散式系統，包括 Windows Server Active Directory 的問題。 例如實體伺服器上，您可以複製光碟或使用不受支援的方法來復原狀態的伺服器，包括使用襯等等，但實體伺服器上執行的是難還原虛擬機器中的快照 hypervisor 比。 Azure 提供相同的非預期條件會造成的功能。 例如，您應該複製 Dc VHD 的檔案，而不是執行定期備份，因為還原可能會導致類似的情況下，使用快照還原功能。

這類回復介紹 USN 泡泡可能導致之間 Dc 永久不同的狀態。 例如可能會導致問題︰

- 延遲物件
- 不一致的密碼
- 不一致的屬性值
- 如果已復原的結構描述母片的結構描述不符

如需有關如何影響網域控制站的詳細資訊，請參閱[USN 和 USN 復原](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback)。

以 Windows Server 2012，[其他的保護，內建 AD DS](https://technet.microsoft.com/library/hh831734.aspx)的開頭。 這些保護能防止虛擬的網域控制站針對上述的問題，只要基礎 hypervisor 平台支援 VM GenerationID。 Azure 支援 VM-GenerationID，表示在執行 Windows Server 2012 或稍後 Azure 虛擬機器的網域控制站有其他的保護。

> [AZURE.NOTE] 您應該關閉並重新啟動 VM 來賓作業系統，而不是 Azure 傳統入口網站中使用**關機**] 選項中，執行 Azure 中的網域控制站的。 現在，關閉 [VM 使用傳統的入口網站會使解除配置 VM。 解除配置的 VM 的優點不會產生費用，但它也會重設 VM GenerationID，這是不想要的 DC。 時重設 VM GenerationID AD DS 資料庫的呼叫識別碼也重設，捨棄 RID 集區與 SYSVOL 會標示為非授權。 如需詳細資訊，請參閱[簡介 Active Directory 網域服務 (AD DS) 虛擬化](https://technet.microsoft.com/library/hh831734.aspx)和[安全地虛擬化 DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx)。

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>為什麼部署 Windows Server AD DS Azure 虛擬機器上的商務連絡人？

許多 Windows Server AD DS 部署案例是適合的部署與 Vm Azure 上。 例如，假設您的需要驗證使用者在亞洲遠端位置的公司。 公司尚未先前部署 Windows 伺服器 Active Directory 中的 Dc 亞洲因為部署以及有限管理伺服器部署後的專業知識的成本。 如此一來，亞洲驗證要求是由提供服務的 Dc 不理想的結果。 在此情況下，您可以部署在您指定必須執行亞洲 Azure 資料中心內的 VM DC。 將 DC 附加至已直接連接到遠端位置 Azure 虛擬網路，藉此改善驗證效能。

Azure 也很適合否則成本損毀修復 (DR) 網站的替代的。 較低成本的主機服務少量網域控制站和單一虛擬網路上 Azure 代表美觀的替代方案。

最後，您可能會想要部署網路上的應用程式 Azure，例如 SharePoint，需要 Windows Server Active Directory 但已在內部網路或公司的 Windows Server Active Directory 沒有相依性。 在此情況下，部署上以符合 SharePoint Azure 隔離樹系伺服器的需求是最佳選擇。 同樣地，也支援部署需要連線至內部部署網路與公司的 Active Directory 的網路應用程式。

> [AZURE.NOTE] 因為它所提供的圖層 3 連線，VPN 元件，可提供 Azure 虛擬網路與內部部署網路之間的連線也可以啟用執行內部部署運用 Azure 虛擬網路執行為 Azure 虛擬機器的 Dc 的成員伺服器。 但如果無法使用 VPN，之間的通訊內部部署電腦和 Azure 基礎網域控制站將無法運作，結果在 [認證及其他各種不同的錯誤。  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>部署內部部署與 Windows Server Active Directory 網域控制站上 Azure 虛擬機器之間的對比

- 針對包含超過單一 VM 任何 Windows Server Active Directory 部署案例，就必須使用 Azure 虛擬網路的 IP 位址一致性。 請注意，本指南假設 Dc Azure 的虛擬網路上執行。

- 與內部部署 Dc，建議使用靜態 IP 位址。 使用 PowerShell 的 Azure 只可以設定的靜態 IP 位址。 如需詳細資訊，請參閱[Vm 內部靜態 IP 位址](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)。 如果您有監控系統或檢查來賓作業系統內的靜態 IP 位址設定其他方案，您可以指定網路介面卡的內容 VM 的相同的靜態 IP 位址。 但是請注意如果 VM 期經歷服務治療或關閉 [傳統] 入口網站中，已解除配置其位址的網路介面卡會遭到捨棄。 在此情況下，來賓內的靜態 IP 位址會需要重設。

- 部署 Vm 虛擬網路上不會暗示 （或需要） 連線至內部部署網路;虛擬網路只是會讓該。 您必須建立虛擬私人通訊 Azure 及內部網路的網路。 您需要部署內部網路上的 VPN 端點。 從 Azure 開啟 VPN 內部網路。 如需詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)與[設定-網站 VPN Azure 入口網站中](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

> [AZURE.NOTE] 其中一個選項以[建立點為網站 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)有個別的 windows 電腦直接連接到 Azure 虛擬網路。

- 無論您是否建立虛擬網路的出口流量，但不是輸入 Azure 費用。 各種不同的 Windows Server Active Directory 設計選項可能會影響部署產生多少出口流量。 例如，部署唯讀網域控制站 (RODC) 限制出口流量因為不複寫輸出。 但決定来部署 RODC 需要必須權衡執行寫入作業，亦即和應用程式與服務網站有 Rodc[相容性](https://technet.microsoft.com/library/cc755190)需求。 如需詳細資料傳輸費用的詳細資訊，請參閱[Azure 價格，然](http://azure.microsoft.com/pricing/)。

- 已完成控制透過何種伺服器資源可用於 Vm 內部部署，例如 RAM 磁碟大小，以及等等，在 Azure 您必須從清單中選取預先設定的伺服器大小。 Dc，資料磁碟是儲存在 Windows Server Active Directory 資料庫必要除了作業系統磁碟。

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>您可以部署 Azure 虛擬機器上的 Windows Server AD FS 嗎？

是的您可以佈署 Azure 虛擬機器上的 Windows Server AD FS 和[AD FS 部署的最佳作法](https://technet.microsoft.com/library/dn151324.aspx)內部適用於在 Azure AD FS 部署。 但部分的最佳作法，例如負載平衡和可用性 」 需要技術超出什麼 AD FS 提供本身。 他們必須提供的基礎。 讓我們來檢閱一些這些最佳作法，請參閱如何達成其使用 Azure Vm 和 Azure 虛擬網路。

1. **永遠不會揭露直接到網際網路的安全性 token 服務 (STS) 伺服器。**

    這是很重要，因為 STS 問題安全性權杖。 如此一來，應該處理相同的網域控制站的保護層級 STS，例如 AD FS 伺服器的伺服器。 如果 STS 遭惡意使用者會有存取權杖可能包含的信賴廠商應用程式和其他 STS 伺服器信任組織中的選擇的能力。

2. **部署 Active Directory 網域控制站 AD FS 伺服器相同的網路中的所有使用者網域。**

    AD FS 伺服器驗證的使用者使用 Active Directory 網域服務。 建議您部署網域控制站 AD FS 伺服器相同的網路上。 這會提供業務連續性，以防 Azure 網路與您的內部部署網路之間的連結會中斷，並讓較低的延遲和登入的效能提升。

3. **部署高可用性和平衡載入的多個 AD FS 節點。**

    在大部分情況下，AD FS 可讓應用程式錯誤是無法接受報價，因為需要應用程式，通常是安全性權杖任務要徑。 如此一來，與 AD FS 現在位於要徑存取重要的應用程式，因為 AD FS 服務必須是透過多個 AD FS proxy 和 AD FS 伺服器高度可用。 若要達到通訊群組的要求，通常會前面 AD FS 伺服器和 AD FS Proxy 部署負載平衡器。

4. **部署網際網路存取的一或多個 Web 應用程式 Proxy 節點。**

    當使用者需要存取 AD FS 服務受保護的應用程式時，AD FS 服務必須是您可以從網際網路。 這被達成部署 Web 應用程式 Proxy 服務。 若要將多個節點部署的可用性及負載平衡強烈建議。

5. **從 Web 應用程式 Proxy 節點限制存取的內部網路資源。**

    若要允許外部使用者存取 AD FS 從網際網路]，您需要部署 Web 應用程式 Proxy 節點 （或舊版 Windows Server 中的 AD FS Proxy）。 Web 應用程式 proxy 節點直接是網際網路上公開。 就不需要網域，並透過 TCP 連接埠 443 和 80 只需要 AD FS 伺服器的存取權。 強烈建議遭到封鎖其他所有電腦 （特別是網域控制站） 的通訊。

    這是通常達成內部部署透過 DMZ。 防火牆使用操作 whitelist 的模式來限制流量 DMZ 從內部部署網路 （也就是，只允許從指定的 IP 位址，然後移至指定的連接埠流量時，會封鎖其他所有流量）。

下圖顯示的傳統的內部部署 AD FS 部署。

![在傳統的內部部署 Active Directory Federation Services 部署的圖表](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

不過，因為 Azure 不提供原生，完整功能的防火牆功能，其他選項必須要用來限制流量。 下表顯示每個選項和其優缺點。

| 選項 | 利用 | 缺點 |
| ------ | --------- | ------------ |
| [Azure 網路 Acl](virtual-networks-acl.md) | 降低成本與簡單的初始設定 | 如果任何新 Vm 會新增至部署時，需要額外的網路 ACL 設定 |
| [Barracuda NG 防火牆](https://www.barracuda.com/products/ngfirewall) | Whitelist 模式的作業，而且不需要網路 ACL 設定 | 增強的成本及複雜的初始設定 |

部署 AD FS 的進階步驟在本例中所示︰

1. 建立跨內部部署連線，使用 VPN 或[ExpressRoute](http://azure.microsoft.com/services/expressroute/)虛擬網路。

2. 部署網域控制站虛擬網路上。 此步驟是選擇性的但建議使用。

3. 部署虛擬網路上的 [網域 AD FS 伺服器。

4. 建立[內部負載平衡設定](http://azure.microsoft.com/blog/internal-load-balancing/)，包括 AD FS 伺服器，並使用新的私人 IP 位址中虛擬網路 （動態 IP 位址）。

  1. 更新 DNS 若要建立指向內部負載平衡設定的私人 （動態） IP 位址的 FQDN。

5. 建立 Web 應用程式 Proxy 節點雲端服務 （或另一個虛擬網路）。

6. 部署中的雲端服務或虛擬網路 Web 應用程式 Proxy 節點

  1. 建立外部負載平衡組，包括 Web 應用程式 Proxy 節點。

  2. 更新外部 DNS 名稱 (FQDN)，指向雲端服務公用 IP 位址 （虛擬 IP 位址）。

  3. 設定 AD FS proxy]，使用對應至內部負載平衡組 AD FS 伺服器的 FQDN。

  4. 更新宣告式網站，以其宣告提供者使用外部的 FQDN。

7. 限制 Web 應用程式 Proxy AD FS 虛擬網路中的任何電腦之間的存取權。

若要限制流量，Azure 內部負載平衡器的負載平衡集必須 TCP 連接埠 80 和 443、 僅流量的設定，所有其他的流量負載平衡設定的內部動態 IP 位址卸除。

![允許使用 TCP 443 + 80 ADFS 網路 Acl 的圖表。](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

AD FS 伺服器的流量允許使用只能由下列來源︰

- Azure 內部的負載平衡器。
- 在內部部署網路系統管理員的 IP 位址。

> [AZURE.WARNING] 設計必須防止 [Web 應用程式 Proxy 節點 Azure 虛擬網路中的任何其他 Vm 或內部網路上的任何位置。 可以完成的內部部署應用裝置的快速傳送連線或網站-VPN 連線的 VPN 裝置設定防火牆規則。

這個選項缺點是需要在多個裝置，包括內部負載平衡器、 AD FS 伺服器，以及新增至虛擬網路的任何其他伺服器的網路 Acl 設定。 如果沒有設定限制流量的網路 Acl 加入部署任何裝置，整個部署可以風險。 如果 Web 應用程式 Proxy 節點的 IP 位址變更這個，網路 Acl 必須重設 （亦即 proxy 應設定為使用[動態的靜態 IP 位址](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)）。

![ADFS 上與網路 ACL Azure。](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

另一個選項是使用[Barracuda NG 防火牆](https://www.barracuda.com/products/ngfirewall)應用裝置來控制之間 AD FS proxy 伺服器和 AD FS 伺服器的流量。 這個選項符合安全性和高可用性的最佳作法，需要較少的管理初始安裝後，因為 Barracuda NG 防火牆應用裝置提供的防火牆管理 whitelist 模式，您可以直接在 Azure 虛擬網路上安裝。 就不需要設定網路 Acl 隨時部署加入新的伺服器。 但這個選項，將初始部署複雜度和成本。

在此情況下，而不是一個部署兩個虛擬網路。 我們會撥打電話給 VNet1 和 VNet2。 VNet1 包含 proxy 和 VNet2 包含 Sts 與網路連線至公司網路。 VNet1 是實際 （雖然幾乎所有） 隔離從 VNet2，從公司網路。 VNet1 就會連接到 VNet2 使用特殊通道技術為傳輸獨立網路架構 （陳）。 陳通道附加至每個虛擬網路使用 Barracuda NG 的防火牆，在每一個虛擬網路上的一個 Barracuda。  高可用性，建議您部署兩個 Barracudas 在每個虛擬網路。一個使用中時，其他被動。 提供太豐富的防火牆功能可讓我們來模仿傳統的內部部署 DMZ Azure 中的作業。

![ADFS Azure 防火牆上。](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

如需詳細資訊，請參閱[AD FS︰ 延伸到網際網路的宣告注意內部部署前端應用程式](#BKMK_CloudOnlyFed)。

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>AD FS 部署如果目標是單獨的 Office 365 SSO 替代方案

有另一個方法部署 AD FS 完全，如果您的目標是僅適用於 Office 365 登入啟用。 在此情況下，您可以只使用密碼同步處理內部部署目錄同步，並使用最小的部署複雜度達成相同的結果，因為這種方式不需要 AD FS 或 Azure。

下表將比較與不包含部署 AD FS，登入程序的運作方式。

| Office 365 單一登入使用 AD FS 和 DirSync | Office 365 相同登入使用 DirSync + 密碼同步處理 |
| ------------- | ------------- |
| 1.使用者登入公司網路，並通過 Windows Server Active Directory。 | 1.使用者登入公司網路，並通過 Windows Server Active Directory。 |
| 2.在使用者嘗試存取 Office 365 (我@contoso.com)。 | 2.在使用者嘗試存取 Office 365 (我@contoso.com)。 |
| 3.office 365 使用者重新導向至 Azure AD。 | 3.office 365 使用者重新導向至 Azure AD。 |
| 4.由於 Azure AD 無法驗證使用者，並瞭解 AD FS 內部部署與信任，它會重新導向使用者 AD FS。 | 4.azure AD 無法直接接受 Kerberos 票證且沒有信任關係存在，它會要求使用者輸入認證。 |
| 5。 的使用者會傳送至 AD FS STS Kerberos 票證。 | 5。 在使用者輸入相同的內部部署密碼，然後 Azure AD 驗證他們的使用者名稱和密碼已同步處理的目錄同步。 |
| 6.AD FS 轉換必要 token 格式/宣告 Kerberos 票證，並重新導向至 Azure AD 的使用者。 | 6.azure AD 使用者重新導向至 Office 365。 |
| 7。 Azure AD 使用者驗證 （另一個轉換會發生）。 |  7.使用者可以在 Office 365 並使用 Azure AD 權杖 OWA 登入。 |
| 8.azure AD 使用者重新導向至 Office 365。 |  |
| 9。 使用者自動登入 Office 365。 |  |

含 DirSync 使用密碼同步處理案例 (沒有 AD FS)，Office 365，單一登入會取代 「 相同登入 」 位置 」 相同 」 就是指使用者必須重新輸入其相同的內部部署認證時存取 Office 365。 請注意此資料可以會記錄以協助降低後續提示使用者的瀏覽器。

### <a name="additional-food-for-thought"></a>不同的食物 for 思考

- 如果您部署 AD FS proxy Azure 虛擬機器上的時，需要 AD FS 伺服器的連線。 如果他們是內部部署，建議您運用提供的虛擬網路中，讓與 AD FS 伺服器通訊的 Web 應用程式 Proxy 節點網站-VPN 連線。

- 如果您部署 AD FS server Azure 虛擬機器上的，連線至 Windows Server Active Directory 網域控制站、 屬性儲存區及設定資料庫，則需要和 ExpressRoute 或 Azure 虛擬網路與內部部署網路之間的網站-VPN 連線，也可能會要求。

- 從 Azure 虛擬機器 （出口流量），費用會套用至所有流量。 如果成本行駛因子變異數，建議您部署上 Azure 離開 AD FS 伺服器內部部署的 Web 應用程式 Proxy 節點。 Azure 虛擬機器以及部署 AD FS 伺服器，如果其他成本帶來驗證內部部署使用者。 出口流量必須支付的成本，無論它為通過 ExpressRoute 或 VPN 網站的連線。

- 如果您決定要使用 Azure 的原生伺服器負載平衡高可用性 AD FS 伺服器的功能，請注意，負載平衡提供探查用來決定的虛擬機器中的雲端服務健康狀況。 若是 Azure 虛擬機器 （而不是網頁或背景工作角色），因為回應預設探查代理程式未顯示在 Azure 虛擬機器上必須使用自訂的檢查。 為求，您可能會使用自訂的 TCP 探查，只需要的 TCP （TCP SYN 區段傳送和回應的 TCP SYN ACK 區段） 成功連線以判定虛擬機器健康情況。 您可以設定為使用您的虛擬機器，主動接聽任何 TCP 連接埠自訂探查。

> [AZURE.NOTE] 需要公開相同的連接埠直接 （例如連接埠 80 和 443） 網際網路一組的電腦無法共用相同的雲端服務。 因此，建議您建立專用的雲端服務，您的 Windows Server AD FS server，以避免可能會與應用程式的連接埠需求與 Windows Server AD FS 之間重疊。

## <a name="deployment-scenarios"></a>部署案例

下節概述完全部署案例注意力必須採取的重要考量至帳戶。 每個案例有更多詳細資料的決策及因素考慮的連結。

1. [AD DS︰ 部署 AD DS 注意公司網路連線不需要使用應用程式](#BKMK_CloudOnly)

    例如，具網際網路 SharePoint 服務已部署 Azure 虛擬機器上。 應用程式具有公司網路資源沒有相依性。 需要 Windows Server AD DS 應用程式，但不需要公司的 Windows Server AD DS。

2. [AD FS︰ 延伸到網際網路的宣告注意內部部署前端應用程式](#BKMK_CloudOnlyFed)

    例如，具有已成功部署內部部署使用者和使用公司的宣告注意應用程式需要成為從網際網路存取。 應用程式需要存取網際網路上直接使用自己的公司身分識別的商業夥伴和公司的現有使用者。

3. [AD DS︰ 部署需要連線至公司網路的 Windows Server AD DS 可識別應用程式](#BKMK_HybridExt)

    例如，支援 Windows 整合式驗證，並使用 Windows Server AD DS 存放庫作為資料設定和使用者設定檔的 LDAP 注意應用程式部署 Azure 虛擬機器上。 會以運用現有的公司 Windows Server AD DS，並提供單一登入應用程式。 應用程式不知道宣告。

### <a name="BKMK_CloudOnly"></a>1.AD DS︰ 部署 AD DS 注意公司網路連線不需要使用應用程式

![雲端專用 AD DS 部署](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**圖 1**

#### <a name="description"></a>描述

Azure 虛擬機器上部署 SharePoint 並應用程式的公司網路資源有無相依性。 應用程式需要 Windows Server AD DS，但並*不*需要公司的 Windows Server AD DS。 沒有 Kerberos 或同盟的信任所需，因為使用者會自動提供透過 Windows Server AD DS 網域也裝載在雲端上 Azure 虛擬機器中的應用程式。

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>和技術區域套用到此案例的方式案例的考量事項

- [網路拓撲](#BKMK_NetworkTopology)︰ 建立 Azure 虛擬網路沒有跨內部部署連線 （也稱為網站-連線）。

- [DC 部署設定](#BKMK_DeploymentConfig)︰ 將新的網域控制站部署到新的單一網域的 Windows Server Active Directory 樹系。 此應該部署以及 Windows DNS 伺服器。

- [Windows Server Active Directory 網站拓撲](#BKMK_ADSiteTopology)︰ 使用預設的 Windows Server Active Directory 網站 （預設第一個站台名稱會所有電腦）。

- [IP 位址和 DNS](#BKMK_IPAddressDNS):

 - 使用設定 AzureStaticVNetIP Azure PowerShell 指令程式來設定，亦即的靜態 IP 位址。
 - 在安裝及設定 Windows Server DNS 網域控制站 Azure 上。
 - 設定虛擬網路內容的名稱和 VM 裝載的 DC 和 DNS 伺服器角色的 IP 位址。

- [通用類別目錄](#BKMK_GC)︰ 樹系中的第一個 DC 必須是全域目錄伺服器。 因為單一網域樹系通用類別目錄不需要任何額外的工作，亦即從其他 Dc 也必須設定為 Gc。

- [Windows Server AD DS 資料庫和 SYSVOL 的位置](#BKMK_PlaceDB)︰ 新增 Dc 執行為 Azure Vm 才能儲存 Windows Server Active Directory 資料庫、 記錄，以及 SYSVOL 資料磁碟。

- [備份與還原](#BKMK_BUR)︰ 決定您要儲存備份系統狀態的位置。 如有需要，請儲存備份的 DC vm 新增另一個資料磁碟。

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS︰ 延伸到網際網路的宣告注意內部部署前端應用程式

![跨內部部署連線同盟](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**圖 2**

#### <a name="description"></a>描述

具有已成功部署內部部署使用者和使用公司的宣告注意應用程式需要成為直接從網際網路存取。 應用程式可做為 web frontend 到以儲存資料的 SQL 資料庫。 在應用程式使用 SQL server 也在公司網路。 兩個 Windows Server AD FS Sts 和負載平衡器已部署內部部署 access 提供給公司的使用者。 應用程式現在必須此外存取網際網路上直接使用自己的公司身分識別的商業夥伴和公司的現有使用者。

以簡化和符合部署及設定的需求，這項需求的努力，它會決定其他兩個 web frontends 和第二個 Azure 虛擬機器上安裝 Windows Server AD FS proxy 伺服器。 所有四個 Vm 將公開直接到網際網路，並將會提供使用 Azure 虛擬網路的網站-VPN 功能的內部網路的連線。

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>和技術區域套用到此案例的方式案例的考量事項

- [網路拓撲](#BKMK_NetworkTopology)︰ 建立 Azure 虛擬網路和[設定跨內部部署連線能力](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

 > [AZURE.NOTE] 針對每個 Windows Server AD FS 憑證，請確定所定義的憑證範本，並產生的憑證的 URL，可以到達 Azure 上執行 Windows Server AD FS 執行個體。 這可能會要求您 PKI 基礎結構的某些部分跨內部部署連線。 範例如果 CRL 端點 LDAP 為基礎，並裝載獨佔內部，然後跨內部部署連線，就必須。 如果不希望，可能需要使用其 CRL 會在網際網路上存取 CA 所發出的憑證。

- [雲端服務設定](#BKMK_CloudSvcConfig)︰ 確保您有兩個 cloud services 順序提供兩個負載平衡虛擬 IP 位址。 第一個雲端服務的虛擬 IP 位址會導向至兩個連接埠 80 和 443 Windows Server AD FS proxy Vm。 Windows Server AD FS proxy Vm 將會設定為指向內部部署負載平衡的 IP 位址面對 Windows Server AD FS Sts。 第二個雲端服務的虛擬 IP 位址會導向至兩個 Vm web frontend 執行一次連接埠 80 和 443。 設定自訂的檢查，以確保負載平衡器只會引導至正常運作 Windows Server AD FS proxy 和 web frontend Vm 流量。

- [同盟伺服器設定](#BKMK_FedSrvConfig)︰ 設定 Windows Server AD FS 同盟伺服器 (STS) 來產生在雲端建立 Windows Server Active Directory 樹系安全性權杖。 設定同盟宣告提供者的信任關係，與您想要接受身分識別，從不同的合作夥伴，然後設定您想要產生的權杖，若要在不同應用程式依賴廠商信任資料庫關聯圖]。

    在大多數情況下，Windows Server AD FS proxy 伺服器部署中的安全性考量具網際網路容量與其 Windows Server AD FS 同盟對應項目仍然隔離的直接網際網路連線。 無論部署案例中，您必須設定您的雲端服務會提供公開的 IP 位址和連接埠過您兩個 Windows Server AD FS STS 執行個體 」 或 「 proxy 執行個體，可以負載平衡虛擬 IP 位址。

- [Windows Server AD FS 可用性設定](#BKMK_ADFSHighAvail)︰ 建議部署 Windows Server AD FS 具有至少兩個伺服器陣列的容錯移轉及負載平衡。 您可能會想要考慮的 Windows Server AD FS 設定資料，使用 Windows 內部資料庫 (WID)，並使用 Azure 的內部負載平衡功能傳入的邀請分散伺服器陣列中的伺服器。

如需詳細資訊，請參閱[AD DS 部署指南](https://technet.microsoft.com/library/cc753963)。


### <a name="BKMK_HybridExt"></a>3.AD DS︰ 部署需要連線至公司網路的 Windows Server AD DS 可識別應用程式

![跨內部部署 AD DS 部署](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**圖 3**

#### <a name="description"></a>描述

Azure 虛擬機器上部署 LDAP 注意的應用程式。 它支援 Windows 整合式驗證，並使用 Windows Server AD DS 作為存放庫設定及使用者設定檔資料。 目標是以運用現有的公司 Windows Server AD DS，並提供單一登入應用程式。 應用程式不知道宣告。 使用者也需要直接從網際網路存取應用程式。 若要最佳化效能與成本，它會決定兩個公司網域屬於其他網域控制站的同時 Azure 上的應用程式部署。

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>和技術區域套用到此案例的方式案例的考量事項

- [網路拓撲](#BKMK_NetworkTopology)︰ 建立 Azure 虛擬網路與[跨內部部署連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

- [安裝方法](#BKMK_InstallMethod)︰ 部署複本 Dc 從公司的 Windows Server Active Directory 網域。 DC 複本，您可以在 VM 上安裝 Windows Server AD DS 並選擇性地安裝從媒體 (IFM) 功能，若要減少需要安裝期間複製到新的 DC 的資料。 教學課程，請參閱[安裝 Azure 的複本 Active Directory 網域控制站](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)。 即使您使用 IFM 時，可能建立虛擬 DC 內部部署，並將整個虛擬硬碟 (VHD) 移至雲端，而不是在安裝期間複寫 Windows Server AD DS 更有效率。 安全，建議您刪除從內部部署網路 VHD 後，已複製到 Azure。

- [Windows Server Active Directory 網站拓撲](#BKMK_ADSiteTopology)︰ 在 Active Directory 網站和服務中建立新的 Azure 網站。 建立 Windows Server Active Directory 子網路物件代表 Azure 虛擬網路，並將子網路新增至網站。 建立新的網站連結，其中包含新 Azure 網站和 Azure 虛擬網路 VPN 結束點所在才能控制及最佳化 azure Windows Server Active Directory 流量的網站。

- [IP 位址和 DNS](#BKMK_IPAddressDNS):

 - 使用設定 AzureStaticVNetIP Azure PowerShell 指令程式來設定，亦即的靜態 IP 位址。
 - 在安裝及設定 Windows Server DNS 網域控制站 Azure 上。
 - 設定虛擬網路內容的名稱和 VM 裝載的 DC 和 DNS 伺服器角色的 IP 位址。

- [地理分散式 Dc](#BKMK_DistributedDCs)︰ 視需要設定其他虛擬網路。 如果您的 Active Directory 網站拓撲需要 Dc 會對應到不同的 Azure 區域，比您想要據以建立 Active Directory 網站的地區。

- [唯讀 Dc](#BKMK_RODC)︰ 您可能會部署 RODC Azure 網站中的，根據您的需求執行的作業，亦即和應用程式與服務的相容性在中撰寫 Rodc，網站。 如需有關應用程式的相容性的詳細資訊，請參閱[唯讀網域控制站應用程式的相容性指南](https://technet.microsoft.com/library/cc755190)。

- [通用類別目錄](#BKMK_GC)︰ Gc 需要在多網域樹系服務登入要求。 如果您不要部署 GC Azure 網站中的，您就會產生出口流量成本，為驗證要求會造成其他網站中的查詢 Gc。 最小化資料，您可以啟用萬用群組的成員資格快取 Azure Active Directory 網站和服務的網站。

    如果您部署 GC，設定網站連結和網站的連結成本，以便在 Azure 網站 GC 並不需要複寫相同的部分網域分割其他 Gc 慣用做為來源 DC。

- [Windows Server AD DS 資料庫和 SYSVOL 的位置](#BKMK_PlaceDB)︰ 新增 Dc Azure Vm 上執行，才能儲存 Windows Server Active Directory 資料庫、 記錄，以及 SYSVOL 資料磁碟。

- [備份與還原](#BKMK_BUR)︰ 決定您要儲存備份系統狀態的位置。 如有需要，請儲存備份的 DC vm 新增另一個資料磁碟。

## <a name="deployment-decisions-and-factors"></a>部署決策及因素

下表摘要列出在前一個案例和對應的決策，請考慮，與下方的更多詳細資料的連結會影響的 Windows Server Active Directory 技術區域。 部分技術區域可能無法用於每個部署案例中，與一些技術區域可能會更重要部署案例其他技術區域。

例如，如果部署虛擬網路上的複本 DC 樹系有一個網域，然後選擇要部署通用類別目錄伺服器在此情況下不會部署案例的關鍵因為它會建立任何其他複寫需求。 相反地，如果樹系有多個網域，然後決定是否来部署通用類別目錄虛擬網路上的可能會影響 [可用的頻寬，效能、 驗證、 目錄查詢，以及等等。

| Windows Server Active Directory 技術區域 | 決策 | 因素 |
| ---- | ---- | ---- |
| [網路拓撲](#BKMK_NetworkTopology) | 建立虛擬網路？ | <li>若要存取 Corp 資源的需求</li> <li>驗證</li> <li>帳戶管理</li> |
| [DC 部署設定](#BKMK_DeploymentConfig) | <li>部署個別的樹系沒有任何信任嗎？</li> <li>將部署新的樹系同盟功能？</li> <li>將部署新的樹系與 Windows Server Active Directory 樹系信任或 Kerberos？</li> <li>延伸 Corp 樹系部署複本 DC 嗎？</li> <li>延伸 Corp 樹系部署新的子網域或網域樹狀目錄嗎？</li> | <li>安全性</li> <li>法規遵循</li> <li>成本</li> <li>恢復功能和容錯能力</li> <li>應用程式的相容性</li> |
| [Windows Server Active Directory 網站拓撲](#BKMK_ADSiteTopology) | 如何與最佳化流量和最小化成本的 Azure 虛擬網路設定子網路、 網站和網站的連結？ | <li>子網路與網站定義</li> <li>網站內容] 連結，並變更通知</li> <li>複寫壓縮</li> |
| [IP 位址和 DNS](#BKMK_IPAddressDNS) | 如何設定 IP 位址和名稱解析？ | <li>使用使用設定 AzureStaticVNetIP cmdlet 來指派的靜態 IP 位址</li> <li>安裝 Windows Server DNS 伺服器，並設定虛擬網路內容的名稱和 VM 裝載的 DC 和 DNS 伺服器角色的 IP 位址</li> |
| [地理分散式 Dc](#BKMK_DistributedDCs) | 如何在不同的虛擬網路上複製到 Dc？ | 如果您的 Active Directory 網站拓撲需要 Dc 對應到不同的 Azure 區域，比您想要據以建立 Active Directory 網站的地區。 若要在個別的虛擬網路上的網域控制站之間複製[設定虛擬網路虛擬網路連線](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。 |
| [唯讀 Dc](#BKMK_RODC) | 使用唯讀或可寫入的 Dc 嗎？ | <li>篩選 HBI/PII 屬性</li> <li>篩選機密資料</li> <li>限制外寄流量</li> |
| [通用類別目錄](#BKMK_GC) | 安裝通用類別目錄嗎？ | <li>單一網域樹系中，讓所有的 Dc Gc</li> <li>為多網域樹系，Gc 所需的驗證</li> |
| [安裝方法](#BKMK_InstallMethod) | 如何安裝 DC Azure 中？ | 其中一個︰ <li>安裝 Windows PowerShell 或帶領 AD DS</li> <li>移動內部部署虛擬 DC 的 VHD</li> |
| [Windows Server AD DS 資料庫和 SYSVOL 的位置](#BKMK_PlaceDB) | Windows Server AD DS 資料庫、 記錄和 SYSVOL 的儲存位置？ | 變更 Dcpromo.exe 預設值。 這些重要 Active Directory 檔案*必須*放在 Azure 資料磁碟上，而不是實作寫入快取的作業系統磁碟。 |
| [備份與還原](#BKMK_BUR) | 如何保護及復原資料？ | 建立系統狀態備份 |
| [同盟伺服器設定](#BKMK_FedSrvConfig) | <li>將部署新的樹系在雲端的同盟功能？</li> <li>部署 AD FS 內部部署和雲端中的 proxy 的方式公開嗎？</li> | <li>安全性</li> <li>法規遵循</li> <li>成本</li> <li>Access 應用程式的商業夥伴</li> |
| [雲端服務設定](#BKMK_CloudSvcConfig) | 在雲端服務會隱含地部署第一次建立虛擬機器。 您需要部署其他雲端服務嗎？ | <li>VM 或 Vm 需要直接揭露至網際網路？</li> <li> 時，不需要負載平衡服務嗎？</li> |
| [公用與私人 IP 位址 (與虛擬 IP 動態 IP) 同盟伺服器需求](#BKMK_FedReqVIPDIP) | <li>Windows Server AD FS 執行個體需要直接從網際網路連線到嗎？</li> <li>在雲端部署的應用程式時，不需要它自己的網際網路的 IP 位址和連接埠嗎？</li> | 建立所需的部署每一個虛擬 IP 位址的一個雲端服務 |
| [Windows Server AD FS 可用性設定](#BKMK_ADFSHighAvail) | <li>在我的 Windows Server AD FS server 伺服器陣列中的節點多少？</li> <li>在 [我的 Windows Server AD FS proxy 伺服器陣列中部署多少節點？</li> | 恢復功能與容錯 |

### <a name="BKMK_NetworkTopology"></a>網路拓撲

若要符合的 IP 位址一致性和 Windows Server AD DS DNS 需求，就必須先建立[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)，然後附加您的虛擬機器。 在其建立時，您必須決定是否要您也可以延伸 [連線至內部部署公司網路，無障礙連線到內部部署電腦的 [Azure 虛擬機器，這用傳統 VPN 技術來達成，而且需要 VPN 端點的公司網路的邊緣上公開。 就是要有 vpn 才能從 Azure 起始公司網路、 倒過來。

請注意，擴充至內部部署網路套用至每個 VM 的標準費用超過虛擬網路時，套用額外的費用。 具體來說，還有費用 Azure 虛擬網路閘道 CPU 時間，以及每個 VM 透過 VPN 通訊與內部部署機器產生的出口流量。 如需有關網路流量費用的詳細資訊，請參閱[Azure 價格，然](http://azure.microsoft.com/pricing/)。

### <a name="BKMK_DeploymentConfig"></a>DC 部署設定

設定，亦即的方式取決於您想要執行 Azure 服務的需求。 例如，您可能會部署新樹系，從您自己的公司樹系，以進行測試的概念，新的應用程式或需要目錄服務，但不是特定存取內部企業資源的一些其他短期專案隔離。

為的優勢，亦即不會與複寫隔離樹系內部部署 Dc，結果較少輸出系統本身，所產生的網路流量的直接降低成本。 如需有關網路流量費用的詳細資訊，請參閱[Azure 價格，然](http://azure.microsoft.com/pricing/)。

另一個範例，假設您有隱私權需求服務，但服務取決於您內部 Windows Server Active Directory 存取。 如果您允許主機資料在雲端服務，您可能會部署新的子網域為內部樹系 Azure 上。 在此情況下，您可以部署 DC，新的子網域 （不含通用類別目錄以協助解決隱私權問題）。 這個案例中的，以及複本 DC 部署需要虛擬網路與您內部部署的 Dc 的連線。

如果您建立新的樹系，選擇是否要使用[Active Directory 信任](https://technet.microsoft.com/library/cc771397)或[同盟信任](https://technet.microsoft.com/library/dd807036)。 [餘額取決於 [相容性、 安全性、 規範、 成本，以及恢復的需求。 例如，利用[選擇性驗證](https://technet.microsoft.com/library/cc755844)您可能會選擇部署新樹系上 Azure 及 Windows Server Active Directory 之間建立信任的內部部署樹系和森林。 如果宣告注意應用程式，不過，您可能會部署，而不是 Active Directory 樹系信任的同盟信任。 其他因素會複寫延長您內部部署 Windows Server Active Directory 至雲端的更多資料或產生當做驗證與查詢載入更多外寄流量的成本。

可用性與容錯需求也會影響您的選擇。 例如，如果中斷的連結，運用 Kerberos 信任或同盟的應用程式信任的所有可能完全無法進行運算除非您已部署足夠 Azure 基礎結構。 替代的部署設定，例如複本 Dc (可寫入或 Rodc) 增加可容許連結中斷的機率。

### <a name="BKMK_ADSiteTopology"></a>Windows Server Active Directory 網站拓撲

您需要正確定義的項目才最佳化流量與成本最小化網站連結。 網站、 網站連結和子網路影響複寫拓撲之間 Dc 和驗證流量傳送。 請考慮下列資料傳輸費用，然後部署及設定網域控制站根據您的部署案例的需求︰

- 有 nominal 每小時本身的閘道器的費用︰

 - 啟動和停止適時

 - 停止，Azure Vm 與公司網路隔離

- 輸入的流量是免費

- 根據[Azure 價格，然](http://azure.microsoft.com/pricing/)，負責外寄流量。 您可以最佳化網站內部部署與雲端網站之間的網站連結屬性，如下所示︰

 - 如果您使用多個虛擬網路，設定網站連結和成本妥當之後，移到可提供的免費服務相同層級的其中一個排序 Azure 網站時，防止 Windows Server AD DS。 您也可以考慮停用所有網站連結 (BASL)] 選項 （預設為啟用） 橋。 如此一來，可確保只有直接連接的網站複製彼此。 可轉移連線的網站中的 Dc 不再能夠彼此直接，但必須複製到公用網站或網站。 如果基於某種原因中繼網站無法使用，即使有網站之間的連線，將不會發生 Dc 間接連線網站之間。 最後，其中的轉移複製行為的節會保留理想的建立網站包含適當的網站連結和網站，例如內部部署，公司網路網站的連結橋。

 - [設定網站連結成本](https://technet.microsoft.com/library/cc794882)適當為了避免非預期的流量。 例如，如果**嘗試下一個最接近的網站**設定已啟用，請確定虛擬網路網站不是 [下一步最接近增加連線至公司網路的 Azure 網站的網站連結物件相關聯的成本。

 - 設定網站連結[的時間間隔](https://technet.microsoft.com/library/cc794878)與[排程](https://technet.microsoft.com/library/cc816906)依據一致性需求以及物件變更率。 對齊複寫排程與延遲。 Dc 複寫僅最後一個值的狀態，讓減少複寫間隔可以儲存成本，如果有足夠的物件變更工資率。

- 如果最小化成本優先順序，請確定複寫排程，而不會啟用變更通知。 複寫之間網站時，這是預設的設定。 這是不重要事項如果您部署虛擬網路上的 RODC，因為 RODC 不會複寫輸出的任何變更。 但如果您部署可寫入的 DC，請確定 [網站] 連結未設定為複寫更新不必要的頻率。 如果您部署通用類別目錄伺服器 (GC)，請確定包含 GC 的其他每個網站會將網域分割複製來源據點的已連線的網站連結或 Azure 網站中有低的成本大於 GC 的網站連結的網站。


- 很可能仍進一步減少產生複寫網站變更複寫壓縮演算法間的網路流量。 壓縮演算法會控制呼叫完成登錄項目 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator 壓縮演算法。 預設值是 3，這與 Xpress 壓縮演算法相關聯。 您可以值變更為 2，可變更演算法來 MSZip。 在大多數情況下，這會增加壓縮，但它會犧牲 CPU 使用率。 如需詳細資訊，請參閱[如何 Active Directory 複寫拓撲運作](https://technet.microsoft.com/library/cc755994)。

### <a name="BKMK_IPAddressDNS"></a>IP 位址和 DNS

Azure 虛擬機器會依預設配置 「 DHCP 租用地址 」。 符合需求的 Windows Server AD DS 的虛擬機器生命週期與虛擬機器保存 Azure 虛擬網路動態地址，因為。

如此一來，您可以使用動態位址 Azure 上，您就使用靜態 IP 位址，因為它舉例來說段租用，而且租用段等於雲端服務的存留時間的效果。

不過，如果 VM 是關閉的配置動態地址。 若要防止被取消配置的 IP 位址，您可以[使用設定-AzureStaticVNetIP 指派的靜態 IP 位址](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx)。

名稱解析，部署您自己 （或運用您現有） DNS 伺服器基礎結構。Azure 所提供的 DNS 不符合 Windows Server AD DS 的進階的名稱解析需求。 例如，不支援動態 SRV 記錄，然後等。 名稱解析是 Dc 和網域的用戶端的要徑設定項目。 Dc 必須能夠註冊資源記錄，以及解決其他 DC 資源記錄。
故障了和效能因素，最好是 Azure 上執行的 dc 安裝 Windows Server DNS 服務。 然後設定 Azure 虛擬網路內容的名稱和 DNS 伺服器 IP 位址。 虛擬網路上的其他 Vm 啟動時，其 DNS 用戶端解析程式設定將會設定與 DNS 伺服器動態 IP 位址配置的一部分。

> [AZURE.NOTE] 您無法在網際網路上直接裝載於 Azure 上的 Windows Server AD DS Active Directory 網域加入內部部署電腦。 連接埠需求的 Active Directory 和網域聯結作業呈現適合直接公開必要的連接埠和效果，整個 DC 至網際網路。

Vm 註冊其 DNS 名稱自動啟動或名稱變更時。

如需有關本範例，並示範如何佈建的第一個 VM，並在其上安裝 AD DS 的另一個範例的詳細資訊，請參閱[安裝新的 Microsoft Azure Active Directory 樹系](active-directory-new-forest-virtual-machine.md)。 如需有關如何使用 Windows PowerShell 的詳細資訊，請參閱[安裝 Azure PowerShell](../powershell-install-configure.md)和[Azure 管理 Cmdlet](https://msdn.microsoft.com/library/azure/jj152841)。

### <a name="BKMK_DistributedDCs"></a>地理分散式 Dc

Azure 裝載在不同的虛擬網路上的多個 Dc 時，有優點︰

- 多網站容錯能力

- 實體靠近分公司 （較低延遲）

設定直接虛擬網路之間的通訊的相關資訊，請參閱[設定虛擬網路虛擬網路連線能力](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

### <a name="BKMK_RODC"></a>唯讀 Dc

您需要選擇是否要部署唯讀或可寫入的 Dc。 您可能需要部署 Rodc，因為您不會有實體控制，而 Rodc 設計用來部署其實體安全性的風險，例如分公司的位置。

Azure 不會顯示實際安全性風險的分支 office，但 Rodc 可能仍證明為成本更有效率，因為他們所提供的功能非常適合這些環境，雖然十分不同的原因。 例如，Rodc 有沒有輸出複寫，可以選擇性地填入機密資訊 （密碼）。 在缺點，這些機密缺乏可能需要視需要為使用者進行驗證的外寄流量或電腦。 但機密資料可以選擇性地預先填入和快取。

Rodc 提供的其他優點及 HBI 和 PII 問題，因為您可以新增包含 rodc 機密資料的屬性篩選屬性設定 (FAS)。 FAS 是一組可自訂不會複寫到 Rodc 的屬性。 您可以使用 FAS 以保護，您不允許或不想要儲存 PII 或 HBI Azure 上。 如需詳細資訊，請參閱 [RODC 篩選的屬性設定 [(https://technet.microsoft.com/library/cc753459)]。

請確定應用程式會與您打算使用的 Rodc 相容。 許多 Windows Server Active Directory 啟用應用程式適用於 Rodc，但部分應用程式可以執行效率降低或失敗，如果沒有可寫入的 DC 存取。 如需詳細資訊，請參閱[唯讀 Dc 應用程式的相容性指南](https://technet.microsoft.com/library/cc755190)。

### <a name="BKMK_GC"></a>通用類別目錄

您需要選擇是否要安裝通用類別目錄 (GC)。 在單一網域樹系，您應設定所有 Dc 為通用類別目錄伺服器。 它不會增加成本，因為不會有任何額外的複寫流量。

在多網域樹，Gc 的驗證程序時展開標準群組的成員資格。 如果您不要部署 GC，驗證 DC 上 Azure 虛擬網路上的工作負載間接會產生若要在每個驗證嘗試查詢 Gc 內部部署的輸出驗證流量。

Gc 相關聯的成本會較預測，因為他們主控 （部分） 的每個網域。 如果工作負載裝載具網際網路服務，以及驗證針對 Windows Server AD DS 的使用者，可能完全無法預期的成本。 若要降低雲端網站以外的 GC 查詢驗證時，您可以[啟用快取的通用群組的成員資格](https://technet.microsoft.com/library/cc816928)。

### <a name="BKMK_InstallMethod"></a>安裝方法

您需要選擇虛擬網路上安裝網域控制站的方式︰

- 升階新 Dc。 如需詳細資訊，請參閱[安裝新的 Active Directory 樹系 Azure 虛擬網路上](active-directory-new-forest-virtual-machine.md)。

- 移動內部部署虛擬 DC 的 VHD 至雲端。 在此情況下，您必須確定的內部部署虛擬 DC 」 移，「 不 」 複製 」 或 「 複製 」。

使用只 Azure 虛擬機器 Dc （而不是 Azure 」 web 」 或 「 工作 」 角色 Vm）。 他們是長期，而且是必要的 dc 狀態的持續性。 Azure 虛擬機器專為例如 Dc 的工作量。

不要使用 SYSPREP 可以部署或複製的 Dc。 複製 Dc 功能僅提供開始在 Windows Server 2012 中。 [複製] 功能需要在基礎 hypervisor VMGenerationID 支援。 在 Windows Server 2012 和 Azure 虛擬的網路中的 [HYPER-V 兩者都支援 VMGenerationID，，如同協力廠商虛擬化軟體廠商所做的。

### <a name="BKMK_PlaceDB"></a>Windows Server AD DS 資料庫和 SYSVOL 的位置

選取要找出 Windows Server AD DS 資料庫、 記錄和 SYSVOL 的位置。 他們必須部署 Azure 資料磁碟上。

> [AZURE.NOTE] Azure 資料磁碟限制以 1TB。

資料磁碟機執行預設動作不快取寫入。 附加至 VM 使用寫入到快取資料磁碟機。 撰寫透過快取會讓確定寫入是認可長期 Azure 儲存體之前交易已完成的 VM 作業系統的觀點。 它會提供持續性，稍微慢寫入犧牲。

這是很重要的 Windows Server AD DS，因為寫入置磁碟快取失效假設所做的 DC。 Windows Server AD DS 嘗試停用寫入快取，但是由磁碟 IO 系統接受它。 若要停用寫入快取失敗可能，在某些情況下，會造成 USN 復原導致延遲物件和其他問題。

最佳作法是虛擬 dc，執行下列動作︰

- 設定主機快取喜好設定] 設定 Azure 資料磁碟上無。 這可防止寫入快取 AD DS 作業的問題。

- 儲存資料庫、 記錄和 SYSVOL 上有相同資料磁碟或個別的資料磁碟。 一般而言，這是用作業系統本身的磁碟從另一個磁碟。 心得是的 Windows Server AD DS 資料庫及 SYSVOL 不能儲存於 Azure 作業系統的磁碟型別。 根據預設，AD DS 安裝程序，請在 %systemroot%資料夾中，建議您不要 Azure 安裝下列元件。

### <a name="BKMK_BUR"></a>備份與還原

請注意的是什麼，不支援的備份與還原的 DC 一般而言，以及更明確地說，在 VM 中執行。 請參閱[備份與還原虛擬 dc 的考量事項](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers)。

建立備份狀態系統只備份軟體，特別是知道的 Windows Server AD DS，例如 Windows Server 備份的備份需求。

請勿複製或複製的 Dc VHD 檔案，而不是執行定期備份。 還原應該有必要，這樣才能使用沒有 Windows Server 2012 與支援的 hypervisor 複製或複製 Vhd 會介紹 USN 泡泡圖。

### <a name="BKMK_FedSrvConfig"></a>同盟伺服器設定

設定 Windows Server AD FS 同盟伺服器 (Sts) 的部分根據您想要部署 Azure 的應用程式是否需要存取您的內部網路上的資源。

如果應用程式符合下列條件，您可以將隔離的應用程式部署從您的內部部署網路。

- 對方接受 SAML 安全性權杖

- 他們的年齡 exposable 到網際網路

- 不會存取內部部署資源

在此情況下，設定 Windows Server AD FS Sts，如下所示︰

1. 設定 Azure 隔離的單一網域樹系。

2. 設定 Windows Server AD FS 同盟伺服器陣列來提供樹系同盟的存取。

3. 在內部部署樹系設定 Windows Server AD FS （同盟伺服器陣列和同盟伺服器 proxy 伺服器陣列）。

4. 建立之間的內部部署和 Azure 的執行個體的 Windows Server AD FS 同盟信任關係。

另一方面，如果應用程式要求存取內部部署資源，您可以設定 Windows Server AD FS Azure 上的應用程式，如下所示︰

1. 設定內部網路和 Azure 之間的連線。

2. 在內部部署樹系設定 Windows Server AD FS 同盟伺服器陣列。

3. 設定 Windows Server AD FS 同盟伺服器 proxy 伺服器陣列上 Azure。

此設定的優點是能夠減少的公開內部部署資源，類似於 Windows Server AD FS 設定周邊網路中的應用程式。

請注意，在任一案例中，您可以建立信任關係更多的身分識別提供者，視企業對企業共同作業。

### <a name="BKMK_CloudSvcConfig"></a>雲端服務設定

雲端服務是如果您想要公開 VM 直接到網際網路，或網際網路的負載平衡應用程式的方式公開。 這可能是因為每個雲端服務可提供單一可設定虛擬 IP 位址。

### <a name="BKMK_FedReqVIPDIP"></a>公用與私人 IP 位址 (與虛擬 IP 動態 IP) 同盟伺服器需求

每個 Azure 虛擬機器收到動態 IP 位址。 動態 IP 位址是私人位址只能內 Azure 存取。 在大多數情況下，不過，它會需要設定您的 Windows Server AD FS 部署虛擬 IP 位址。 虛擬 IP 需要公開至網際網路，Windows Server AD FS 結束點，而將供同盟的合作夥伴和客戶的驗證及持續管理。 虛擬的 IP 位址是雲端服務，其中包含一或多個 Azure 虛擬機器的屬性。 如果部署 Azure 及 Windows Server AD FS 宣告注意應用程式共用一般的連接埠和具網際網路，每個會要求其本身的虛擬 IP 位址和，因此必須建立一個雲端服務應用程式和 Windows Server AD fs 秒。

定義的各項的虛擬 IP 位址和動態 IP 位址，請參閱[的術語與定義](#BKMK_Glossary)。

### <a name="BKMK_ADFSHighAvail"></a>Windows Server AD FS 可用性設定

部署獨立 Windows Server AD FS 同盟服務時，建議 AD FS STS] 及 [proxy 生產環境中部署至少兩個節點伺服器陣列。

請參閱[AD FS 2.0 部署拓撲考量](https://technet.microsoft.com/library/gg982489) [AD FS 2.0 設計指南](https://technet.microsoft.com/library/dd807036)決定最佳的部署設定選項符合您特定需求。

> [AZURE.NOTE] 若要取得的負載平衡 Azure 的 Windows Server AD FS 端點，設定所有成員的 Windows Server AD FS 伺服器陣列中相同的雲端服務，並使用負載平衡 Azure HTTP （預設值 80） 的功能和 HTTPS 連接埠 （預設值 443）。 如需詳細資訊，請參閱[Azure 負載平衡器探查](https://msdn.microsoft.com/library/azure/jj151530)。
Azure 不支援 Windows Server 網路負載平衡 (NLB)。
