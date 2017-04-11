<properties
    pageTitle="將您的自訂網域名稱新增至 Azure Active Directory 預覽 |Microsoft Azure"
    description="如何將公司網域名稱新增至 Azure Active Directory，以及如何驗證的網域名稱。"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>將自訂網域名稱新增至 Azure Active Directory 預覽

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-domains-add-azure-portal.md)
- [Azure 傳統入口網站](active-directory-add-domain.md)

您有一個或多個網域名稱，您的組織使用公司進行商務往來，和您的使用者登入公司網路使用您的公司網域名稱。 使用預覽 Azure Active Directory (Azure AD)，您可以新增您的公司網域名稱以及 Azure ad。 [什麼是在預覽中？](active-directory-preview-explainer.md) 這個選項可讓您指定在目錄中的使用者名稱所熟悉您的使用者，例如‘alice@contoso.com.’程序很簡單︰

1. 將自訂網域名稱新增至您的目錄
2. 在網域名稱註冊機構新增的網域名稱的 DNS 項目
3. Azure AD 中驗證自訂網域名稱

## <a name="how-do-i-add-a-domain-name"></a>我要如何新增的網域名稱？

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取 [**更多服務**中 [文字] 方塊中，輸入**Azure Active Directory** ，然後按下**enter 鍵**。

    ![開啟管理使用者](./media/active-directory-domains-add-azure-portal/user-management.png)

3. 在***目錄名稱***刀中，選取**網域名稱**。

4. 在***目錄名稱*網域名稱**刀中，選取 [**新增**] 命令。

  ![選取 [新增] 命令](./media/active-directory-domains-add-azure-portal/add-command.png)

5. 在**網域名稱**刀中，在方塊中，例如 'contoso.com'，輸入您的自訂網域名稱，然後選取**[新增網域**。 請務必加上.com、.net 或其他最上層的副檔名。

6. 在***網域名稱***刀 （也就是防禦開啟在標題中有新的網域名稱），以取得 DNS 項目資訊 Azure AD 將用來驗證您的組織擁有自訂的網域名稱。

  ![取得 DNS 項目資訊](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

既然您已新增的網域名稱，Azure AD 必須驗證您的組織擁有的網域名稱。 Azure AD 可以執行此驗證之前，您必須在 DNS 區域檔案中的網域名稱來新增 DNS 項目。 在網域名稱的網域名稱註冊機構的網站被執行此工作。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>在該網域的網域名稱註冊機構新增 DNS 項目

Azure AD 搭配使用您的自訂網域名稱的下一個步驟是，更新之網域的 DNS 區域檔案。 這可讓 Azure AD 以驗證您的組織擁有自訂的網域名稱。

1.  登入該網域的網域名稱註冊機構。 如果您沒有安裝更新 DNS 項目的存取權，請要求的人員或小組可完成步驟 2，並讓您知道完成時，此存取。

2.  藉由新增 Azure AD 所提供給您的 DNS 項目，請更新之網域的 DNS 區域檔案。 這個 DNS 項目可讓 Azure AD 以驗證您擁有該網域。 DNS 項目不會變更任何行為，例如郵件路由或虛擬主機。

說明與此新增 DNS 項目，請閱讀 [[新增 DNS 項目在常用 DNS 註冊機構上的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>驗證與 Azure AD 的網域名稱

一旦您已新增 DNS 項目，您準備好驗證與 Azure AD 的網域名稱。

已傳播的 DNS 記錄之後，才可以驗證的網域名稱。 這個傳播通常只的秒數，但有時候可能需要一小時或更多。 如果驗證無法使用第一次，稍後再試。

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取 [**瀏覽**，在 [文字] 方塊中，輸入使用者管理，然後按下**enter 鍵**。

    ![開啟管理使用者](./media/active-directory-domains-add-azure-portal/user-management.png)

3. 在**使用者管理的網域名稱**刀中，選取您要驗證的未經驗證的網域名稱。

4. 在***網域名稱***刀 （也就是刀開啟在標題中有新的網域名稱） 中，選取 [**驗證**完成驗證]。

現在，您可以[指定包含您的自訂網域名稱的使用者名稱](active-directory-users-create-azure-portal.md)。

## <a name="troubleshooting"></a>疑難排解

如果您無法驗證自訂網域名稱，請嘗試下列動作。 我們將會開始的最常見下最常見, 工作。

1.  **請等候一小時**。 需要傳播之前 Azure AD 可以驗證網域的 DNS 記錄。 這可能需要一小時以上。

2.  **輸入的 DNS 記錄，確保和正確**。 完成這個步驟，在網站中該網域的網域名稱註冊機構。 Azure AD 無法驗證的網域名稱，如果 DNS 項目沒有出現在 [DNS 區域檔案，或如果不是完全符合 DNS 項目該 Azure AD 提供給您。 如果您沒有更新的網域名稱註冊機構中該網域的 DNS 記錄的存取權，與人員或在具有此存取，您組織的小組共用 DNS 項目，並要求對方新增 DNS 項目。

3.  **刪除從 Azure AD 中的另一個目錄的網域名稱**。 只有一個目錄中，可以驗證的網域名稱。 如果先前在另一個目錄中驗證網域名稱，其之前，必須刪除那里確認新目錄中。 若要深入瞭解刪除網域名稱，請閱讀[管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)。    

## <a name="add-more-custom-domain-names"></a>新增更多的自訂網域名稱

如果您的組織使用多個的自訂網域名稱，例如 「 contoso.com 」 和 「 contosobank.com 」，您可以將其新增上限 900 的網域名稱。 使用本文中的相同的步驟，新增每個您的網域名稱。

## <a name="next-steps"></a>後續步驟

[管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)
