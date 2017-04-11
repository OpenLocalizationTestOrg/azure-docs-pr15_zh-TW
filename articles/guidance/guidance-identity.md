<properties
   pageTitle="管理 Azure 中的身分識別 |Microsoft Azure"
   description="說明與比較橫跨在部署/雲端邊界與 Azure 的混合式系統管理身分識別可用的不同方法。"
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>管理 Azure 中的身分識別

根據 Windows 大部分的企業系統，您會使用 Active Directory (AD) 提供身分識別管理服務應用程式。 AD 運作，以及在內部部署環境中，但您在您擴充至雲端網路基礎結構時有一些重要的決策，讓 concerning 管理身分識別的方式。 您應該展開合併在雲端的 Vm 您內部部署的網域嗎？ 您應該在雲端，方式建立新的網域，如果是這樣，如何？ 應在雲端實作自己樹系或才可使用的 Azure Active Directory (AAD)？

本文將說明會議這個案例中，所帶來的挑戰的一些常見的選項，可協助您判斷哪一個解決方案最符合您的需求根據您的需求。

## <a name="using-azure-active-directory"></a>使用 Azure Active Directory

您可以使用 AAD 在雲端建立 AD 網域，並將其連結至內部部署 AD 網域。 AAD 可讓您設定單一登入 (SSO) 執行透過雲端存取的應用程式的使用者。

[![0]][0]

AAD 實作雲端中的安全性網域乾淨的簡單方法。 它會利用許多的 Microsoft 應用程式，例如 Microsoft Office 365。 

使用 AAD 的優點︰

- 有不需要保留在雲端的 Active Directory 基礎結構。 完全管理，由 Microsoft 維護 AAD。

- AAD 提供可用的內部部署相同身分識別的資訊。

- 在 Azure 減少外部應用程式和使用者連絡內部部署的網域就會發生驗證。

使用 AAD 時要考慮的事項︰

- 識別服務限於使用者和群組]。 沒有驗證服務及電腦帳戶的能力。

- 您必須使用您的內部部署網域保持 AAD 目錄同步處理設定連線。 

- 您負責發佈應用程式的使用者可以在透過 AAD 雲端存取。

詳細資訊，請參閱[實作 Azure Active Directory][implementing-aad]。

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>在雲端使用 Active Directory 加入至內部部署樹系

您也可以裝載 AD 目錄服務 (AD DS) 內部部署，但在應用程式的項目 Azure 中的所在位置的混合式案例它可以複製至雲端的這項功能和 AD 存放庫更有效率。 這種方法可協助降低傳送驗證所造成的延遲和雲端的本機的授權要求回至 AD DS 執行內部部署。 

[![1]][1]

這個方法需要您在雲端建立您自己的網域，並將其加入至內部部署樹系。 您建立 Vm 裝載 AD DS 服務。

使用不同的網域在雲端的優勢︰

- 可讓您驗證使用者、 服務和電腦帳戶內部部署和雲端中。

- 提供相同的身分識別資訊可用的內部部署的存取權。

- 管理個別的 AD 樹系; 不需要在雲端網域可以隸屬於內部部署樹系。

- 您可以套用群組原則所定義的內部部署 GPO 物件雲端中的網域。

使用不同的網域在雲端的考量︰

- 要求您建立及管理您自己的 AD DS 伺服器和雲端中的網域。

- 可能有一些同步處理之間的延遲雲端中的網域伺服器及執行內部部署的伺服器。

如需如何設定此結構，請參閱[延伸 Active Directory 目錄服務 （新增） 至 Azure][extending-adds]。

## <a name="using-active-directory-with-a-separate-forest"></a>使用另一個樹系的 Active Directory

執行 Active Directory (AD) 內部部署的組織可能樹系包含許多不同的網域。 您可以使用網域隔離功能區域的必須分開保存，可能是基於安全性理由，但您可以之間共用資訊建立信任關係的網域。

[![2]][2]

組織使用不同的網域，可以利用 Azure 來重新放置到另一個樹系雲端中的一或多個網域。 或者，組織可能會想要保留所有雲端資源邏輯這些保留內部部署，並在自己的目錄中，儲存雲端資源的相關資訊，也會保留在雲端樹系的一部分。

使用雲端中的個別的樹系的好處︰

- 您可以實作內部部署身分識別和分隔 Azure 專用的身分識別。

- 不需要從內部部署複寫 AD 樹系 Azure 減少網路延遲的效果。

考量︰

- 驗證在雲端的內部部署身分識別執行額外的網路*躍點*內部部署 AD 伺服器。

- 要求您自己 AD DS 伺服器且實作樹系在雲端，並建立樹系適當的信任關係。

[建立 Active Directory 目錄服務 （新增） 資源樹系 Azure 中]的文件[adds-forest-in-azure]說明如何實作這種方法的更多詳細資料。

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>使用 Active Directory Federation Services (ADFS) 與 Azure

ADFS 可以執行於內部部署，但在混合式部署的案例中 Azure 中的應用程式的所在位置可以是更有效率實作這項功能在雲端，如下所示。

[![3]][3]

此結構功能的尤其有用︰

- 利用同盟的授權公開合作夥伴的 web 應用程式的解決方案。

- 支援的系統執行組織防火牆以外的網頁瀏覽器存取。

- 讓使用者從授權外部的裝置，例如遠端電腦的筆記本，與其他行動裝置連線來存取 web 應用程式的系統。 

Azure 中使用 ADFS 優點︰

- 您可以利用宣告注意應用程式。

- 會提供信任外部合作夥伴進行驗證。

- 它會提供與大量驗證通訊協定的相容性]。

Azure 中使用 ADFS 考量︰

- 您需要在雲端實作您自己新增 ADFS，與 ADFS Web 應用程式 Proxy 伺服器。

- 此結構可複雜設定。

詳細資訊，請參閱[實作 Active Directory Federation Services (ADFS) Azure 中][adfs-in-azure]。

## <a name="next-steps"></a>後續步驟

下列資源說明如何執行本文所述架構。

- [實作 Azure Active Directory][implementing-aad]
- [延伸至 Azure 的 Active Directory 目錄服務 （新增）][extending-adds]
- [建立 Azure Active Directory 目錄服務 （新增） 資源樹系][adds-forest-in-azure]
- [實作 Azure Active Directory Federation Services (ADFS)][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "使用 Azure Active Directory 雲端身分識別架構"
[1]: ./media/guidance-identity/figure2.png "安全的混合式網路架構與 Active Directory"
[2]: ./media/guidance-identity/figure3.png "安全的混合式網路架構與另一個 AD 網域樹系"
[3]: ./media/guidance-identity/figure4.png "安全的混合式網路架構使用 ADFS"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md