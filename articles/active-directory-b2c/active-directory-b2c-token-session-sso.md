<properties
    pageTitle="Azure Active Directory B2C︰ 權杖、 工作階段和單一登入設定 |Microsoft Azure"
    description="權杖、 工作階段和 Azure Active Directory B2C 中的單一登入設定"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C︰ 權杖、 工作階段和單一登入設定

此功能可讓您更細緻的控制項，[每個原則基礎](active-directory-b2c-reference-policies.md)的︰
 
1. 安全性權杖 Azure Active Directory (Azure AD) B2C 所發出的存留時間。
2. Azure AD B2C 由管理 web 應用程式工作階段的存留時間。
3. 跨多個應用程式與原則 B2C 租用戶的單一登入 (SSO) 行為。

您可以在您 B2C 租用戶中使用此功能，如下所示︰

1. 請遵循下列步驟，[瀏覽至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)Azure 入口網站上。
2. 按一下 [**登入原則**]。 *附註︰ 您可以使用上任何原則類型，此功能不只是在**登入原則** *。
3. 按一下以開啟原則。 例如，也可以按一下 [ **B2C_1_SiIn**。
4. 按一下 [刀頂端的 [**編輯**]。
5. 按一下 [**權杖、 工作階段和單一登入設定**。
6. 進行您想要的變更。 深入了解後續各節中可用的屬性。
7. 按一下**[確定]**。
8. 刀上方，按一下 [**儲存**]。

![權杖、 工作階段和單一登入設定的螢幕擷取畫面](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>權杖存留時間設定

Azure AD B2C 支援[OAuth 2.0 授權通訊協定](active-directory-b2c-reference-protocols.md)，以啟用 [受保護的資源的安全存取。 若要執行這項支援，Azure AD B2C 會發出各種[安全性權杖](active-directory-b2c-reference-tokens.md)。 以下是您可以使用來管理所 Azure AD B2C 發出安全性權杖的存留時間的屬性︰

- **存取與識別碼 token 存留時間 （分鐘）**︰ OAuth 2.0 承載者權杖的存留時間用來存取受保護的資源。 Azure AD B2C 問題只 ID 權杖這一次。 此值會時套用至存取權杖，我們新增的支援。
   - 預設 = 60 分鐘的時間。
   - 最小值 （含） = 5 分鐘。
   - 最大值 （含） = 1440 分鐘。
- **重新整理 token 存留時間 （天）**︰ 前面的重新整理權杖可用來取得新的 access 或識別碼權杖的最大時間週期 (，您也可以新增重新整理權杖，如果您的應用程式已獲授予他人`offline_access`範圍)。
   - 預設 = 14 天。
   - 最小值 （含） = 1 天。
   - 最大值 （含） = 90 天。
- **重新整理權杖滑動視窗存留時間 （天）**︰ 這段時間內經過使用者之後會強制重新驗證，不管最新的有效期間資料重新整理權杖取得應用程式。 此外，它只可以提供如果切換設定為 [ **Bounded**。 它必須是大於或等於**重新整理 token 存留時間 （天）**值。 如果切換設定為**Unbounded**時，無法提供特定的值。
   - 預設 = 90 天。
   - 最小值 （含） = 1 天。
   - 最大值 （含） = 365 天。

以下是一些您可以使用這些內容的使用案例︰

- 他或她為應用程式持續作用中時，請允許保持登入行動應用程式，使用者。 您可以藉由設定**重新整理 token 滑動視窗存留時間 （天）**您登入的原則中切換到**Unbounded** 。
- 藉由設定適當的存取權杖存留時間符合您的產業安全性與規範要求。

## <a name="session-configuration"></a>工作階段設定

Azure AD B2C 支援[OpenID 連線驗證通訊協定](active-directory-b2c-reference-oidc.md)啟用安全登入 web 應用程式。 以下是您可以使用來管理 web 應用程式工作階段的屬性︰

- **Web 應用程式工作階段存留時間 （分鐘）**︰ Azure AD B2C 的工作階段 cookie 儲存在使用者的瀏覽器，在驗證成功的存留時間。
   - 預設 = 1440 分鐘。
   - 最小值 （含） = 15 分鐘。
   - 最大值 （含） = 1440 分鐘。
- **Web 應用程式工作階段逾時**︰ 如果此參數設為**絕對**，強制使用者指定的**Web 應用程式工作階段存留時間 （分鐘）**經過的時間週期之後重新驗證。 如果此參數設定為 [**循環**（預設設定），使用者會保持登入，只要使用者是在 web 應用程式持續作用中。

以下是一些您可以使用這些內容的使用案例︰

- 藉由設定適當的 web 應用程式工作階段符合您的產業安全性和法規遵循的需求存留時間。
- 強制重新驗證後高安全性的部分 web 應用程式的使用者的互動期間設定的時間範圍。 

## <a name="single-sign-on-sso-configuration"></a>單一登入 (SSO) 設定

如果您有多個應用程式與原則 B2C 租用戶時，您可以透過使用**單一登入設定**屬性來管理使用者的互動。 您可以設定屬性其中一個下列設定︰

- **租用戶**︰ 這是預設的設定。 使用這項設定，可讓多個應用程式與原則 B2C 租用戶共用相同的使用者工作階段。 例如，當使用者登入應用程式，Contoso 採購、 他或她可以也流暢地登入另一個，Contoso 配藥，時存取。
- **應用程式**︰ 這個選項可讓您維護專用的應用程式，與無關的其他應用程式的使用者工作階段。 例如，如果您想要使用者登入 Contoso 配藥 （使用相同的認證），即使他或她已經登入 Contoso 購物，在相同的另一個應用程式 B2C 租用戶。 
- **原則**︰ 這個選項可讓您維護專用的原則，獨立於使用的應用程式的使用者工作階段。 例如，如果使用者已登入並完成多重因素驗證 (MFA) 步驟，他或她可以提供存取至較高安全性組件的多個應用程式，只要不到期原則會受限於工作階段。
- **停用**︰ 這會強制執行整個使用者路透過在每次執行原則的使用者。 例如，這可讓多位使用者註冊您的應用程式 （中共用桌面的情況），偶數時一位使用者會保持登入的整個期間。
