<properties
   pageTitle="商標指導方針的應用程式 |Microsoft Azure"
   description="開發人員導向的 Azure Active Directory 的資源指南全面涵蓋所有內容"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>應用程式的商標指導方針


本主題探討開發應用程式與 Azure Active Directory (Azure AD) 時，您應該使用的商標指導方針。 這些指導方針可協助他們想要使用其公司或學校帳戶，在 Azure AD，管理或個人帳戶的註冊和登入您的應用程式時，將您的客戶。

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>與公司的個人帳戶或學校帳戶，microsoft

Microsoft 管理使用者帳戶的兩種︰

- **個人帳戶**（先前稱為 Windows Live ID）。 這些帳戶代表*個別*使用者和 Microsoft 之間的關係，並會用來存取 microsoft 的消費者裝置和服務。 這些帳戶供個人使用。

- **公司或學校帳戶。** 這些帳戶是由 Microsoft 管理，代表使用 Azure Active Directory 的組織。 這些帳戶用來登入 Office 365 及其他商務服務 microsoft。

Microsoft 公司或學校帳戶通常是由組織 （公司、 學校、 政府機關） 指派給員工、 學生 （美國聯邦的員工） 的使用者。 這些帳戶是掌握直接在雲端，Azure AD 平台，或是從內部部署目錄，例如 Windows Server Active Directory 同步處理至 Azure AD。 Microsoft *okay*的公司或學校帳戶，但帳戶會擁有及控制貴組織。

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>在應用程式中的 Azure AD 帳戶參照

Microsoft 不會揭露使用者 Azure Active Directory 品牌名稱，或兩者皆不應該您。

- 使用者的登入之後，您應該使用組織的名稱和最大的標誌。 這是更多使用一般的字詞，例如 「 您的組織]。

- 當使用者沒有登入時，請參考自己的帳戶為 「 工作或學校帳戶 」 來傳達這些帳戶由 Microsoft 使用 Microsoft 標誌。 不要使用 「 企業帳戶 」、 「 企業版帳戶 」 或 「 公司帳戶 」 等文字建立使用者混淆。

## <a name="user-account-pictogram"></a>使用者帳戶 pictogram
在舊版的這些指導方針，我們建議您使用 「 藍色徽章 」 pictogram。 根據使用者和開發人員的意見反應，現在建議使用 Microsoft 標誌改為。 這將可協助使用者瞭解他們可重複使用他們使用或其他 Microsoft Office 365 企業版服務登入您的應用程式的帳戶。

## <a name="signing-up-and-signing-in-with-azure-ad"></a>註冊和 Azure AD 登入

您的應用程式可能會呈現不同的路徑，註冊和登入，下列各節提供視覺化的指引，這兩種方法。

**如果您的應用程式支援的使用者登向上 （例如免費試用版或 freemium 模型）**︰ 您可以顯示**登入**] 按鈕，可讓使用者存取您的應用程式，使用其公司帳戶或其個人帳戶。 Azure AD 會顯示同意提示他們存取您的應用程式的第一次。

**如果您的應用程式需要只有管理員可以同意的權限，或如果您的應用程式需要組織授權**︰ 您應該將管理員擷取從使用者的登入。 **「 取得此應用程式 」] 按鈕**會重新導向至登入，然後請要求他們授與同意代表組織使用者的管理員。 這會有隱藏使用者同意依提示進行，您的應用程式新增的優點。

## <a name="visual-guidance-for-app-acquisition"></a>取得應用程式的視覺指導方針

您的 「 取得應用程式 」] 連結必須將使用者導向至 Azure AD 授與存取權 （授權） 頁面上，若要讓組織的系統管理員授權存取組織的資料是由 Microsoft 裝載您的應用程式。 [整合應用程式，與 Azure Active Directory](active-directory-integrating-applications.md)文章討論如何要求存取權的詳細資訊。

管理員同意至您的應用程式之後，他們可以選擇將其新增至其使用者的 Office 365 應用程式啟動器體驗 （從方格圖示和[https://portal.office.com/myapps](https://portal.office.com/myapps)存取）。 如果您想要通知這項功能，您可以使用字詞等 [新增至您的組織的此應用程式] 及 [顯示] 按鈕，就像這樣︰

![應用程式類型和案例](./media/active-directory-branding-guidelines/add-to-my-org.png)

不過，我們建議您撰寫說明性文字，而不要依賴按鈕。 例如︰
> *如果您已經使用 Office 365 或 Microsoft 的其他商務服務，您可以只授與 < your_app_name > 存取組織的資料。這可讓您的使用者存取 < your_app_name > 其現有的公司帳戶。*


## <a name="visual-guidance-for-sign-in"></a>視覺指導登入
您的應用程式應該將使用者重新導向至登入端點對應到您使用與 Azure AD 整合通訊協定的按鈕顯示號。 下一節提供什麼該按鈕看起來應該像詳細資料。

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictogram，「 登入 Microsoft 」
Microsoft 標誌的關聯並唯一代表 Azure AD 內的其他身分識別提供者之間的 「 登在與 Microsoft 「 條款可能支援您的應用程式。 如果您沒有足夠的空間，「 登在與 microsoft 」，則確定縮短，「 登入]。

![應用程式類型和案例](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![應用程式類型和案例](./media/active-directory-branding-guidelines/sign-in-light.png)

您也可以使用深的色彩配置的按鈕。

![應用程式類型和案例](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![應用程式類型和案例](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>商標該做與不該做規則

**** 「 公司或學校帳戶 」 一起使用 「 登中有 「 按鈕提供協助辨識的內容是否他們可以使用該使用者的其他說明。 **不**使用其他的字詞，例如 「 企業帳戶 」、 「 企業版帳戶 」 或 「 公司帳戶 」。

**不要**使用 「 Office 365 識別碼 」 或 「 Azure 識別碼 」。 Office 365 也是 microsoft 不會用於驗證的 Azure AD 提供取用者的名稱。

**不**會改變 Microsoft 標誌。

**不**會揭露 Azure 或 Active Directory 品牌使用者。 然而是 [確定] 以使用這些字詞的開發人員、 IT 專業人員和管理員。

## <a name="navigation-dos-and-donts"></a>瀏覽該做與不該做規則

**務必讓使用者在登出並切換到另一個使用者帳戶。** 雖然多數人都有 Microsoft/Facebook/Google/Twitter 從單一個人帳戶，人員通常是一個以上的組織與相關聯。 多個登入使用者的支援即將推出。
