<properties
    pageTitle="Azure AD 應用程式 Proxy 與發佈的應用程式的條件的存取權"
    description="說明如何設定條件的 access 應用程式發佈到遠端使用 Azure AD 應用程式 Proxy 存取。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-conditional-access"></a>使用條件的存取權

您可以設定將應用程式使用應用程式 Proxy 發行條件的存取權授予存取規則。 這可讓您︰

- 需要每個應用程式的多重因素驗證
- 使用者不是作用中時，才需要多重因素驗證
- 封鎖使用者，使他們不是作用中時，存取應用程式

所有使用者及群組，或只特定的使用者和群組時，會套用這些規則。 依預設規則會套用至應用程式存取的所有使用者。 不過規則也可以限制使用者所指定的安全性群組的成員。  

使用者存取同盟的應用程式使用 OAuth 2.0、 OpenID 連線、 SAML 或 WS 同盟時，會評估存取規則。 此外，存取規則時，會評估與 OAuth 2.0 OpenID 連線的重新整理權杖可用於取得存取權杖。

## <a name="conditional-access-prerequisites"></a>條件的存取權的先決條件

- Azure Active Directory 進階版訂閱
- 同盟或受管理的 Azure Active Directory 租用戶
- 同盟租用戶需要啟用該多重因素驗證 (MFA)  
    ![設定存取規則-需要多重因素驗證](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>設定每個應用程式多重因素驗證
1. Azure 傳統入口網站中以系統管理員身分登入。
2. 移至 [Active Directory，然後選取您要啟用應用程式 Proxy 的目錄。
3. 按一下 [**應用程式**]，然後捲動至 [**存取規則**] 區段。 Access 的 [規則] 區段只會顯示使用的應用程式使用應用程式 Proxy 發行同盟的驗證。
4. 選取 [**啟用存取規則**，**在**啟用規則。
5. 指定使用者和群組對象規則會套用。 若要選取一或多個群組的存取規則會套用到使用 [**新增群組**] 按鈕。 這個對話方塊也可以用於移除選取的群組。  若要套用至群組選取規則時，access 會只會強制執行規則屬於指定的安全性群組的使用者。  

  - 若要明確地從規則中排除安全性群組，**除了**檢查並指定一個或多個群組。 使用者的 Except 清單中群組的成員不需要執行多因素驗證。  

  - 如果使用者已設定為使用個別使用者多重因素驗證功能，此設定會優先於應用程式多重因素驗證規則。 這表示已經被設定為每位使用者多重因素驗證的使用者都必須執行多因素驗證，即使他們有已不會應用程式的多重因素驗證規則。 進一步瞭解[多重因素驗證與每位使用者的設定](../multi-factor-authentication/multi-factor-authentication.md)。

6. 選取您想要設定的存取規則︰
    - **需要多重因素驗證**︰ 存取規則套用對象的使用者都必須完成多重因素驗證，才能存取套用規則的應用程式。
    - **不在工作時需要多重因素驗證**︰ 嘗試從信任的 IP 位址存取應用程式的使用者不需要執行多因素驗證。 多重因素驗證設定] 頁面上，您可以設定受信任的 IP 位址範圍。
    - **封鎖時不在公司的存取權**︰ 嘗試存取應用程式與公司網路以外的使用者將無法存取應用程式。


## <a name="configuring-mfa-for-federation-services"></a>設定 MFA federation services
同盟的租用戶的多重因素驗證 (MFA) 可能會執行 Azure Active Directory 或內部部署 AD FS server。 根據預設，MFA 會裝載的 Azure Active Directory 的任何頁面上。 若要設定 MFA 內部部署，請執行 Windows PowerShell 並使用 – SupportsMFA 屬性來設定 Azure AD 模組。

下列範例會示範如何在 contoso.com 租用戶中使用[設定 MsolDomainFederationSettings 指令程式](https://msdn.microsoft.com/library/azure/dn194088.aspx)來啟用內部部署 MFA:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

除了設定這個旗標，必須執行多因素驗證設定同盟租用戶 AD FS 執行個體。 依照[部署 Microsoft Azure 多重因素驗證內部部署](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)的指示進行。


## <a name="see-also"></a>另請參閱

- [使用 [宣告應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [發佈應用程式的應用程式 Proxy](active-directory-application-proxy-publish.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [發佈應用程式使用您自己的網域名稱](active-directory-application-proxy-custom-domains.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
