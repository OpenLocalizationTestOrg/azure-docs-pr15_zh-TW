<properties
pageTitle="新增特定語言的公司商標登入頁面中的 Azure Active Directory 預覽 |Microsoft Azure"
description="瞭解如何新增語言特定的公司商標圖片和 Azure 登入頁面的文字"
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
ms.date="09/12/2016"
ms.author="curtand"/>

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>新增特定語言的公司商標 Azure Active Directory 預覽中登入頁面

若要避免混淆，許多公司想要套用一致的外觀與風格所有網站以及所管理的服務。 Azure Active Directory 預覽會提供這項功能可以讓您自訂的 [登入] 頁面，使用您的公司標誌和自訂色彩配置外觀。 [什麼是在預覽中？](active-directory-preview-explainer.md) 登入頁面是當您登入 Office 365 或其他 web 應用程式為您的身分識別提供者使用 Azure AD 時出現的頁面。 您輸入認證此頁面與互動。

## <a name="customizing-the-sign-in-page-for-another-language"></a>自訂另一種語言的登入頁面

如果您已經建立自訂登入頁面以[新增公司商標至您的登入頁面](active-directory-branding-custom-signon-azure-portal.md)所述，您可以特定語言的項目新增至您的自訂登入頁面。 您可以設定每個目錄的單一登入頁面，以預設的可自訂的項目。 您已設定頁面元素的預設集之後，您可以設定不同的地區設定的其他版本。 您也可以混合，並使其符合 [各種元素。 例如，您可以︰

- 建立預設的**登入頁面圖像**適用於所有文化特性，然後英文和法文建立特定的版本。 當您其中一個這兩種語言設定您的瀏覽器時，特定語言的圖像會出現，而預設下圖顯示所有其他語言。

- 設定不同的標誌，為您的組織 （例如，日文或希伯來文版本）。

我們建議您保留幾種語言變化低，進行的維修作業和效能因素。

**若要新增公司商標至您的目錄︰**

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取**更多服務**中 [文字] 方塊中，輸入**使用者和群組]** ，然後按下**enter 鍵**。

    ![開啟管理使用者](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. 在 [**使用者和群組**刀中，選取 [**公司商標**]。

4. 在**使用者和群組的公司商標**刀，選取 [**新增語言**] 命令。

    ![新增特定語言的品牌元素](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. 修改您想要自訂的項目。 所有項目是選擇性的。

6. 按一下 [**儲存**]。

就可以在一個小時商標出現登入頁面所做的任何變更。

## <a name="next-steps"></a>後續步驟

[新增公司商標至您的登入頁面](active-directory-branding-custom-signon-azure-portal.md)
