<properties
    pageTitle="Azure MFA 與 AD FS |Microsoft Azure"
    description="這是說明如何開始使用 Azure MFA 與 AD FS Azure 多重因素驗證頁面。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na" ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Azure 多重因素驗證與 Active Directory Federation Services 快速入門



<center>![雲端](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

如果您的組織有同盟您內部部署的 Active Directory 與 Azure Active Directory 使用 AD FS，有兩種選項使用 Azure 多重因素驗證。

- 安全的雲端資源使用 Azure 多重因素驗證或 Active Directory Federation Services
- 使用 Azure 多重因素驗證伺服器的安全雲端和內部部署資源

下表摘要列出之間保護 Azure 多重因素驗證與 AD FS 資源的驗證體驗

|驗證經驗-瀏覽為基礎的應用程式|驗證經驗-非瀏覽器型的應用程式
:------------- | :------------- | :------------- |
保護 Azure AD 資源使用 Azure 多重因素驗證 |<li>執行第一個驗證步驟內部部署使用 AD FS。</li> <li>第二步是電話為基礎的方法，執行使用雲端驗證。</li>|使用者可以使用應用程式密碼以登入。
保護 Azure AD 資源使用 Active Directory Federation Services |<li>執行第一個驗證步驟內部部署使用 AD FS。</li><li>第二步是執行內部部署接受宣告。</li>|使用者可以使用應用程式密碼以登入。

同盟使用者的應用程式密碼以警告︰

- 應用程式密碼驗證使用雲端驗證]，讓他們略過同盟。 同盟積極只適用於設定應用程式密碼。
- 內部部署用戶端存取控制設定不會被應用程式密碼。
- 您會遺失內部部署的應用程式密碼驗證記錄功能。
- 帳戶停用/刪除可能需要三個小時的目錄同步處理、 延遲停用/刪除應用程式中的雲端身分識別的密碼。

## <a name="next-steps"></a>後續步驟

設定 Azure 多重因素驗證或 Azure 多重因素驗證伺服器與 AD FS 的詳細資訊請參閱下列文章︰

- [安全使用 Azure 多重因素驗證與 AD FS 雲端資源](multi-factor-authentication-get-started-adfs-cloud.md)
- [使用 Windows Server 2012 R2 AD FS Azure 多重因素驗證伺服器的安全雲端和內部部署資源](multi-factor-authentication-get-started-adfs-w2k12.md)
- [保全與 AD FS 2.0 使用 Azure 多重因素驗證伺服器的雲端和內部部署資源](multi-factor-authentication-get-started-adfs-adfs2.md)
