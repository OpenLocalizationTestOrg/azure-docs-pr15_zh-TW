<properties
    pageTitle="新增您的自訂網域名稱並設定 [同盟登入到 Azure Active Directory |Microsoft Azure"
    description="如何將您公司的網域名稱新增至 Azure Active Directory，以及如何設定同盟登入 Azure Active Directory 與內部部署同盟方案之間。"
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
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>將您的自訂網域名稱新增至 Azure Active Directory

您可以設定的自訂網域名稱，例如 「 contoso.com 」，以便為 contoso.com 的使用者可以有同盟單一登入體驗︰ 從您的公司網路。 如果您已經有 Active Directory Federation Services (AD FS) 或公司網路上執行的不同同盟伺服器，您可以設定為使用您的自訂網域名稱，使用 Azure AD Connect 工具 Azure AD。 您也可以使用 Azure AD Connect 部署新的 AD FS 環境中，並設定的同盟單一登入的 Azure ad。

如果您沒有，而且不打算部署 AD FS 或另一個同盟伺服器，請遵循以下指示︰[新增 Azure Active Directory 的自訂網域名稱](active-directory-add-domain.md)。

## <a name="add-a-custom-domain-name-to-your-directory"></a>將自訂網域名稱新增至您的目錄

1. 登入[Azure 傳統入口網站](https://manage.windowsazure.com/)與 Azure AD 目錄的全域管理員的使用者帳戶。

2. 在**Active Directory**中，開啟您的目錄，然後選取 [**網域**] 索引標籤。

3. 在 [命令] 列中，選取 [**新增**]，然後輸入您的自訂網域，例如 「 contoso.com 」 的名稱。 請務必加上.com、.net 或其他最上層的副檔名。

4. 選取**我計劃要設定單一登入與我的本機 Active Directory 此網域**的核取方塊。

5. 選取 [**新增**]。

執行 Azure AD Connect 工具，以取得 Azure AD 會使用 [驗證網域的 DNS 項目。 您會看到 DNS 中的項目**Azure AD 網域**精靈中的步驟。 您能看到哪些內容在精靈的步驟如下所示[的這些指示](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。 如果您沒有 Azure AD Connect 工具，您可以[在此下載](http://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>在該網域的網域名稱註冊機構新增 DNS 項目

Azure AD 搭配使用您的自訂網域名稱的下一個步驟是，更新之網域的 DNS 區域檔案。 這可讓 Azure AD 以驗證您的組織擁有自訂的網域名稱。

1. 登入您的網域名稱的網域名稱註冊機構的網站。 如果您沒有安裝 access 執行此動作，請要求的人員或組織中的人員來完成步驟 2，讓您知道完成時，此存取小組。

2. 藉由新增 Azure AD 所提供給您的 DNS 項目，請更新之網域的 DNS 區域檔案。 這個 DNS 項目可讓 Azure AD 以驗證您擁有該網域。 DNS 項目不會變更任何行為，例如郵件路由或虛擬主機。

此步驟的說明，請參閱[新增 DNS 項目在常用 DNS 註冊機構上的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>驗證與 Azure AD 的網域名稱

一旦您已新增 DNS 項目，您準備好驗證與 Azure AD 的網域名稱。

若要驗證的網域，選取 [ **Azure AD 網域**精靈的步驟 Azure AD Connect 上的 [**下一步**]。 Azure AD 會尋找 DNS 中的項目中該網域的 DNS 區域檔案。 Azure AD 只驗證的網域名稱之後已傳播的 DNS 記錄。 傳播通常只的秒數，但有時候可能需要一小時或更多。 如果驗證無法使用第一次，稍後再試。

Azure AD Connect 精靈中其餘的步驟，然後再繼續。 這會同步處理您的 Windows Server AD Azure AD 的使用者。 您設定的同盟網域中的同步處理的使用者無法存取 Azure AD 公司網路中同盟單一登入體驗。

## <a name="troubleshooting"></a>疑難排解

如果您無法驗證自訂網域名稱，請嘗試下列動作。 我們將會開始的最常見下最常見, 工作。

1.  **請等候一小時**。 需要傳播之前 Azure AD 可以驗證網域的 DNS 記錄。 這可能需要一小時以上。

2.  **輸入的 DNS 記錄，確保和正確**。 完成這個步驟，在網站中該網域的網域名稱註冊機構。 Azure AD 無法驗證的網域名稱，如果 DNS 項目沒有出現在 [DNS 區域檔案，或如果不是完全符合 DNS 項目該 Azure AD 提供給您。 如果您沒有更新的網域名稱註冊機構中該網域的 DNS 記錄的存取權，與人員或在具有此存取，您組織的小組共用 DNS 項目，並要求對方新增 DNS 項目。

3.  **刪除從 Azure AD 中的另一個目錄的網域名稱**。 只有一個目錄中，可以驗證的網域名稱。 如果先前在另一個目錄中驗證網域名稱，其之前，必須刪除那里確認新目錄中。 若要深入瞭解刪除網域名稱，請閱讀[管理自訂網域名稱](active-directory-add-manage-domain-names.md)。

## <a name="add-more-custom-domain-names"></a>新增更多的自訂網域名稱

如果您的組織使用多個的自訂網域名稱，例如 「 contoso.com 」 和 「 contosobank.com 」，您可以將其新增上限 900 的網域名稱。 使用本文中的相同的步驟，新增每個您的網域名稱。

## <a name="next-steps"></a>後續步驟

-   [管理自訂網域名稱](active-directory-add-manage-domain-names.md)
-   [瞭解網域管理概念 Azure AD 中](active-directory-add-domain-concepts.md)
-   [顯示當使用者登入，您公司的商標](active-directory-add-company-branding.md)
-   [使用 PowerShell 來管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
