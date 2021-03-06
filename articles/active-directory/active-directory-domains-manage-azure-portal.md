<properties
    pageTitle="管理您的 Azure Active Directory 預覽中的自訂網域名稱 |Microsoft Azure"
    description="管理概念及管理網域名稱 Azure Active Directory 中的內容"
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
    ms.date="09/12/2016"
    ms.author="curtand;jeffsta"/>

# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>管理您的 Azure Active Directory 預覽中的自訂網域名稱

將網域名稱是很重要的部分許多目錄資源的識別碼︰ 它是使用者，組件] 群組中，地址的使用者名稱或電子郵件地址，並可在應用程式的應用程式識別碼 URI 的組件。 Azure Active Directory (Azure AD) 預覽中的資源可以包含屬於包含資源的目錄已驗證的網域名稱。 [什麼是在預覽中？](active-directory-preview-explainer.md) Azure AD 中，只有全域管理員可以執行網域管理工作。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>設定您的 Azure AD 目錄的主要網域名稱

建立您的目錄時，最初的網域名稱，例如 「 contoso.onmicrosoft.com 」，也是主要網域名稱。 當您建立新使用者時，主要網域是新使用者的預設網域名稱。 這會簡化回傳系統管理員在入口網站中建立新使用者的程序。 若要變更的主要網域名稱︰

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取 [**更多服務**中 [文字] 方塊中，輸入**Azure Active Directory** ，然後按下**enter 鍵**。

    ![開啟管理使用者](./media/active-directory-domains-add-azure-portal/user-management.png)

3. 在***目錄名稱***刀中，選取**網域名稱**。

4. 在***目錄名稱*網域名稱**刀中，選取您想要進行的主要網域名稱的網域名稱。

5.  在***domainname***刀 （也就是刀開啟在標題中有新的網域名稱），選取 [**讓 [主要**] 命令。 確認提示時選擇。

    ![讓主要網域名稱](./media/active-directory-domains-manage-azure-portal/make-primary.png)

您可以變更您的目錄，不同盟任何驗證的自訂網域的主要網域名稱。 變更您的目錄的主要網域，不會變更任何現有使用者的使用者名稱。

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Azure AD 中新增自訂網域名稱

每個 Azure AD 目錄，您可以新增最 900 的自訂網域名稱。 若要[新增額外的自訂網域名稱](active-directory-domains-add-azure-portal.md)的程序是相同的第一個的自訂網域名稱。

## <a name="add-subdomains-of-a-custom-domain"></a>新增自訂網域的子網域

如果您想要將第三層網域名稱，例如 「 europe.contoso.com 」 新增至您的目錄，您應該先新增及驗證的第二層網域，例如 contoso.com。 子網域都會被 Azure AD 自動驗證。 若要查看您剛才新增的子網域已驗證，重新整理頁面，在瀏覽器中，列出的網域。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>如果您的 DNS 註冊機構變更您的自訂網域名稱，該怎麼辦

如果您的 DNS 註冊機構變更您的自訂網域名稱，您可以繼續使用 Azure AD 本身不中斷及其他設定工作的自訂網域名稱。 如果您使用您的自訂網域名稱搭配 Office 365、 Intune 或其他服務所依賴 Azure AD 中的自訂網域名稱時，請參閱的文件，這些服務。

## <a name="delete-a-custom-domain-name"></a>刪除的自訂網域名稱

如果您的組織不會再使用該網域名稱，或如果您要使用另一個 Azure AD 該網域名稱，您可以從 Azure AD 刪除的自訂網域名稱。

若要刪除的自訂網域名稱，您必須先確定目錄中的任何資源依賴的網域名稱。 如果您無法刪除您的目錄的網域名稱︰

-   任何使用者都有使用者名稱、 電子郵件地址或包含的網域名稱的 proxy 位址。

-   任何群組具有電子郵件地址或網域名稱包含 proxy 位址。

-   Azure AD 中的任何應用程式有應用程式識別碼 URI 包含的網域名稱。

您必須變更或刪除任何這類資源 Azure AD 目錄中，您可以刪除的自訂網域名稱之前，先。

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>使用 PowerShell 或 Graph API 來管理網域名稱

使用 Microsoft PowerShell 或使用 （在公用預覽版本） Azure AD Graph API 以程式設計方式，也可以完成大部分的管理工作的 Azure Active Directory 中的網域名稱。

-   [使用 PowerShell 來管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [使用圖形 API Azure AD 中管理網域名稱](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>後續步驟

-   [新增自訂網域名稱](active-directory-domains-add-azure-portal.md)
