<properties
    pageTitle="Azure AD Connect 和同盟 |Microsoft Azure"
    description="此頁面是使用 Azure AD Connect AD FS 作業有關的所有文件的中央位置"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect 和同盟

Azure AD Connect 可讓您設定與內部部署同盟 AD FS 和 Azure AD。 使用同盟登入，您可以讓使用者登入 Azure AD 基礎服務使用自己內部部署的密碼，在公司網路上, 不必重新輸入密碼。 AD FS 同盟選項可讓您部署新的或現有的 AD FS 在 Windows Server 2012 R2 伺服器陣列中。

本主題是同盟的詳細資訊的主相關的其他主題相關功能的 Azure AD 連線] 和 [清單] 連結。 Azure AD Connect 的連結，請參閱整合您的內部部署識別與 Azure Active Directory。

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect 同盟主題

| 主題 | 它會說明及讀取的時機 |
|:------|:-----------|
| **Azure AD Connect 使用者登入選項** ||
| [了解使用者登入選項](active-directory-aadconnect-user-signin.md) | 各種使用者登入選項的說明，以及會影響 Azure 登入使用者體驗 |
| **使用 Azure AD Connect AD FS 安裝**||
| [必要條件](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | 透過 Azure AD Connect AD FS 安裝成功的必要條件|
| [設定 AD FS 伺服器陣列](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | 如何安裝新的 AD FS 伺服器陣列使用 Azure AD Connect |
| **修改 AD FS 設定** | |
| [修復信任](active-directory-aadconnect-federation-management.md#reparing-the-trust) | 如何修復之間目前信任內部部署 AD FS 和 Office 365 / Azure |
| [新增新的 AD FS server](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | 展開與其他 AD FS server 文章初始安裝 AD FS 伺服器陣列 |
| [新增新的 AD FS WAP 伺服器](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | 展開與其他 WAP 伺服器文章初始安裝 AD FS 伺服器陣列 |
| [新增新的同盟的網域](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | 新增另一個要 Azure AD 與同盟網域 |
|**安裝的工作**||
| [新增自訂的公司標誌 / 圖例](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| 修改指定就會顯示在 AD FS 登入] 頁面自訂標誌的登入體驗 |
| [新增描述登入](active-directory-aadconnect-federation-management.md#add-sign-in-description) | 變更登入 AD FS 登入頁面上的描述 | 
| [修改 AD FS 宣告規則](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | 修改/AD FS 對應至 Azure AD Connect 同步處理設定中新增宣告規則 |


## <a name="additional-resources"></a>其他資源

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
* [Azure AD FS 部署](active-directory-aadconnect-azure-adfs.md)
* [Azure 與 Azure 流量管理員中的可用性跨地理 AD FS 部署](active-directory-adfs-in-azure-with-azure-traffic-manager.md)


