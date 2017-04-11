<properties
    pageTitle="Azure AD Connect︰ 支援拓撲 |Microsoft Azure"
    description="本主題詳述支援與不受支援拓撲 Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>拓撲 Azure ad 連線
本主題的目標是來說明不同內部部署與 Azure AD 拓撲關鍵整合方案 Azure AD Connect 同步處理。 說明支援和不支援的設定。

圖例的文件中的圖片︰

描述 | 圖示
-----|-----
內部部署 Active Directory 樹系| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
篩選匯入的 active Directory| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Azure AD Connect 同步處理伺服器| ![同步處理](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD 連線同步處理伺服器 「 臨時模式 」| ![同步處理](./media/active-directory-aadconnect-topologies/LegendSync2.png)
使用 FIM2010 或 MIM2016 GALSync| ![同步處理](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Azure AD Connect 同步處理伺服器，詳細說明| ![同步處理](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Azure AD 目錄 |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
不支援的案例 | ![不支援](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>單一樹系，單一 Azure AD 租用戶
![單一樹系單一租用戶](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

最常見的拓撲是單一樹系內部部署，一或多個網域，與單一 Azure AD 租用戶。 Azure AD 驗證會使用密碼同步處理。 Azure AD Connect 的快速安裝支援這種拓撲。

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>單一樹系，一個 Azure AD 租用戶的多個同步處理伺服器
![單一樹系篩選不受支援](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

不支援有多個 Azure AD Connect 同步處理伺服器連線到相同的 Azure AD 租用戶，除了[臨時伺服器](#staging-server)。 即使這些已同步處理互斥一組物件時，它不受支援。 您可能會有這視為如果您無法達到樹系從單一伺服器，或將載入分散到多個伺服器中所有的網域。

## <a name="multiple-forests-single-azure-ad-tenant"></a>多個樹系單一 Azure AD 租用戶
![多重樹系單一租用戶](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

許多組織中有多個內部部署 Active Directory 樹系的環境。 有各種不同的原因有一個以上的內部部署 Active Directory 樹系。 一般範例是設計與帳戶資源樹系和結果合併或收購之後。

當您有多個樹系、 所有樹系必須是以單一 Azure AD Connect 連線到伺服器同步處理。 您沒有伺服器加入網域。 如有必要達到所有樹系，伺服器放置在網路 DMZ。

Azure AD Connect 安裝精靈提供幾個選項來合併彙算中多個樹系表示的使用者。 目標是一次在 Azure AD 中只以表示使用者。 有一些您可以在安裝精靈中的自訂安裝路徑中設定的一般拓撲。 選取代表您拓撲**可唯一識別您的使用者**] 頁面上的對應選項。 使用者只能設定合併彙算。 重複的群組的預設設定，但沒有合併。

下一節中討論常見拓撲︰[個別拓撲](#multiple-forests-separate-topologies)[完整網狀](#multiple-forests-full-mesh-with-optional-galsync)，與[帳戶資源](#multiple-forests-account-resource-forest)。

假設中 Azure AD Connect 同步處理的預設設定︰

1. 使用者有一個已啟用的帳戶，此帳戶的所在位置的樹系會用於驗證使用者。 此假設是兩個密碼同步處理，以及同盟。 UserPrincipalName 和 sourceAnchor/immutableID 來自此樹系。
2. 使用者會有一個信箱。
3. 樹系裝載的使用者信箱的有最佳的資料品質，顯示在 Exchange 全域通訊清單 (GAL) 的屬性。 如果使用者有沒有信箱，然後任何樹系可用來參與這些屬性的值。
4. 如果您有連結的信箱，然後還有另一個帳戶不同樹系用來登入。

如果您的環境不符合這些假設，會發生下列情況︰

- 如果您有多個作用中的帳戶或多個信箱，同步處理引擎會挑選其中一個，並忽略另。
- Azure ad，不會匯出連結的信箱，沒有作用中的帳戶。 使用者帳戶並不是任何群組的成員。 目錄同步的連結的信箱一律可表示為標準的信箱。 這項變更刻意是不同的行為，以更有效地支援多樹系案例。

了解[預設控制器](active-directory-aadconnectsync-understanding-default-configuration.md)可以找到更多詳細資料。

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>多個樹系、 一個 Azure AD 租用戶的多個同步處理伺服器
![不支援的多重樹系多同步處理](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

不支援有一個以上的 Azure AD 連線同步處理伺服器連線到單一 Azure AD 租用戶。 例外是使用[臨時伺服器](#staging-server)。

### <a name="multiple-forests--separate-topologies"></a>多個樹系 – 個別拓撲
**使用者都可以一次在所有的目錄**

![一次多重樹系使用者](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![多重樹系分離拓撲](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

在此環境中，所有樹系內部部署會被視為個別實體，使用者不會出現在任何其他樹系。
每個樹系有自己的 Exchange 組織，而且樹系之間有無 GALSync。 此拓撲可能之後合併/擷取或組織正每個業務單位隔離彼此的情況。 這些樹系 Azure AD 在相同組織中，而且顯示整合 GAL。
在此圖片]，在每個樹系中的每個物件一次表示斷，而在目標 Azure AD 租用戶中彙總。

### <a name="multiple-forests--match-users"></a>多個樹系 – 符合使用者
**使用者身分識別存在於跨多個目錄**

針對所有這些案例常見的通訊群組，而安全性群組最多可以包含的使用者、 連絡人及 FSPs （外部安全性原則）

FSPs 用於相加來表示成員的安全性群組中的其他樹系。 所有 FSPs Azure AD 都為解析成實際物件。

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>多個樹系 – 完整與選擇性 GALSync 的網狀
**使用者身分識別存在跨多個目錄。符合使用︰ 郵件屬性**

![多重樹系使用者的郵件](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![多重樹系完整網狀](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

完整網狀拓撲可讓使用者與資源位於任何樹系，通常有雙向信任樹系之間。

如果有多個樹系中 Exchange，您也可以選擇可能的內部部署 GALSync 解決方案。 所有其他樹系中的連絡人會以每位使用者。 經常使用 Forefront 識別管理員 2010年或 Microsoft 身分識別管理員 2016年來實作 GALSync。 Azure AD Connect 無法用於內部部署 GALSync。

在此案例中，身分識別的物件就會結合使用郵件屬性。 具有一個樹系的信箱的使用者已加入與其他的樹系連絡人。

### <a name="multiple-forests--account-resource-forest"></a>多個樹系 – 帳戶資源樹系
**使用者身分識別存在跨多個目錄。符合使用︰ ObjectSID 及 msExchMasterAccountSID 屬性**

![多重樹系使用者 ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![多重樹系 AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

在 [帳戶資源樹系拓撲，有一或多個帳戶樹系作用中的使用者帳戶。 您也會有一或多個資源樹系停用的帳戶。

在這個案例中一個 （或更多）**資源樹系**信任所有**帳戶樹系**。 資源樹系通常具有 Exchange 和 Lync 延伸的 AD 結構描述。 所有 Exchange 和 Lync 服務，以及其他共用的服務位於此樹系。 使用者此樹系中已停用的使用者帳戶和信箱已連結至帳戶樹系。

## <a name="office-365-and-topology-considerations"></a>Office 365 和拓撲考量
有些 Office 365 工作負載受支援拓撲需要特定限制。 如果您打算使用下列其中一個帳戶，然後讀取受支援的拓撲主題的工作量。

工作負載 |  
--------- | ---------
Exchange Online | 如果有多個 Exchange 組織內部部署 （也就是 Exchange 已部署至多個樹系），則必須使用 Exchange 2013 SP1 或更新版本。 您可以在這裡找到詳細資料︰[與多個 Active Directory 樹系的混合式部署](https://technet.microsoft.com/library/jj873754.aspx)
商務用 Skype | 如果您使用多個樹系內部部署，只帳戶資源樹系拓撲是受支援。 詳細資料的受支援的拓撲可以在這裡找到︰[用 Skype 商務 Server 2015 的環境需求](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>執行伺服器
![執行伺服器](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect 支援安裝**臨時模式**中的第二個伺服器。 在此模式下伺服器讀取所有連線目錄中的資料，但不會寫入任何項目至連線目錄。 它會使用標準的同步處理循環圖，因此具有 [身分識別資料的更新的複本。 毀壞位置主要伺服器失敗，您可能無法透過臨時伺服器。 Azure AD Connect 精靈中執行此動作。 此第二個伺服器最好位於不同的資料中心，因為沒有基礎結構中共用的主要伺服器。 您必須手動複製到第二個伺服器主要伺服器上所做的任何設定變更。

暫存伺服器也可用來測試新的自訂設定，以及它對您的資料產生的效果。 您可以預覽變更，並調整設定。 當您滿意新的設定時，您可以請臨時伺服器作用中的伺服器，並集舊作用中的伺服器臨時模式。

這個方法可以用於取代作用中的同步處理伺服器。 準備新的伺服器和集臨時模式。 請確定它是很好的狀態，停用臨時模式 （讓作用中），然後關閉目前使用中的伺服器。

當您想要在不同的資料中心有多個備份有一個以上的暫存伺服器可能是。

## <a name="multiple-azure-ad-tenants"></a>多個 Azure AD 租用戶
Microsoft 建議 Azure AD 為組織中有一個租用戶。
您打算使用多個 Azure AD 租用戶之前，這些主題涵蓋常見的案例，讓您可以使用單一租用戶。

主題 |  
--------- | ---------
使用管理單位的委派 | [Azure AD 中的系統管理單位管理](active-directory-administrative-units-management.md)

![多重樹系多重租用戶](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

有 1:1 之間的關聯性的 Azure AD Connect 同步處理伺服器和 Azure AD 租用戶。 每個 Azure AD 租用戶，您需要一個 Azure AD Connect 同步處理伺服器安裝。 Azure AD 租用戶執行個體隔離的設計，而且中的使用者無法看到其他租用戶的使用者。 如果是這種分隔，然後這是受支援的設定，但否則，您應該使用單一 Azure AD 租用戶模型。

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Azure AD 租用戶中每一次的物件
![單一樹系篩選](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

在此拓撲，一個 Azure AD Connect 同步處理伺服器連線至每個 Azure AD 租用戶。 Azure AD Connect 同步處理伺服器必須設定篩選，各有一組互斥操作的物件。 例如，您可以範圍特定網域或 OU 每個伺服器。 DNS 網域可以只在單一註冊 Azure AD 租用戶。 Upn 的內部部署使用者 AD 必須使用不同的命名空間。 例如，在圖片上三個不同的 UPN 尾碼登錄內部部署 AD: contoso.com、 fabrikam.com 和 wingtiptoys.com。 使用者在每一個內部部署 AD 網域使用不同的命名空間。

Azure AD 租用戶例項之間有無 GALsync。 通訊錄在 Exchange Online 和 Skype 適用於商務只會顯示使用者在相同的租用戶中。

這個拓撲有下列限制否則支援案例︰

- Azure AD 租用戶只有一個可以啟用 Exchange 混合式與內部部署的 Active Directory。
- 在 Windows 10 裝置只能與一個 Azure AD 租用戶相關聯。

互斥組物件的需求也適用於回寫。 某些回寫功能不支援此拓撲因為這些功能假設單一組態內部部署︰

-   群組回寫預設設定
-   裝置回寫

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>多次 Azure AD 租用戶中的每個物件
![不支援的單一樹系多重租用戶](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![不支援的單一樹系多重連接器](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- 它不支援同步處理相同的使用者，以多個 Azure AD 租用戶。
- 它不支援進行設定，若要讓使用者在一個 Azure AD 為另一個 Azure AD 租用戶中的連絡人顯示變更。
- 它不支援修改 Azure AD Connect 同步處理連線至多個 Azure AD 租用戶。

### <a name="galsync-by-using-writeback"></a>使用回寫 GALsync
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD 租用戶都隔離的設計。

- 它不支援變更 Azure AD Connect 同步處理的另一個 Azure AD 租用戶讀取資料的設定。
- 若要將使用者匯出為連絡人不支援的另一個內部部署 AD 使用 Azure AD Connect 同步處理。

### <a name="galsync-with-on-premises-sync-server"></a>GALsync 與內部部署同步處理伺服器
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

若要使用 FIM2010/MIM2016 內部部署的兩個 Exchange 組織之間 GALsync 使用者支援。 組織中的使用者顯示為外部使用者/連絡人其他組織中。 這些不同內部部署廣告然後同步處理到自己 Azure AD 租用戶。

## <a name="next-steps"></a>後續步驟
若要瞭解如何安裝這些案例 Azure AD Connect，請參閱[Azure AD Connect 自訂安裝](./connect/active-directory-aadconnect-get-started-custom.md)。

進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
