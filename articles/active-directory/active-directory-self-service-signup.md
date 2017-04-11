<properties
    pageTitle="什麼是 Azure 自助註冊？ |Microsoft Azure"
    description="概觀自助註冊 Azure、 如何管理註冊程序，以及如何接手 DNS 網域名稱。"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>


# <a name="what-is-self-service-signup-for-azure"></a>什麼是 Azure 自助註冊？

本主題說明自助註冊程序，以及如何接手 DNS 網域名稱。  

## <a name="why-use-self-service-signup"></a>為什麼要使用自助註冊？

- 讓客戶他們想要更快速的服務。
- 建立電子郵件提供的服務。
- 建立電子郵件為基礎的註冊流程快速讓使用者建立身分識別使用其容易記住的公司電子郵件別名。
- 未受管理的 Azure 目錄可以稍後再做到受管理的目錄，並重複使用的其他服務。

## <a name="terms-and-definitions"></a>詞彙和定義

+ **自助註冊**︰ 這是依據使用者註冊雲端服務，並具有 Azure Active Directory (Azure AD) 中自動為其建立身分識別根據他們的電子郵件網域的方法。
+ **未受管理的 Azure 目錄**︰ 這是該身分識別，會建立的目錄。 未受管理的目錄是具有全域管理員。
+ **電子郵件驗證的使用者**︰ 這是 Azure AD 中的使用者帳戶類型。 具有自動建立之後註冊的自助優惠稱為電子郵件驗證的使用者身分識別的使用者。 電子郵件驗證的使用者是使用 creationmethod 標記目錄的一般成員 = EmailVerified。

## <a name="user-experience"></a>使用者體驗

例如，假設其電子郵件是的使用者Dan@BellowsCollege.com接收機密的檔案，透過電子郵件。 Azure 版權管理 (Azure RMS) 保護的檔案。 但波紋管大學 Dan 的組織已經無法註冊 Azure RMS，也不具有部署 Active Directory RMS。 在此情況下，Dan 可註冊個人 RMS 的免費訂閱才能讀取受保護的檔案。

如果 Dan 的第一個使用者電子郵件地址從 BellowsCollege.com 註冊此自助提供，然後為 BellowsCollege.com 在 Azure AD 中建立受管理的目錄。 如果其他使用者從 BellowsCollege.com 網域註冊這項服務或類似的自助產品，也必須建立 Azure 中未受管理目錄中的電子郵件驗證的使用者帳戶。

## <a name="admin-experience"></a>管理員體驗

系統管理員擁有 DNS 網域的名稱未受管理的 Azure 目錄可接手或合併後證明擁有權的目錄。 下節說明系統中的體驗更多詳細資料，但有摘要︰

- 當您未受管理的 Azure 目錄，您只要成為全域管理員的未受管理的目錄。 這通常稱為內部這。
- 合併未受管理的 Azure 目錄、 您受管理的 Azure 目錄中加入 DNS 網域的名稱未受管理的目錄，並建立對應的使用者-資源，因此使用者可以繼續存取不受干擾的服務。 這通常稱為外部這。

## <a name="what-gets-created-in-azure-active-directory"></a>Azure Active Directory 中建立什麼？

#### <a name="directory"></a>目錄

- Azure Active Directory 網域建立目錄，每個網域一個目錄。
- Azure AD 目錄有沒有全域管理員。

#### <a name="users"></a>使用者

- 為每位註冊的使用者，使用者會建立一個物件 Azure AD 目錄中。
- 每個使用者物件時都會標示為外部。
- 每位使用者存取指定給他們註冊服務。

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>如何宣告自助 Azure AD 目錄，為我擁有自己的網域？

您可以宣告自助 Azure AD 目錄來執行網域驗證。 網域驗證證明您擁有該網域建立 DNS 記錄。

有兩種方式要執行 Azure AD 目錄的 DNS 這項操作︰

- 內部這 （管理員探索未受管理的 Azure 目錄及想要轉換成受管理的目錄）
- 外部這 （管理員嘗試將新的網域新增至他們受管理的 Azure 目錄）

您可能會感興趣的驗證您擁有網域，因為您接手未受管理的目錄之後使用者執行自助註冊，或您可能會將新網域新增至現有的受管理目錄項目。 例如，您有名為 contoso.com 網域，且您想要新增新的網域名稱為 contoso.co.uk 或 contoso.uk。

## <a name="what-is-domain-takeover"></a>網域這是什麼？  

本節說明如何驗證您擁有網域

### <a name="what-is-domain-validation-and-why-is-it-used"></a>什麼是網域驗證，以及為何使用？

若要在上執行作業目錄，Azure AD 會要求您驗證 DNS 網域擁有權。  驗證的網域 」 可讓您宣告目錄，並將升階自助目錄到受管理的目錄，或將現有的受管理目錄合併自助目錄。

## <a name="examples-of-domain-validation"></a>網域驗證的範例

有兩種方式執行的是目錄 DNS 這︰

+ 內部這 （例如，可找到自助、 未受管理的目錄，和想要轉換成管理目錄系統管理員）
+ 外部這 （例如，管理員嘗試將新的網域新增至受管理的目錄）

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>內部這-升階自助、 未受管理的目錄，為受管理的目錄

當您執行內部這時，就會從未受管理的目錄目錄轉換成受管理的目錄。 您必須完成 DNS 網域名稱驗證]，在 DNS 區域中建立 MX 記錄或 TXT 記錄。 該動作︰

+ 驗證您擁有該網域
+ 可讓您管理目錄
+ 可讓您將全域管理員的目錄

例如，假設 IT 系統管理員從波紋管大學探索自助方案的學校使用者已註冊。 註冊的 DNS 的擁有者名稱 BellowsCollege.com，為 IT 系統管理員可以驗證 Azure 中的 DNS 名稱的擁有權，然後未受管理的目錄。 目錄然後變成受管理的目錄，並為 IT 系統管理員指派全域管理員角色，BellowsCollege.com 目錄。

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>外部這-將現有的受管理目錄合併自助目錄

在外部這，您已經有的受管理的目錄和您想要的所有使用者及未受管理的目錄中的群組，以加入受管理的目錄，而非自己的兩個分隔目錄。

身為管理員的受管理的目錄，您新增網域，而和該網域有與其相關聯的未受管理的目錄。

例如，假設您是 IT 系統管理員，以及您已經有的受管理的目錄的 Contoso.com，您的組織已註冊的網域名稱。 您會發現的使用者從您的組織有執行自助設定提供使用電子郵件的網域名稱user@contoso.co.uk,這是您的組織擁有的另一個網域名稱。 這些使用者目前有未受管理的 contoso.co.uk 目錄中的帳戶。

您不想要管理兩個不同的目錄，讓未受管理的 contoso.co.uk 目錄合併 contoso.com 您現有的 IT 管理目錄。

外部這遵循 DNS 驗證程序相同內部這。  正在的差異︰ 使用者和服務會對應至 「 IT 管理目錄。

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>執行外部這的影響為何？

與外部的這，讓使用者可以繼續存取服務不受干擾，會建立對應的使用者-資源。 個人，包括 RMS 的多個應用程式中處理的使用者-資源對應，而且使用者可以繼續存取這些服務不進行變更。 如果應用程式不有效率地處理的使用者-資源對應，外部這可能明確封鎖，防止使用者不佳的體驗。

#### <a name="directory-takeover-support-by-service"></a>目錄這支援服務

目前下列服務會支援這︰

- RMS


下列服務會推出支援這︰

- 中

下列請不要和需要其他管理員動作後外部這移轉使用者資料。

- SharePoint/OneDrive


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>如何執行 DNS 網域名稱這

您有幾個選項的方式來執行網域驗證 （如果您想要執行這）︰

1.  Azure 管理入口網站

    這被觸發執行網域元件。  如果目錄已經有網域，您將需要執行外部這的選項。

    Azure 入口網站使用您的認證登入。  瀏覽至您現有的目錄，然後，以**新增網域]**。

2.  Office 365

    若要使用您的網域及 DNS 記錄，您可以在 Office 365 中使用 [[管理網域](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/)] 頁面上的選項。 請參閱[驗證您的網域在 Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/)。

3.  Windows PowerShell

    下列步驟，才能使用 Windows PowerShell 驗證。

    步驟    |   若要使用 Cmdlet
    ------- | -------------
    建立認證的物件 | 取得認證
    Azure ad 連線 | 連線 MsolService
    取得網域的清單   | 取得 MsolDomain
    建立進行  | 取得 MsolDomainVerificationDns
    建立 DNS 記錄   | 請在您的 DNS 伺服器上執行此動作
    驗證挑戰    | 確認 MsolEmailVerifiedDomain

例如︰

1. 若要使用的用來回應自助提供的認證的 Azure AD 連線︰ 匯入模組 MSOnline $msolcred = 取得認證連線 msolservice-認證 $msolcred

2. 取得網域的清單︰

    取得 MsolDomain

3. 若要建立挑戰取得 MsolDomainVerificationDns cmdlet 執行︰

    取得 MsolDomainVerificationDns-DomainName *your_domain_name* – 模式 DnsTxtRecord

    例如︰

    取得 MsolDomainVerificationDns-DomainName contoso.com – 模式 DnsTxtRecord

4. 複製從這個命令傳回的值 （挑戰）。

    例如︰

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. 在公用的 DNS 命名空間建立 DNS txt 記錄，包含您在上一個步驟中複製的值。

    這筆記錄的名稱的上層網域名稱，因此如果您使用 Windows 伺服器 DNS 角色建立此資源記錄，將 [記錄名稱空白，只貼上值到 [文字] 方塊

6. 執行以驗證挑戰確認 MsolDomain cmdlet:

    確認 MsolEmailVerifiedDomain-DomainName *your_domain_name*

    例如︰

    確認 MsolEmailVerifiedDomain-DomainName contoso.com

成功的挑戰會傳回不會產生錯誤的提示。

## <a name="how-do-i-control-self-service-settings"></a>我要如何控制自助設定？

管理員今天有兩個自助的控制項。 他們可以控制︰

- 是否使用者可以加入透過電子郵件的目錄。
- 是否使用者可以授權給自己應用程式和服務。


### <a name="how-can-i-control-these-capabilities"></a>如何控制這些功能？

系統管理員可以設定這些功能，使用這些 Azure AD cmdlet Set-msolcompanysettings 參數︰

+ **AllowEmailVerifiedUsers**控制使用者是否可以建立或加入未受管理的目錄。 如果您將參數設定為 $false 時，沒有電子郵件驗證的使用者可以加入目錄。
+ **AllowAdHocSubscriptions**控制使用者執行自助註冊能力。 如果您將參數設定為 $false 時，沒有任何使用者可以執行自助註冊。


### <a name="how-do-the-controls-work-together"></a>如何控制項共同作業？

這兩個參數可搭配使用來定義更精確地控制自助註冊。 例如，下列命令會讓使用者執行自助，但只当這些使用者帳戶中有 Azure AD （也就是說，若要建立電子郵件驗證帳戶所需的使用者無法將自助執行設定）︰

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

下列流程圖會說明這些參數的不同組合，並產生目錄及自助條件設定。

![][1]

如需詳細資訊以及如何使用這些參數的範例，請參閱[Set-msolcompanysettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)。

## <a name="see-also"></a>另請參閱

-  [如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-msolcompanysettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
