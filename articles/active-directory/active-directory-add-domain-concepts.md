<properties
    pageTitle="Azure Active Directory 中的自訂網域名稱的概觀 |Microsoft Azure"
    description="說明包括同盟的單一登入的 Azure Active directory 中使用自訂網域名稱的概念性架構"
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
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Azure Active Directory 中的自訂網域名稱的概觀

將網域名稱是很重要的部分許多目錄資源的識別碼︰ 它是使用者，組件] 群組中，地址的使用者名稱或電子郵件地址的一部分，並且可應用程式識別碼 URI 應用程式的一部分。 Azure Active Directory (Azure AD) 中的資源可以包含屬於包含資源的目錄已驗證的網域名稱。 Azure AD 中，只有全域管理員可以執行網域管理工作。

Azure AD 中的網域名稱是全域唯一的。 以單一 Azure AD 可將網域名稱。 如果在一個 Azure AD 目錄已驗證的網域名稱，然後沒有其他 Azure AD 目錄可以驗證或使用該相同的網域名稱。

## <a name="initial-and-custom-domain-names"></a>初始的自訂網域名稱

Azure AD 中的每個網域名稱為初始網域名稱或將自訂網域名稱。

每個 Azure AD 隨附在表單 contoso.onmicrosoft.com 的初始網域名稱。 此第三層級的網域名稱，在此範例中 「 contoso.onmicrosoft.com，「 已建立目錄建立時，通常是由管理員建立目錄。 無法變更或刪除目錄的初始網域名稱。 最初的網域名稱，而完整的功能，主要被為了驗證自訂網域名稱之前，可做為啟動載入機制。

在大部分的生產環境中，目錄具有至少有一個驗證自訂網域，例如 「 contoso.com 」，並會顯示使用者的自訂網域。 將自訂網域名稱供使用，如裝載網站是擁有與該組織，例如 「 contoso.com，」 所使用的網域名稱。 此網域名稱時熟悉的員工，因為他們使用登入公司網路，或要傳送及接收電子郵件的使用者名稱的一部分。

Azure AD 就能使用之前，必須新增至您的目錄，且的自訂網域名稱。

## <a name="verified-and-unverified-domain-names"></a>驗證與未經驗證的網域名稱

為已驗證的 Azure AD 隱含評估目錄的初始網域名稱。 當系統管理員 Azure ad 新增自訂網域名稱時，則它一開始是在未經驗證的狀態。 Azure AD 不允許使用未經驗證的網域名稱，任何目錄資源。 這可確保只有一個目錄可以使用特定的網域名稱，並組織使用的網域名稱實際擁有該網域名稱。

Azure AD 查看網域名稱的網域名稱服務 (DNS) 區域檔案中的特定項目，以驗證網域名稱的擁有的權。 若要驗證的網域名稱的擁有權，系統管理員會 DNS 項目從 Azure AD Azure AD，看起來與網域名稱的 DNS 區域檔案中新增的項目。 維護網域名稱註冊機構，該網域的 DNS 區域檔案。 [新增自訂網域至 Azure AD 目錄](active-directory-add-domain.md)的文件中顯示的步驟驗證網域。

將 DNS 項目新增至區域檔案的網域名稱，不會影響其他網域服務，例如電子郵件或 web 主機服務。

## <a name="federated-and-managed-domain-names"></a>同盟和受管理的網域名稱

授與使用者同盟的登，在您的內部部署的 Active Directory 和 Azure AD 之間的體驗，您可以設定 Azure AD 中的自訂網域名稱。 設定網域的同盟需要更新中 Azure AD 有權限的資源，也可以在 Windows Server 的 Active Directory。 設定 [從 Azure AD Connect 必須完成同盟的網域，或使用 PowerShell。 從 Azure 傳統入口網站無法啟動同盟的自訂網域。 [觀看這段影片，瞭解如何設定 AD FS 的 Azure AD Connect 使用的使用者登入](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)。

已不會建立同盟的網域也稱為受管理的網域。 Azure AD 目錄的初始網域會隱含地評估為受管理的網域。

## <a name="primary-domain-names"></a>主要網域名稱

目錄的主要網域名稱是預先選取的預設值為 「 網域 」 的部分使用者名稱，為系統管理員[Azure 傳統入口網站](https://manage.windowsazure.com/)或其他入口網站，例如 Office 365 管理入口網站中建立新使用者的網域名稱。 目錄可以有單一主要網域名稱。 系統管理員可以變更為任何驗證不同盟的自訂網域的主要網域名稱或初始網域。

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Azure AD 中的網域名稱及其他 Microsoft 線上服務

Azure AD 才可以使用，例如 Exchange Online、 SharePoint Online 和 Intune 另一個 Microsoft 線上服務中必須驗證的網域名稱。 這些其他服務通常需要系統管理員，才能新增一或多個特定服務的 DNS 項目。

Azure web 應用程式會使用自己機制，以驗證網域擁有權。 必須搭配 Azure AD 驗證網域，即使其先前經過用於 Azure web 應用程式中的 Azure AD 所依賴的訂閱。 Azure web 應用程式可以使用不同的目錄，從保護 web 應用程式目錄中經過驗證的網域名稱。

## <a name="managing-domain-names"></a>管理網域名稱

從 Azure 傳統入口網站及 PowerShell，您可以完成網域管理工作。 許多工作，可以使用 Azure AD 圖形 API （在公用預覽版本） 來完成。

-   [新增及驗證的自訂網域名稱](active-directory-add-domain.md)

-   [管理 Azure 傳統入口網站中的網域](active-directory-add-manage-domain-names.md)

-   [使用 PowerShell 來管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [使用 Azure AD 圖形 API Azure AD 中管理網域名稱](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
