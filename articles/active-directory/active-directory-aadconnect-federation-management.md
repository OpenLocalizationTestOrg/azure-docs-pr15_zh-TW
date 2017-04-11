<properties
    pageTitle="Active Directory Federation Services 管理和 Azure AD Connect 與自訂 |Microsoft Azure"
    description="自訂使用者 AD FS 登入體驗，Azure AD Connect 與 PowerShell 的 Azure AD Connect 與 AD FS 管理。"
    keywords="AD FS、 ADFS，AD FS 管理 AAD 連線，連線，登入 AD FS 自訂，修復信任，O365，同盟，信賴"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Active Directory Federation Services 管理與自訂與 Azure AD Connect

此文件詳細資料的工作相關的 Active Directory Federation Services (AD FS) 可執行使用 Microsoft Azure Active Directory 連線，以及可能需要完成 AD FS 伺服器陣列設定的其他一般 AD FS 工作。

| 主題 | 它會說明 |
|:------|:-------------|
|**AD FS 管理**|
|[修復信任](#repairthetrust)| 修復 Office 365 的同盟信任 |
|[新增 AD FS server](#addadfsserver) | 展開額外的 AD FS server 與 AD FS 伺服器陣列|
|[新增 AD FS web 應用程式 proxy 伺服器](#addwapserver) | 展開 AD FS 伺服器陣列與其他 WAP 伺服器|
|[新增同盟的網域](#addfeddomain)| 新增同盟的網域|
| **AD FS 自訂**|
|[新增自訂的公司標誌或圖例](#customlogo)| 自訂 AD FS 登入頁面的公司標誌和圖例 |
|[新增登入描述](#addsignindescription) | 新增登入頁面描述 |
|[修改 AD FS 宣告規則](#modclaims) | 修改 AD FS 宣告各種同盟案例 |

## <a name="ad-fs-management"></a>AD FS 管理

Azure AD Connect 提供相關 AD FS 可以使用最小的使用者互動 Azure AD Connect 精靈] 來執行各種工作。 當您完成安裝 Azure AD Connect 執行精靈之後，您可以執行精靈一次執行其他工作。

### 修復信任<a name=repairthetrust></a>

Azure AD Connect 可以檢查目前的健康情況的 AD FS 和 Azure Active Directory 信任，並採取適當的動作，以修復信任。 請遵循下列步驟來修復您 Azure AD 與 AD FS 信任。

1. 從其他工作清單中選取**修復 AAD 和 ADFS 信任**。
![修復 AAD 及 ADFS 信任](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. 在**連線至 Azure AD**頁面上，提供全域管理員認證 Azure ad，並按一下 [**下一步**。
![Azure ad 連線](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. 在 [**遠端存取的認證**] 頁面上輸入網域系統管理員認證。
![遠端存取的認證](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    按一下 [**下一步**] 之後，Azure AD Connect 會檢查憑證狀況，並顯示任何問題。

    ![憑證的狀態](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    **準備設定**] 頁面會顯示所要執行修復信任的動作清單。

    ![若要設定好](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. 按一下 [修復信任的 [**安裝**]。

>[AZURE.NOTE] Azure AD Connect 可以只修復或處理自我簽署的憑證。 Azure AD Connect 無法修復協力廠商憑證。

### 新增 AD FS server<a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect 需要新增 AD FS server PFX 憑證檔案。 因此，您可以執行這項作業，只有當您使用 Azure AD Connect 設定 AD FS 伺服器陣列。

1. 選取 [**部署額外的同盟伺服器**]，然後按一下 [**下一步**]。
![其他同盟伺服器](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. 在**連線至 Azure AD**頁面上，輸入您的全域管理員認證 Azure AD，按一下 [**下一步**。
![Azure ad 連線](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. 提供網域系統管理員認證。
![網域系統管理員認證](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD Connect 會要求您提供時設定新的 AD FS 伺服器陣列與 Azure AD Connect PFX 檔案的密碼。 按一下 [**輸入密碼**以 PFX 檔案提供的密碼。
![憑證密碼](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![指定 SSL 憑證](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. 在**AD FS 伺服器**] 頁面中，輸入伺服器名稱或都會新增至 AD FS 伺服器陣列的 IP 位址。
![AD FS 伺服器](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. 按一下 [**下一步**，然後移到最後的 [**設定**] 頁面。 Azure AD Connect 加 AD FS 伺服器陣列中的伺服器完成後，會提供您確認連線的選項。
![若要設定好](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![安裝完成](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### 新增 AD FS web 應用程式 proxy 伺服器<a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect 需要 PFX 憑證檔案來新增 web 應用程式 proxy 伺服器。 因此，您可以執行這項作業，只有當您使用 Azure AD Connect 設定 AD FS 伺服器陣列。

1. 從可用的工作清單中選取 [**部署 Web 應用程式 Proxy** ]。
![部署 web 應用程式 proxy](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. 提供 Azure 全域管理員認證。
![Azure ad 連線](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. 在 [**指定的 SSL 憑證**] 頁面上提供 PFX 檔案時使用 Azure AD Connect 設定 AD FS 伺服器陣列所提供的密碼。
![憑證密碼](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![指定 SSL 憑證](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. 新增要新增的 web 應用程式 proxy 伺服器。 因為 web 應用程式 proxy 伺服器可能不會加入網域，精靈會要求正在新增的伺服器管理員認證。
![管理伺服器認證](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. 在**Proxy 信任認證**] 頁面上，提供設定 proxy 信任和存取 AD FS 伺服器陣列中的主要伺服器的系統管理認證。
![Proxy 信任認證](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. 在**準備好設定**頁面上，精靈會顯示所要執行的動作清單。
![若要設定好](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. 按一下 [完成設定的 [**安裝**]。 設定完成之後，精靈會提供您要驗證之伺服器的連線選項。 按一下 [**驗證**] 核取 [連線]。
![安裝完成](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### 新增同盟的網域<a name=addfeddomain></a>

輕鬆新增網域，才能使用 Azure AD Connect Azure AD 與同盟。 Azure AD Connect 新增同盟的網域，並修改宣告規則，以正確反映發行者，當您有多個與 Azure AD 同盟的網域。

1. 若要新增同盟的網域，請選取 [工作**新增其他 Azure AD 網域**。
![其他 Azure AD 網域](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. 在精靈的下一個頁面上，提供 Azure AD 全域管理員認證。
![Azure ad 連線](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. 在 [**遠端存取的認證**] 頁面上提供的網域系統管理員認證。
![遠端存取的認證](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. 在下一個頁面上，精靈會提供 Azure AD 網域，您可以使用聯合內部部署目錄的清單。 從清單中選擇網域。
![Azure AD 網域](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    選擇網域之後，精靈會提供您有關進一步，精靈會採取的動作與設定的影響適當的資訊。 在某些情況下，如果您選取的網域，不您尚未驗證中 Azure AD，精靈會提供您的資訊能協助您 [驗證網域。 如需詳細資訊，請參閱[新增 Azure Active Directory 您自訂網域名稱](active-directory-add-domain.md)。

5. 按一下 [**下一步**，並**準備設定**] 頁面會顯示 Azure AD Connect 將執行的動作清單。 按一下 [完成設定的 [**安裝**]。
![若要設定好](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>AD FS 自訂

下列各節提供詳細資訊，您可能必須自訂您 AD FS 登入頁面時執行一般工作的部分。

### 新增自訂的公司標誌或圖例<a name=customlogo></a>

若要變更會顯示在 [**登入**] 頁面上的公司標誌，請使用下列 Windows PowerShell cmdlet 和語法。

> [AZURE.NOTE] 建議的標誌的大小會 260 x 35 @ 96 dpi 與不大於 10 KB 的檔案大小。

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] 需要*TargetName*參數。 預設名稱為預設佈景主題與 AD FS 發行。


### 新增登入描述<a name=addsignindescription></a>

若要新增至**登入頁面**的登入頁面描述，請使用下列 Windows PowerShell cmdlet 和語法。

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### 修改 AD FS 宣告規則<a name=modclaims></a>

AD FS 支援 rtf 宣告語言可供您建立自訂宣告規則。 如需詳細資訊，請參閱[宣告規則語言的角色](https://technet.microsoft.com/library/dd807118.aspx)。

下列各節說明如何撰寫屬於 Azure AD 某些案例的自訂規則與 AD FS 同盟。

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>條件的屬性值不變識別碼

Azure AD Connect 可讓您指定的物件會同步處理至 Azure AD 時，會成為來源錨點的屬性。 如果的自訂屬性中的值不是空的您可能會想要發行不變的識別碼宣告。 例如，您可能會選取**ms-ds-consistencyguid**為來源錨點的屬性，並想要為**ms-ds-consistencyguid**發行**ImmutableID** ，以防屬性有針對其值。 如果不有任何針對屬性的值，會發出**objectguid 資訊**為不變的識別碼。  您可以建立自訂宣告下一節所述的規則集。

**規則 1︰ 查詢屬性**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

此規則，在您正在查詢**ms-ds-consistencyguid**和**objectguid 資訊**Active Directory 的使用者的值。 存放區名稱變更為 AD FS 部署中的適當的儲存區名稱。 也至適當的宣告類型所定義**objectguid 資訊**和**ms-ds-consistencyguid**您同盟變更宣告類型。

此外，透過**新增**及**問題**，您避免加入外寄的實體問題，並可以中間值為使用中的值。 您會發出更新規則中的宣告之後您建立的值作為不變的識別碼。

**規則 2︰ 核取 [ms-ds-consistencyguid 是否存在的使用者**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

此規則可定義稱為**idflag**填入使用者沒有**ms-ds-concistencyguid**是否設定為**useguid**暫時標幟。 此邏輯是 AD FS 不允許空白宣告。 因此當您新增宣告 http://contoso.com/ws/2016/02/identity/claims/objectguid 和 http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid 規則 1，最後會產生**msdsconsistencyguid**宣告只当使用者填入值。 如果不填入時，它必須為空值，並立即其去掉，也會看到 AD FS。 所有物件，如此該宣告一律會有執行規則 1 之後，會都有**objectguid 資訊**。

**規則 3︰ 為不變 ID 議題 ms-ds-consistencyguid 出現**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

這是隱含的**存在**核取。 如果宣告] 的值，然後發出的為不變的識別碼。 上一個範例使用**nameidentifier**宣告。 您必須變更為適當的宣告輸入您的環境中不變 id。

**規則 4︰ 為不變 ID 議題 objectguid 資訊 ms-ds-consistencyGuid 不存在**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

在此規則中，您只要簽暫時旗標**idflag**。 您決定是否要宣告根據其值。

> [AZURE.NOTE] 請務必這些規則的順序。

#### <a name="sso-with-a-subdomain-upn"></a>子網域 UPN 含 SSO

您可以新增多個網域同盟使用 Azure AD Connect，如[新增新的同盟的網域](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain)所述。 您必須修改 UPN 宣告，讓發行者識別碼對應到根網域和不子網域，因為同盟的根網域也涵蓋子。

根據預設，發行者識別碼宣告規則設為︰

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![預設發行者識別碼宣告](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

預設的規則只會 UPN 尾碼，而會使用該參數中發行者識別碼宣告。 例如，John sub.contoso.com 中的使用者，而 Azure AD 與同盟 contoso.com。 John 進入john@sub.contoso.com時 Azure AD，登入的使用者名稱和宣告 AD FS 中的規則，以下列方式處理預設發行者 ID。

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**宣告值︰** http://sub.contoso.com/adfs/services/trust/

若要僅發行者宣告值的根網域，請變更 [宣告規則在符合下列。

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>後續步驟

深入瞭解[使用者登入選項](active-directory-aadconnect-user-signin.md)。
