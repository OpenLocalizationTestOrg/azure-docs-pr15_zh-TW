<properties
   pageTitle="資料的安全性與加密的最佳作法 |Microsoft Azure"
   description="本文提供一組資料的安全性的最佳作法，加密使用內建於 Azure 功能。"
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Azure 資料的安全性及加密的最佳作法

在雲端的資料保護按鍵其中一項納入可能的狀態，可能是您的資料，以及哪些控制可供使用的狀態。 Azure 資料以安全性和加密的最佳作法建議將會下列資料的狀態︰

- 在其餘︰ 包括儲存物件、 容器及靜態存在於實體媒體類型會磁碟或光碟的所有資訊。

- 傳輸中︰ 當資料之間傳送元件、 位置或程式，例如透過網路，跨服務匯流排 （從內部部署至雲端，反之亦然，包括混合式部署連線，例如 ExpressRoute），或在輸入輸出過程中，它會認為中移動。

本文中，我們將討論各種 Azure 資料安全性和加密的最佳作法。 這些最佳做法被從我們的經驗 Azure 資料的安全性和加密與客戶像是您自己的體驗。

針對每個最佳作法，我們會說明︰

- 最佳作法是什麼
- 為什麼您要啟用的最佳作法
- 如果您無法啟用的最佳做法，可能結果
- 可能的替代方案的最佳作法
- 您如何可以瞭解如何啟用的最佳作法

Azure 資料安全性和加密的最佳作法本文會根據一致的意見和 Azure 平台功能的功能集，本文寫入的時間點。 意見與技術變更一段時間，本文會以反映那些變更定期更新。

Azure 資料安全性與加密最佳作法本文所述包括︰

- 強制執行多因素驗證
- 使用角色存取控制 (RBAC)
- 加密 Azure 虛擬機器
- 使用硬體安全性模型
- 使用安全工作站管理
- 啟用 SQL 資料加密
- 保護資料在傳輸時會
- 強制執行檔案層級的資料加密


## <a name="enforce-multi-factor-authentication"></a>強制執行多因素驗證

資料存取的第一個步驟和 Microsoft Azure 中的控制項是使用者進行驗證。 [Azure 多重因素驗證 (MFA)](../multi-factor-authentication/multi-factor-authentication.md)是一種方法來使用另一種方法比只使用者名稱和密碼驗證的使用者的身分。 此驗證方法可協助保護存取資料和會議的簡單登入程序的使用者需求時的應用程式。

藉由使用者啟用 Azure MFA，您要新增使用者登增益集及交易的第二個安全性層級。 在此情況下，交易也可能會存取位於在檔案伺服器或您在 SharePoint Online 中的文件。 Azure MFA 亦有助於減少洩漏的認證必須組織資料的存取權的 IT。

例如︰ 如果您強制 Azure MFA 為您的使用者，並將它設定為使用電話或文字訊息作為驗證，如果使用者認證受到，攻擊無法存取任何資源，因為其不會有存取權使用者的電話。 組織，不會將這個額外的身分識別保護新增會更容易受到認證盜用攻擊，這可能會導致資料入侵。

如果組織想要保留驗證控制項內部部署的一個替代方案是使用[Azure 多重因素驗證伺服器](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)，也稱為 MFA 內部部署。 使用此方法時您仍可使用多重因素驗證，同時保留 MFA server 內部部署。

如需 Azure MFA 的詳細資訊，請參閱[快速入門 Azure 多重因素驗證，在雲端](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)的文件。

## <a name="use-role-based-access-control-rbac"></a>使用角色存取控制 (RBAC)
限制存取根據[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的安全性原則。 這是必要的組織想要強制執行存取資料的安全性原則。 Azure 角色型存取控制 (RBAC) 可用於將權限指派給使用者、 群組和在特定範圍的應用程式。 訂閱、 [資源] 群組中或單一資源，可以是範圍的角色指派。

您可以使用[內建 RBAC 角色](../active-directory/role-based-access-built-in-roles.md)指派給使用者的權限的 Azure 中。 請考慮使用雲端運算子來管理儲存空間帳戶和管理傳統的儲存空間帳戶*傳統的儲存空間帳戶參與者*角色所需要的*儲存空間帳戶參與者*。 需要管理 Vm 和儲存帳戶的雲端運算子，請考慮將其新增至*虛擬機器參與者*的角色。

不會強制執行資料存取控制利用功能，例如 RBAC 的組織可能會提供以外所需的使用者的權限。 這可能會導致無法存取資料的不應該先有一些使用者資料入侵。

您可以進一步瞭解 Azure RBAC 閱讀[Azure Role-Based 存取控制](../active-directory/role-based-access-control-configure.md)的文件。

## <a name="encrypt-azure-virtual-machines"></a>加密 Azure 虛擬機器
大多數組織會[在其餘的資料加密](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)是資料的隱私權、 規範與資料主權必要步驟。 Azure 磁碟加密可讓 IT 系統管理員，來加密 Windows 和 Linux IaaS 虛擬機器 (VM) 磁碟。 Azure 磁碟加密運用業界標準 BitLocker 功能的 Windows 和 DM 窖功能的 Linux 提供大量加密 OS 和資料磁碟。

您可以利用 Azure 磁碟加密，以協助保護和保護您的資料以符合您組織的安全性與規範要求。 組織也必須考量使用加密，以協助降低風險未經授權的相關資料的存取權。 建議您加密之前撰寫機密資料給他們的磁碟機。

請確定您 VM 資料區及開機大量加密才能保護靜止 Azure 儲存體帳戶中的資料。 利用[Azure 金鑰保存庫](../key-vault/key-vault-whatis.md)來保護加密金鑰和密碼。

如需您內部部署的 Windows Server，請考慮下列的加密的最佳作法︰

- 使用[BitLocker](https://technet.microsoft.com/library/dn306081.aspx)加密資料
- 復原資訊儲存在 AD DS。
- 如果有任何問題，BitLocker 金鑰已洩露，我們建議您 [設定格式的磁碟機，若要移除的磁碟機 BitLocker 中繼資料的所有例項，或您解密並再次加密整個磁碟機。

不會強制執行資料加密的組織很可能公開資料完整性問題，例如惡意或惡意使用者竊取資料，並遭到盜用帳戶未經授權存取 [清除格式中的資料。 除了這類風險，他們的努力，而且增強資料安全性使用正確的安全性控制項，必須證明業界法規遵循有的公司。

您可以進一步瞭解 Azure 磁碟加密閱讀[Azure 磁碟加密 for Windows 和 Linux IaaS Vm](azure-security-disk-encryption.md)的文章。

## <a name="use-hardware-security-modules"></a>使用硬體安全性模組

產業加密解決方案使用密碼來加密資料。 因此，這是關鍵安全地儲存這些按鍵。 金鑰管理會變成資料保護，不可或缺的一部分，因為它會用來儲存密碼用來加密資料的索引鍵。

Azure 磁碟加密使用[Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)，可協助您控制與管理加密和密碼的重要保存庫訂閱，並確保在您 Azure 儲存體中其餘的加密磁碟虛擬機器中的所有資料。 您應該使用 Azure 金鑰保存庫稽核索引鍵和原則使用方式。

有許多固有的風險，相關沒有適當的安全性控制項，來保護私人按鍵用來加密資料的位置。 如果攻擊者有權存取私人按鍵，他們無法解密資料，並可能的機密資訊的存取。

您可以進一步瞭解憑證管理 Azure 中的一般建議閱讀，請參閱[Azure 中的憑證管理︰ 該做與不該做規則](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/)。

如需有關 Azure 金鑰保存庫的詳細資訊，請閱讀[Azure 金鑰保存庫快速入門](../key-vault/key-vault-get-started.md)。

## <a name="manage-with-secure-workstations"></a>使用安全工作站管理

大部分的攻擊目標使用者，由於端點會變成其中一個主要攻擊的點。 當攻擊端點，他可以利用使用者的認證以存取組織的資料。 大部分的端點攻擊就可以利用使用者是本機工作站系統管理員。

您可以使用安全管理工作站降低這類風險。 我們建議您以減少攻擊工作站中的使用[有權限存取工作站 （爪）](https://technet.microsoft.com/library/mt634654.aspx) 。 這些安全管理工作站可協助您減少這些攻擊可協助您確保您的資料是更安全。 請務必使用爪強化及鎖定工作站。 這是一個重要的步驟，提供高的安全性保證工作與資料保護機密帳戶。

端點保護缺乏可能風險放置資料，請務必跨用來取用資料，不論資料的位置 （雲端或內部部署） 的所有裝置強制執行安全性原則。

您可以瞭解更多關於權限來存取工作站閱讀[保護有權限存取](https://technet.microsoft.com/library/mt631194.aspx)的文件。

## <a name="enable-sql-data-encryption"></a>啟用 SQL 資料加密

[Azure SQL 資料庫透明資料加密](https://msdn.microsoft.com/library/dn948096.aspx)(TDE) 可協助防止惡意活動的潛在威脅執行即時加密和解密資料庫、 相關聯的備份和在其餘的交易記錄檔，而不需要的應用程式的變更。  TDE 加密整個資料庫的儲存空間，使用名為資料庫加密金鑰對稱索引鍵。

即使已加密整個儲存空間，務必也加密資料庫本身。 這是實作深層深度方法保護資料。 如果您使用的[Azure SQL 資料庫](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)，並想要保護信用卡或社會安全號碼等機密資料，您可以加密 FIPS 140-2 驗證 256 位元 AES 加密符合需求的許多業界標準 （例如 HIPAA、 PCI） 的資料庫。

請務必瞭解使用 TDE 加密資料庫時未加密[緩衝集區副檔名](https://msdn.microsoft.com/library/dn133176.aspx)(BPE) 相關的檔案。 您必須使用檔案系統層級的加密工具，例如 BitLocker[加密檔案系統](https://technet.microsoft.com/library/cc700811.aspx)(EFS) BPE 相關檔案。

自授權的使用者例如安全性系統管理員或資料庫的系統管理員可以存取資料即使資料庫加密的 TDE，您應該也遵循下列建議︰

- 在資料庫層級的 SQL 驗證
- Azure AD 驗證使用 RBAC 角色
- 使用者和應用程式應該使用不同的帳戶進行驗證。 如此一來，您可以限制權限授與使用者和應用程式，並減少惡意活動的風險
- 實作資料庫層級安全性使用固定的資料庫角色 （例如 db_datareader 或 db_datawriter），或者您可以建立自訂的角色，授與明確權限所選的資料庫物件的應用程式

不使用資料庫層級加密的組織可能會更容易受到攻擊可能會危害位於 SQL 資料庫中的資料。

您可以進一步瞭解 SQL TDE 加密閱讀[透明 Azure SQL 資料庫的資料加密](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)的文件。

## <a name="protect-data-in-transit"></a>保護資料在傳輸時會

保護資料在傳輸時會應該是您的資料保護策略不可或缺的一部分。 會從多個位置前或往後移動資料，因為一般建議您隨時都使用 SSL/TLS 通訊協定 exchange 資料的不同位置。 在某些情況下，您可能會想要找出您的內部部署與雲端之間的整個通訊通道透過虛擬私人網路 (VPN) 基礎結構。

您的內部部署基礎結構和 Azure 之間移動資料，您應該考慮 HTTPS 或 VPN 等適當的保護。

如果組織想要從多個工作站存取位於內部部署至 Azure 使用[Azure 網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

如需要安全存取 Azure 從一個工作站位於內部部署的組織使用[點對網站 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)。

大型資料集可以移動透過專用高速 WAN 連結，例如[ExpressRoute](https://azure.microsoft.com/services/expressroute/)。 如果您選擇使用 ExpressRoute，您也可以加密應用程式層級的資料使用[SSL/TLS](https://support.microsoft.com/kb/257591)或其他通訊協定新增保護。

如果您互動 Azure 儲存體透過 Azure 入口網站，所有的交易發生透過 HTTPS。 透過 HTTPS 的[儲存空間 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)可以用於互動[Azure 儲存體](https://azure.microsoft.com/services/storage/)和[Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)。

若要保護資料在傳輸時會失敗的組織是更容易受到[攔截攻擊](https://technet.microsoft.com/library/gg195821.aspx)、[竊取](https://technet.microsoft.com/library/gg195641.aspx)及工作階段攔截。 這些攻擊可存取機密資料的第一個步驟。

您可以瞭解更多關於 Azure VPN 選項閱讀文章︰[規劃與 VPN 閘道器的設計](../vpn-gateway/vpn-gateway-plan-design.md)。

## <a name="enforce-file-level-data-encryption"></a>強制執行檔案層級的資料加密

另一層可以增加安全性適合您的資料層級的保護加密檔案，不論檔案的位置。

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx)使用加密、 身分識別，並且授權的原則，以協助保護您的檔案及電子郵件。 Azure RMS 跨多個裝置 — 手機、 平板電腦及來保護您的組織內部和組織外的電腦。 此功能可能是保護的因為 Azure RMS 新增的仍會保留的資料，即使離開貴組織的邊界層級。

當您使用 Azure RMS 來保護您的檔案時，使用業界標準加密[FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html)的完整支援。 當您以保護資料運用 Azure RMS 時，即使會複製到不受控制的儲存空間有保護保持檔案，保證 IT，例如雲端儲存服務。 相同時才會發生透過電子郵件]，與他人共用的檔案受保護的檔案作為附件至電子郵件訊息，說明如何開啟受保護的附件。

規劃 Azure RMS 採用時建議下列動作︰

- 安裝[RMS 共用應用程式](https://technet.microsoft.com/library/dn339006.aspx)。 此應用程式整合了 Office 安裝的 Office 應用程式增益集，好讓使用者可以輕鬆地保護檔案直接。
- 設定為支援 Azure RMS 的應用程式及服務
- 建立反映您的業務需求的[自訂範本](https://technet.microsoft.com/library/dn642472.aspx)。 例如︰ 上方應該會套用在所有上方的密碼的私人資料的範本相關的電子郵件。

[資料分類](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)和檔案保護弱式的組織可能會更容易資料外洩。 不正確的檔案保護組織將無法取得商務獲得深入見解、 監視的濫用並避免惡意存取檔案。

您可以進一步瞭解 Azure RMS 閱讀[快速入門 Azure 版權管理](https://technet.microsoft.com/library/jj585016.aspx)的文件。
