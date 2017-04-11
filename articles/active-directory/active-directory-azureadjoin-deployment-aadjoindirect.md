<properties
    pageTitle="使用情況和部署考量 Azure AD 加入 |Microsoft Azure"
    description="說明如何系統管理員設定 Azure AD 加入及其使用者 （員工、 學生與其他使用者）。 同時也討論使用 Azure AD 加入不同的真實案例。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< 標籤 ms.service= 「 目錄使用中 」 ms.workload="identity 「 ms.tgt_pltfrm="na 」 ms.devlang="na 「 ms.topic="article 」 ms.date="09/27/2016 」

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>使用情況和部署考量 Azure AD 加入

## <a name="usage-scenarios-for-azure-ad-join"></a>使用情況 Azure AD 加入
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>情況 1︰ 企業主要是在雲端

Azure Active Directory 加入 （Azure AD 加入） 的優點如果您目前操作，管理身分識別您在雲端中的商務用或推出移動至雲端。 您可以使用 Azure AD 登入 Windows 10 中建立的帳戶。 完成[的第一個執行的體驗 (FRX) 程序](active-directory-azureadjoin-user-frx.md)，或加入 Azure AD 從[[設定] 功能表](active-directory-azureadjoin-user-upgrade.md)，使用者就可以加入其機器 Azure ad。  使用者也可以喜歡單一登入 (SSO) 存取雲端之類的資源 Office 365]，在瀏覽器中或 Office 應用程式中。

### <a name="scenario-2-educational-institutions"></a>案例 2︰ 教育機構

教育機構通常有兩個使用者類型︰ 學生與教職員。 教職員成員會被視為長期組織的成員。 建立內部部署帳戶是需要。 但學生是組織的 shorter-term 成員，可以在 Azure AD 管理自己的帳戶。 這表示可在雲端，而不是被儲存於內部部署推入目錄小數位數。 這也表示學生都能使用其 Azure AD 帳戶登入 Windows，並取得 Office 應用程式中的 Office 365 資源的存取權。

### <a name="scenario-3-retail-businesses"></a>情況 3︰ 零售企業

零售企業有季節性同事及長期的員工。 您通常建立內部部署帳戶，並使用長期全職員工的網域的電腦。 但季節性工作者是 shorter-term 成員的組織，而且希望管理自己的帳戶，使用者的授權可以更輕鬆地四處移動。 當您在 Office 365 授權的雲端中建立自己的使用者帳戶時，這些使用者會取得登入 Windows 和 Office Azure AD 帳戶，請使用應用程式時離開之後，您會有更多的彈性，其授權的優點。

### <a name="scenario-4-additional-scenarios"></a>案例 4︰ 其他的分析藍本

上述優勢，以及您可以從 Azure ad 加入他們的裝置，因為簡化聯結的體驗，有效裝置管理、 自動行動裝置管理註冊，請和單一登入 Azure ad 您使用者和內部部署資源。  


## <a name="deployment-considerations-for-azure-ad-join"></a>Azure AD 加入部署考量

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>讓使用者能連接公司所擁有的裝置直接 Azure AD


企業可以合作夥伴公司或組織提供雲端專用的帳戶。 下列合作夥伴可以輕鬆地存取公司應用程式與資源的單一登入。 此案例適用於存取主要是在雲端，例如 Office 365 或 SaaS Azure AD 驗證所依賴的應用程式的資源的使用者。

### <a name="prerequisites"></a>必要條件
**在企業層級 （系統管理員）**

*   Azure Active Directory azure 訂閱  

**在使用者層級**

*   在 Windows 10 （專業版和企業版）

### <a name="administrator-tasks"></a>系統管理員工作
* [設定裝置註冊](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>使用者工作
* [在安裝期間 Azure AD 有新的 Windows 10 裝置設定](active-directory-azureadjoin-user-frx.md)
* [Azure AD 的 Windows 10 裝置設定從 [設定] 功能表](active-directory-azureadjoin-user-upgrade.md)
* [加入至您的組織的個人在 Windows 10 裝置](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>在 Windows 10 版貴組織中啟用 BYOD
您可以設定您的使用者和員工使用其個人 Windows 裝置 (BYOD) 來存取公司的應用程式與資源。 您的使用者可以新增個人 Windows 裝置安全與相容性的方式存取資源 Azure AD 帳戶 （公司或學校帳戶）。

### <a name="prerequisites"></a>必要條件
**在企業層級 （系統管理員）**

*   Azure AD 訂閱

**在使用者層級**

*   在 Windows 10 （專業版和企業版）


### <a name="administrator-tasks"></a>系統管理員工作

* [設定裝置註冊](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>使用者工作
* [加入至您的組織的個人在 Windows 10 裝置](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>其他資訊
* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [不需要密碼透過 Microsoft 密碼驗證身分識別](active-directory-azureadjoin-passport.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
