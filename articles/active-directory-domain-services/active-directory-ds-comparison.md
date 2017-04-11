<properties
    pageTitle="Azure AD 網域服務︰ 比較 Azure AD 網域服務 DIY 網域控制站 |Microsoft Azure"
    description="比較 DIY 網域控制站的 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>如何決定是否 Azure AD 網域服務是最適合您的使用案例
Azure AD 網域服務可讓您不必擔心維護您的身分識別基礎結構部署中 Azure 基礎結構服務，您工作負載。 受管理的服務與一般的 Windows Server Active Directory 部署您部署及管理您自己的不同。 服務的設計輕鬆的部署、 自動化的健康監視及補救，以及雲端的簡單的身分識別基礎結構。 我們會不斷發展可新增常見部署案例的支援服務。

若要決定是否要使用 Azure AD 網域服務或向上微調並管理您自己 （自助） 中 Azure AD 基礎結構︰

- 請參閱[Azure AD 網域服務所提供的功能](active-directory-ds-features.md)的清單。

- 檢閱一般[Azure AD 網域服務的部署案例](active-directory-ds-scenarios.md)。

- 最後，您可以[比較自助 AD 選項的 Azure AD 網域服務](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure)。


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>比較 DIY AD 網域 Azure 中的 Azure AD 網域服務
下表可協助您決定要使用 Azure AD 網域服務與管理您自己 Azure AD 基礎結構。

|**功能**|**Azure AD 網域服務**|**Azure Vm 中的自助式' AD**|
|---|:---:|:---:|
|[**受管理的服務**](active-directory-ds-comparison.md#managed-service)|**& #x 2713;**|**& #x 2715;**|
|[**安全的部署**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713;**|管理員必須安全的部署。|
|[**DNS 伺服器**](active-directory-ds-comparison.md#dns-server)|**與 #x 2713;**（受管理的服務）|**& #x 2713;**|
|[**網域或企業系統管理員權限**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715;**|**& #x 2713;**|
|[**網域加入**](active-directory-ds-comparison.md#domain-join)|**& #x 2713;**|**& #x 2713;**|
|[**網域驗證使用 NTLM 和 Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713;**|**& #x 2713;**|
|[**自訂 OU 結構**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713;**|**& #x 2713;**|
|[**結構描述副檔名**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715;**|**& #x 2713;**|
|[**AD 網域樹系信任**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715;**|**& #x 2713;**|
|[**LDAP 讀取**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713;**|**& #x 2713;**|
|[**安全的 LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713;**|**& #x 2713;**|
|[**LDAP 寫入**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715;**|**& #x 2713;**|
|[**群組原則**](active-directory-ds-comparison.md#group-policy)|簡單|完整|
|[**地理分散式部署**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715;**|**& #x 2713;**|


#### <a name="managed-service"></a>受管理的服務
Azure AD 網域服務網域是由 Microsoft 管理。 您不必擔心修補更新與監控備份，確保您的網域的可用性。 管理工作都提供的服務 Microsoft Azure 您受管理的網域。

#### <a name="secure-deployments"></a>安全的部署
受管理的網域安全地鎖定依照 Microsoft 的安全性的最佳作法 AD 部署。 這些最佳做法是體驗工程和支援 AD 部署 AD 產品小組的數十年。 針對 diy 部署，您需要採取特定的部署步驟鎖定向下/保護您的部署。

#### <a name="dns-server"></a>DNS 伺服器
Azure AD 網域服務管理的網域包含受管理的 DNS 服務。 「 AAD DC 系統管理員] 群組的成員可以管理 DNS 的受管理的網域上。 此群組的成員提供完整的 DNS 管理權限的受管理的網域。 可以使用 [DNS 管理主控台' 包含在遠端伺服器管理工具 (RSAT) 套件執行 DNS 管理。

#### <a name="domain-or-enterprise-administrator-privileges"></a>網域或企業系統管理員權限
在 AAD DS 受管理的網域，不會提供這些提高權限的權限。 針對受管理的網域，就無法執行需要這些提高權限的權限，才能執行安裝的應用程式。 精簡的系統管理員權限可供稱為 「 AAD DC 管理員 」 委派的系統管理群組的成員。 這些權限包含設定 DNS 設定] 群組原則、 取得網域機器等系統管理員權限的權限。

#### <a name="domain-join"></a>加入網域
您可以加入虛擬機器類似的電腦加入至 AD 網域受管理的網域。

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>使用 NTLM 和 Kerberos 網域驗證
您可以使用 Azure AD 網域服務您公司的認證的受管理的網域驗證方法。 認證，並且使用您的 Azure AD 租用戶的同步處理。 同步處理的租用戶，如 Azure AD Connect 可確保 Azure ad 會同步處理所做的認證內部部署的變更。 使用 DIY 網域設定] 中，您可能需要設定內部部署帳戶樹系供使用者使用其公司認證驗證的網域信任關係。 或者，您可能需要設定 AD 複寫，以確保使用者密碼的同步處理至您 Azure 網域控制站的虛擬機器。

#### <a name="custom-ou-structure"></a>自訂 OU 結構
「 AAD DC 管理員 」 群組的成員可以建立自訂 Ou 中的受管理的網域。

#### <a name="schema-extensions"></a>結構描述副檔名
您無法延伸 Azure AD 網域服務管理網域的基本結構描述。 因此，無法消除，移至 AAD DS 網域依賴副檔名 AD 結構描述 （例如，使用者物件下新屬性） 應用程式。

#### <a name="ad-domain-or-forest-trusts"></a>AD 網域或樹系信任
若要設定其他網域 （連入/連） 的信任關係無法設定受管理的網域。 因此，例如資源樹系部署或位置不想要同步處理至 Azure AD 密碼的情況下的案例中無法使用 Azure AD 網域服務。

#### <a name="ldap-read"></a>LDAP 讀取
受管理的網域支援 LDAP 閱讀的工作量。 因此，您可以部署執行 LDAP 讀取受管理的網域作業的應用程式。

#### <a name="secure-ldap"></a>安全的 LDAP
您可以設定 Azure AD 網域服務，提供您受管理的網域，包括透過網際網路安全 LDAP 存取。

#### <a name="ldap-write"></a>LDAP 寫入
受管理的網域即是唯讀的使用者物件。 因此，應用程式的執行 LDAP 寫入作業屬性的使用者物件中，無法使用受管理的網域。 此外，使用者的密碼無法在中變更受管理的網域。 另一個範例是修改群組成員資格或群組內的受管理的網域，不允許的屬性。 不過，變更使用者屬性或 Azure AD （透過 PowerShell/Azure 入口網站） 中所做的密碼或內部部署 AD 會同步處理至 AAD DS 受管理的網域。

#### <a name="group-policy"></a>群組原則
複雜的群組原則架構，就不支援的 AAD DS 受管理的網域上。 例如無法建立及部署不同的 Gpo，針對每個網域中的自訂 OU 或使用 WMI 篩選 GP 指定目標。 沒有內建 GPO 每個 'AADDC 電腦 」 和 「 AADDC 使用者' 容器，以設定群組原則可自訂的。

#### <a name="geo-dispersed-deployments"></a>分散的地理部署
Azure AD 網域服務受管理的網域，可在單一虛擬網路 Azure 中。 需要網域控制站，會出現在多個 Azure 區域世界各地的情況下，設定網域中 Azure IaaS Vm 可能更適合用來。


## <a name="do-it-yourself-diy-ad-deployment-options"></a>「 Diy 」 (DIY) AD 部署選項
您可能需要部署的使用情況下，您需要 Windows Server AD 安裝所提供的功能。 在下列情況下，請考慮下列 diy (DIY) 選項︰

- **獨立雲端網域︰**您可以設定獨立的雲端網域使用已設定為網域控制站的 Azure 虛擬機器。 此基礎結構不整合您的內部部署 AD 環境。 這個選項需要不同的 「 雲端認證 」 集來登入/管理 Vm 雲端中的。

- **資源樹系部署︰**您可以設定的網域在資源樹系拓撲中，使用 [設定為網域控制站的 Azure 虛擬機器。 接下來，您可以使用您的內部部署設定 AD 信任關係 AD 環境。 您可以在雲端此資源樹系網域加入電腦 (Azure Vm)。 使用者驗證，這時請透過 VPN/ExpressRoute 連線至您的內部部署目錄。

- **將您的內部部署網域延伸到 Azure:**您可以 Azure 虛擬網路連線至您的內部部署網路使用 VPN/ExpressRoute 連線，以便 Azure Vm 可以加入至您的內部部署 AD。 升級您的內部部署網域為 VM Azure 中的複本網域控制站為另一個方法。 您可以再將其設定為複寫透過 VPN/ExpressRoute 連線至您的內部部署目錄。 此模式中部署有效延伸 Azure 您內部部署的網域。

> [AZURE.NOTE] 您可能會決定 DIY 選項更適合您部署使用案例。 考慮[共用的意見反應](active-directory-ds-contact-us.md)，協助我們瞭解功能可協助您在未來選擇 Azure AD 網域服務。 此意見反應可協助發展根據您的部署需求與使用情況的服務。

我們已發行[部署 Windows Server Active Directory Azure 虛擬機器上的商務連絡人的指導方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)可協助您更輕鬆地 DIY 安裝。


## <a name="related-content"></a>相關的內容
- [功能-Azure AD 網域服務](active-directory-ds-features.md)

- [部署案例-Azure AD 網域服務](active-directory-ds-scenarios.md)

- [部署 Windows Azure 虛擬機器上的伺服器 Active Directory 的指導方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)
