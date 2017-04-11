<properties
    pageTitle="Azure 條件的存取權 SaaS 應用程式 |Microsoft Azure"
    description="Azure AD 條件存取可讓您設定每個應用程式多重因素驗證存取規則及封鎖的不是在受信任的網路上的使用者存取權的能力。 "
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Azure Active Directory 條件 Access 快速入門

Azure Active Directory 條件存取[SaaS](https://azure.microsoft.com/overview/what-is-saas/)應用程式和 Azure AD 連線應用程式可讓您設定條件存取權的群組、 位置及應用程式區分大小寫。 

根據應用程式敏感度條件的存取，您可以設定多重因素驗證 (MFA) 存取規則，每個應用程式。 每個應用程式的 MFA 能夠封鎖不受信任網路上的使用者的存取權。 您可以套用 MFA 規則會指派到應用程式，或僅適用於使用者指定的安全性群組內的所有使用者。  如果他們從組織的網路中的 IP 位址存取應用程式，可能會排除使用者從 MFA 需求。

這些功能將可供購買 Azure Active Directory 進階版授權的客戶。

## <a name="scenario-prerequisites"></a>案例的先決條件
* Azure Active Directory 進階版的授權

* 同盟或受管理的 Azure Active Directory 租用戶

* 同盟租用戶需要啟用該多重因素驗證。

## <a name="configure-per-application-access-rules"></a>設定每個應用程式存取規則

本節說明如何設定每個應用程式存取規則。

1. Azure 傳統入口網站使用的是 Azure AD 全域管理員帳戶登入。
2. 在左窗格中，選取 [ **Active Directory**]。
3. 在 [目錄] 索引標籤中，選取 [目錄]。
4. 選取 [**應用程式**] 索引標籤。
5. 選取規則將會設定的應用程式。
6. 選取 [**設定**] 索引標籤。
7. Access 的 [規則] 區段下捲動。 選取想要的存取規則。
8. 指定會套用規則的使用者。
9. 選取 [**啟用] 以在**啟用原則。

##<a name="understanding-access-rules"></a>了解存取規則

此區段所提供支援 Azure 條件應用程式存取的存取規則的詳細的的描述。

### <a name="specifying-the-users-the-access-rules-apply-to"></a>指定使用者存取規則套用至

預設的原則會套用至應用程式存取的所有使用者。 不過，您也可以限制原則至使用者指定的安全性群組的成員。 [**新增群組**] 按鈕用來存取規則會套用到 [群組選取項目] 對話方塊中選取一或多個群組。 這個對話方塊也可以用於移除選取的群組。 若要套用至群組選取規則時，access 會只會強制執行規則屬於指定的安全性群組的使用者。

安全性群組可明確排除從原則，請選取 [**除了**] 選項，並指定一個或多個群組。 為其成員的清單中的群組**以外**的使用者不會受到多重因素驗證需求，即使是存取規則套用至群組的成員。
以下所示的存取規則會要求存取應用程式時，使用多重因素驗證的 [管理員] 群組中的所有使用者。

![設定與 MFA 條件存取規則](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>MFA 條件存取規則
如果使用者已設定並使用每個使用者多重因素驗證功能，使用者在此設定會合併多重因素驗證規則的應用程式。 這表示已經被設定為每位使用者多重因素驗證的使用者都必須執行多因素驗證，即使他們有已不會應用程式多重因素驗證規則。 進一步瞭解多重因素驗證與每位使用者的設定。

### <a name="access-rule-options"></a>存取規則選項
支援下列選項︰

* **需要多重因素驗證**︰ 存取規則套用至]，對象的使用者都必須完成的多重因素驗證，才能存取原則套用到的應用程式。

* **需要時不在公司的多因素驗證**︰ 來自受信任的 IP 位址的使用者不需要執行多因素驗證。 多重因素驗證設定] 頁面上，您可以設定受信任的 IP 位址範圍。

* **封鎖時不在公司的存取權**︰ 不來自受信任的 IP 位址的使用者會遭到封鎖。 多重因素驗證設定] 頁面上，您可以設定受信任的 IP 位址範圍。

### <a name="setting-rule-status"></a>設定規則狀態
存取規則狀態可讓開啟或關閉規則。 關閉存取規則時，會不會強制執行多因素驗證需求。

### <a name="access-rule-evaluation"></a>存取規則評估

使用者存取同盟的應用程式使用 OAuth 2.0、 OpenID 連線、 SAML 或 WS 同盟時，會評估存取規則。 此外，OAuth 2.0 及 OpenID 連線使用的重新整理權杖擷取存取權杖時，會評估存取規則。 如果原則評估無法使用的重新整理權杖時，會傳回錯誤**invalid_grant** ，這表示使用者必須重新驗證用戶端。

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>設定同盟服務，提供多重因素驗證

同盟的租用戶，為 MFA 可能會執行 Azure Active Directory 或內部部署 AD FS server。

根據預設，MFA 會裝載的 Azure Active Directory 的頁面。 若要設定 MFA 內部部署， **– SupportsMFA**屬性必須設定為**true** Azure Active Directory 中，使用 Windows PowerShell 的 Azure AD 模組。

下列範例會示範如何在 contoso.com 租用戶中使用[設定 MsolDomainFederationSettings 指令程式](https://msdn.microsoft.com/library/azure/dn194088.aspx)來啟用內部部署 MFA:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

除了設定這個旗標，必須執行多因素驗證設定同盟租用戶 AD FS 執行個體。 依照[部署 Azure 多重因素驗證內部部署](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)的指示進行。

## <a name="related-articles"></a>相關的文章

- [保護存取 Office 365 及其他應用程式連線至 Azure Active Directory](active-directory-conditional-access.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
