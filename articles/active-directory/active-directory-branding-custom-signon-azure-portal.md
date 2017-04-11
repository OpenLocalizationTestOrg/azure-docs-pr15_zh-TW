<properties
pageTitle="自訂您登入頁面的 Azure Active Directory 預覽 |Microsoft Azure"
description="瞭解如何新增公司商標至 Azure 登入頁面"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>新增公司商標 Azure Active Directory 預覽中登入頁面

若要避免混淆，許多公司想要套用一致的外觀與風格所有網站以及所管理的服務。 Azure Active Directory 預覽會提供這項功能可以讓您自訂的 [登入] 頁面，使用您的公司標誌和自訂色彩配置外觀。 [什麼是在預覽中？](active-directory-preview-explainer.md) 登入頁面是當您登入 Office 365 或其他 web 應用程式為您的身分識別提供者使用 Azure AD 時出現的頁面。 您輸入認證此頁面與互動。

如果您想要顯示 [在此頁面上的 [您的公司品牌、 色彩和其他可自訂的項目，請參閱下列圖像以瞭解兩個體驗之間的差異。

下列的螢幕擷取畫面顯示和 Office 365 登入頁面上桌上型電腦**之前**自訂的範例︰

![Office 365 登入之前自訂頁面](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

下列的螢幕擷取畫面顯示和 Office 365 登入頁面上桌上型電腦**後**自訂的範例︰

![Office 365 登入後自訂頁面](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>自訂登入頁面

一般而言，如果您需要瀏覽器存取雲端應用程式及服務的組織而定，您可以使用 [登入] 頁面。

如果您已套用變更到您的登入頁面，就可以在一小時，變更才會出現。

當您造訪具有租用戶專屬的 URL，例如 https://outlook.com/**contoso**.com、 或 https://mail 服務時，才會出現品牌的登入頁面。**contoso**。 com。

當您造訪非租用戶特定 Url 的服務 (例如︰ https://mail.office365.com)，會出現在非品牌登入頁面。 在此情況下，您的品牌會顯示當您輸入您的使用者識別碼，或在您選取 [使用者] 磚。

> [AZURE.NOTE]
>
- 您的網域名稱必須會顯示為 「 使用中 」**網域**部分您已設定 Azure 入口網站的品牌。 如需詳細資訊，請參閱[新增自訂網域名稱](active-directory-domains-add-azure-portal.md)。
- 登入頁面商標無法予以至消費者登入 Microsoft 的頁面。 如果您使用 Microsoft 帳戶登入，您可能會看到建立品牌 Azure AD，所呈現的使用者磚清單，但您組織的品牌不會套用至 Microsoft 帳戶登入頁面。

在 [登入頁面，**讓我保持登**] 核取方塊可讓使用者以他們關閉並重新開啟瀏覽器時，保持登入。 

   ![保留我登入](./media/active-directory-branding-custom-signon-azure-portal/01.png)

它不會影響工作階段存留時間。 您可以隱藏 Azure Active Directory 登入頁面上的核取方塊。
是否要顯示] 核取方塊，則**讓我保持登入停用**的設定而定。

   ![保留我登入](./media/active-directory-branding-custom-signon-azure-portal/02.png)


若要隱藏核取方塊，設定此設定為 [**是]**。 

> [AZURE.NOTE] SharePoint Online 和 Office 2010 中的某些功能取決於使用者也可以核取此方塊。 如果您設定此設定為隱藏，您的使用者可能會看到其他和非預期的提示，登入。




**若要新增公司商標至您的目錄︰**

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取**更多服務**中 [文字] 方塊中，輸入**使用者和群組]** ，然後按下**enter 鍵**。

    ![開啟管理使用者](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. 在 [**使用者和群組**刀中，選取 [**公司商標**]。

4. 在**使用者和群組的公司商標**刀，選取 [**編輯**] 命令。

    ![編輯自訂商標](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. 修改您想要自訂的項目。 所有項目是選擇性的。

6. 按一下 [**儲存**]。

就可以在一個小時商標出現登入頁面所做的任何變更。

## <a name="next-steps"></a>後續步驟

[新增特定語言的公司商標](active-directory-branding-localize-azure-portal.md)
