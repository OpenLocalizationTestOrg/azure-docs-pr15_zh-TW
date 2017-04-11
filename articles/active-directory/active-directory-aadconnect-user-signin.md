<properties
    pageTitle="Azure AD Connect︰ 使用者登入 |Microsoft Azure"
    description="Azure AD Connect 使用者的登入的自訂設定。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD 連線使用者的登入選項

Azure AD Connect 可讓您內部部署和雲端資源使用相同的密碼登入的使用者。 本文將說明您想要使用的您登入 Azure AD 每個身分識別模型，以協助您選擇的身分識別的重要概念。

如果您已經熟悉 Azure AD 身分識別模型，並想要深入瞭解特定的方法，請按一下下方適當主題。

* [密碼同步處理](#password-synchronization)
* [同盟的 SSO （使用 ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>選擇 [使用者登入方法
針對大部分的企業組織，只是想要啟用使用者登入 Office 365，SaaS 應用程式和其他 Azure AD 為主的資源，預設密碼同步處理建議選項。
在某些組織，然而，有特定的選項，例如 AD FS 同盟的登的原因。  這些功能包括︰

- 貴組織已有 AD FS 或 3rd 派對部署同盟提供者
- 您的安全性原則，禁止同步處理至雲端的密碼雜湊
- 您的使用者體驗順暢 SSO （不會提示其他密碼） 時需要從網域存取雲端資源聯結公司網路的電腦
- 您需要 AD FS 有一些特定功能
    - 使用協力廠商提供者或智慧卡 （深入了解協力廠商 MFA 提供者，在 Windows Server 2012 R2 AD fs） 的內部部署多重因素驗證
    - 作用中的目錄整合功能，例如柔帳戶鎖定或 AD 密碼和工作時間的原則
    - 同時條件存取內部部署與雲端使用的裝置註冊、 Azure AD 加入或 Intune MDM 原則的資源

### <a name="password-synchronization"></a>密碼同步處理
密碼同步處理的使用者密碼雜湊會從您內部部署的 Active Directory 同步 Azure AD。  時或變更密碼重設內部部署，新密碼同步處理至 Azure AD 立即讓使用者可以隨時使用相同的密碼雲端資源的內部部署後，如同。  密碼永不傳送至 Azure AD，也不儲存在 Azure AD 以純文字。
密碼同步處理可用與密碼回寫功能來啟用自動服務密碼重設 Azure AD 中。

<center>![雲端](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[關於密碼同步處理的詳細資訊](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>在 Windows Server 2012 R2 伺服器陣列中使用新的或現有的 AD FS 同盟
與同盟登入，您的使用者可以登入 Azure AD 基礎服務使用自己內部部署的密碼，在公司網路上, 不必重新輸入密碼。  AD FS 同盟選項可讓您部署新的或現有的 AD FS 在 Windows Server 2012 R2 伺服器陣列中。  如果您選擇要指定現有的伺服陣列，Azure AD Connect 會設定您的伺服器陣列和 Azure AD 之間的信任，以便您的使用者可以登入。

<center>![雲端](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>若要部署與 Windows Server 2012 R2 中 AD FS 同盟，您需要下列項目

如果您部署新的伺服器陣列︰

- 同盟伺服器的 Windows Server 2012 R2 伺服器。
- Windows Server 2012 R2 的 Web 應用程式 Proxy 伺服器。
- 您想要的同盟服務名稱，例如 fs.contoso.com.pfx 檔一個 SSL 憑證。

如果您是部署新的伺服器陣列，或使用現有的伺服陣列︰

- 同盟伺服器上的本機系統管理員認證。
- 在任何工作群組 （非加入網域） 上的本機系統管理員認證您打算將 Web 應用程式 Proxy 角色部署的伺服器。
- 您可以在其中執行精靈的電腦必須能夠連線到您要安裝 AD FS 或透過 Windows 遠端管理 Web 應用程式 Proxy 的任何其他電腦。

[設定與 AD FS 的 SSO](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>使用舊版的 AD FS 登入，或第三方解決方案

如果您已經有使用舊版的 AD FS （例如 AD FS 2.0) 或協力廠商同盟提供者設定雲端符號，您可以選擇透過 Azure AD Connect 跳使用者的登入設定。  這會讓您同時使用現有的方案登上取得最新的同步處理和 Azure AD Connect 其他功能。

[Azure AD 協力廠商同盟相容性清單](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>使用者登入和使用者主要名稱 (UPN)

### <a name="understanding-user-principal-name"></a>了解使用者主要名稱

在 Active Directory 中的預設 UPN 尾碼是網域的 DNS 名稱建立使用者帳戶。 在大部分情況下，這是在網際網路上的企業網域註冊的網域名稱。 不過，您可以新增更多使用 Active Directory 網域及信任的 UPN 尾碼。

使用者的 UPN 是格式username@domai。 例如，適用於 active directory contoso.com 使用者 John 可能 UPN john@contoso.com。 使用者的 UPN 根據 RFC 822。 雖然 UPN 」 和 「 電子郵件共用相同的格式，請為使用者的 UPN 值可能會或可能不等於使用者的電子郵件地址。

### <a name="user-principal-name-in-azure-ad"></a>Azure AD 中的使用者主要名稱

Azure AD Connect 精靈會使用 userPrincipalName 屬性，或可讓您從內部部署使用者主要名稱為使用中 Azure AD 指定的屬性 （在自訂安裝）。 這是用於登入 Azure AD 值。 如果使用者主要名稱屬性的值不會在 Azure AD 對應到驗證的網域，然後 Azure AD 會取代預設。 onmicrosoft.com 的值。

Azure Active Directory 中的每個目錄隨附的內建的網域名稱，可讓您表單 contoso.onmicrosoft.com 中開始使用 Azure 或其他 Microsoft 服務。 您可以改善並簡化登入體驗使用自訂網域。 自訂網域上的資訊中 Azure AD 的名稱，以及如何驗證的網域，請閱讀[將 Azure Active Directory 您自訂網域名稱](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Azure AD 登入設定

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD 登入設定與 Azure AD Connect
Azure AD 登入體驗是取決於 Azure AD 是否能夠符合使用者正在同步處理到其中一個自訂網域驗證 Azure AD 目錄中的使用者主要名稱尾碼。 Azure AD Connect 提供時設定登入設定 Azure AD 的說明，讓使用者登入體驗雲端是類似內部部署。 Azure AD Connect 清單的網域為定義的 UPN 尾碼嘗試將它們 Azure AD 中使用自訂網域，並可協助您使用適當需要採取的動作。
Azure AD 登入頁面會列出適用於內部部署的 Active Directory 中定義的 UPN 尾碼，並顯示針對每個後置字元相對應的狀態。 狀態值可以是下列其中一項如下︰

| 狀態 | 描述 | 所需的動作 |
|:----|:----|:----|
|驗證| Azure AD Connect 找到相符 Azure AD 中驗證網域。 此網域的所有使用者可以登入使用他們的內部部署認證| 不需任何動作 |
|尚未驗證| Azure AD Connect 可能 Azure AD 尋找相符的自訂網域，但是無法完成驗證了。 此網域的使用者的 UPN 尾碼將預設。 如果無法驗證網域的同步處理後的 onmicrosoft.com 尾碼。 | Azure AD 中驗證自訂網域。 [深入瞭解](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|不加入| Azure AD Connect 找不到的 UPN 尾碼至對應的自訂網域。 從這個網域的使用者的 UPN 尾碼將預設。 如果不會新增網域的 onmicrosoft.com 和 verifeid Azure 中的。| 新增及驗證[瞭解更多](./active-directory-add-domain.md)的 UPN 尾碼至對應的自訂網域|

Azure AD 登入頁面會列出定義為內部部署的 Active Directory 和對應的自訂網域中使用目前的驗證狀態 Azure AD UPN suffix(s)。 在自訂安裝，您現在可以選取 [ **Azure AD 登入**] 頁面上的使用者主要名稱的屬性。

![Azure AD 登入頁面](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

您可以按一下 [重新整理] 按鈕，重新取得最新的自訂網域的狀態，從 Azure AD。

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>選取使用者主要名稱中 Azure AD 屬性

UserPrincipalName-屬性 userPrincipalName 是使用者將使用時，登入 Azure AD 屬性和 Office 365。 使用網域也稱為 UPN 尾碼，應該 Azure AD 前使用者會同步處理中完成驗證了。 若要保留預設屬性 userPrincipalName 強烈建議。 如果這個屬性是非舉例來說，然後選取另一個屬性，例如以電子郵件，按住 [登入 ID 的屬性可能無法驗證 這就是 「 替代識別碼]。 替代識別碼屬性值必須遵循 RFC822 標準。 同時使用單一登入 (SSO) 的密碼與登入解決方案的同盟 SSO 可替代的識別碼。

>[AZURE.NOTE] 使用替代的識別碼不相容的所有 Office 365 工作負載均。 如需詳細資訊，請參閱[設定替代的登入 ID](https://technet.microsoft.com/library/dn659436.aspx)。

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>不同的自訂網域狀態和 Azure 登入體驗的效果
若要瞭解 Azure AD 目錄中的自訂網域狀態之間的關係非常重要並 UPN 尾碼定義內部部署。 當您設定 sycnhronization 使用 Azure AD Instance，讓我們幫助不同可能 Azure 登入體驗。

以下資訊，讓我們假設我們也注意 UPN 尾碼 contoso.com 時所使用的內部部署目錄屬於 UPN，例如user@contoso.com。

###### <a name="express-settings--password-synchronization"></a>快速設定 / 密碼同步處理
| 狀態         | Azure 的登入的使用者體驗的效果 |
|:-------------:|:----------------------------------------|
| 不加入 | 在此情況下 contoso.com 沒有自訂網域已新增 Azure AD 目錄中。 UPN 內部部署與後置字元的使用者@contoso.com,不能使用他們的內部部署 UPN 登入 Azure。 他們必須改為使用新的 UPN 所提供給這些 Azure AD 藉由新增預設的 Azure AD 目錄的尾碼。 例如，如果您同步處理使用者 Azure AD 目錄 azurecontoso.onmicrosoft.com 然後內部部署使用者user@contoso.com會提供的 UPNuser@azurecontoso.onmicrosoft.com|
| 尚未驗證 | 在此情況下我們具備 Azure AD 目錄中新增自訂網域 contoso.com，但尚未驗證。 如果您繼續使用同步處理的使用者，而不驗證網域，然後使用者將會指定新的 UPN Azure AD 就像 」 不會加入 「 的情境中操作一樣。|
| 驗證 | 在此情況下，我們具備已新增及驗證的 UPN 尾碼 Azure AD 中的自訂網域 contoso.com。 使用者將能夠使用內部部署使用者主體名稱，例如user@contoso.com,登入 Azure 他們已同步處理至 Azure AD 之後|

###### <a name="ad-fs-federation"></a>AD FS 同盟
您無法使用預設值建立同盟。 Azure AD 中的 onmicrosoft.com 網域或 Azure AD 中的驗證自訂網域。 當您執行的 [Azure AD Connect 精靈] 中，如果您選取的未經驗證的網域建立同盟然後 Azure AD Connect 會提示您具有必要的記錄，以建立您的 DNS 裝載於何處的網域。 如需詳細資訊，請參閱[以下](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

如果您選取 [使用者登入] 選項為 「 AD FS 同盟 」，您就必須繼續進行中 Azure AD 建立同盟的自訂網域。 我們討論，這表示我們應該新增 Azure AD 目錄中的自訂網域 contoso.com。

| 狀態         | Azure 登入的使用者體驗的效果 |
|:-------------:|:----------------------------------------|
| 不加入 | 在此情況下 Azure AD Connect 找不到相符的自訂網域 Azure AD 目錄中的 UPN 尾碼 contoso.com。 您需要新增自訂網域 contoso.com，如果您需要登入他們的內部部署 UPN 等搭配使用 AD FS 使用者user@contoso.com。|
| 尚未驗證 | 在此情況下 Azure AD Connect 會提示您輸入適當詳細說明如何在稍後的階段請確認您的網域|
| 驗證 | 在此情況下您可以繼續使用不含任何進一步的動作設定|  

## <a name="changing-user-sign-in-method"></a>變更使用者登入方法

您可以變更從同盟的使用者登入方法若要使用的 Azure AD Connect 使用精靈初始設定後工作 avaialble Azure AD Connect 密碼同步處理。 再次執行 Azure AD Connect 精靈，您就會顯示您可以執行的工作清單。 從工作清單中選取**變更使用者登入**。 

![變更使用者登入 」](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

在下一個頁面上，系統會要求提供 Azure AD 的認證。

![Azure ad 連線](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

在 [**使用者登入**] 頁面中，選取 [**密碼同步處理**]。 這會變更的目錄同盟到受管理的訂閱。

![Azure ad 連線](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] 如果您要製作暫時切換參數密碼同步處理，請檢查**不會轉換使用者帳戶**。 不檢查] 選項會導致轉換的每位使用者為同盟，可能需要幾小時的時間。
  
## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。

進一步了解[Azure AD Connect︰ 設計概念](active-directory-aadconnect-design-concepts.md)


