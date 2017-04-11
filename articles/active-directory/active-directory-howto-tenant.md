<properties
    pageTitle="如何取得 Azure AD 租用戶 |Microsoft Azure"
    description="如何取得註冊和建置應用程式中的 Azure Active Directory 租用戶。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>如何取得 Azure Active Directory 租用戶

Azure Active Directory (Azure AD)，在[租用戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)會是組織的人員。  則專用的執行個體的組織接收，並擁有其註冊 Microsoft 雲端服務，例如 Azure、 Microsoft Intune 或 Office 365 時的 Azure AD 服務。  每個 Azure AD 租用戶是不同，且不同於其他 Azure AD 租用戶。  

租用戶儲存了公司中的使用者，他們的密碼，使用者設定檔資料、 權限，以及等等的相關資訊。  此外，同時也會包含群組]、 [應用程式，以及 [以組織和安全性的其他資訊。

若要允許 Azure AD 使用者登入您的應用程式，您必須在自己的租用戶中註冊您的應用程式。  Azure AD 租用戶中發佈應用程式是**完全可用**。  事實上，大部分的開發人員會建立多個租用戶和應用程式的實驗，開發、 執行及測試。  您也可以可以選擇註冊取得登入並使用您的應用程式的組織購買授權，如果想要善用進階的目錄功能。

因此，您該如何取得 Azure AD 租用戶？  程序可能小的不同 if 您︰

- [使用現有的 Office 365 訂閱](#use-an-existing-office-365-subscription)
- [使用現有 Azure 訂閱相關聯的 Microsoft 帳戶](#use-an-msa-azure-subscription)
- [使用現有 Azure 訂閱相關聯的組織帳戶](#use-an-organizational-azure-subscription)
- [有以上皆非與要從頭開始](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>使用現有的 Office 365 訂閱
如果您有現有的 Office 365 訂閱，您已經有 Azure AD 租用戶 ！ 您可以使用您的 O365 帳戶登入[Azure 入口網站](https://portal.azure.com)，並開始使用 Azure AD。

## <a name="use-an-msa-azure-subscription"></a>使用 MSA Azure 訂閱
如果您有先前已註冊的 Azure 訂閱與您個人的 Microsoft 帳戶，您已經有租用戶 ！  當您登入[Azure 入口網站](https://portal.azure.com)時，您會自動被登入至您的預設租用戶。 您可以自由適時-使用此租用戶，但您可能會想要建立組織的系統管理員帳戶。

若要這麼做，請遵循下列步驟。  或者，您可能要建立新的租用戶，並追蹤類似的程序該租用戶中建立系統管理員。

1.  登入您的個人帳戶與[Azure 入口網站](https://portal.azure.com)
2.  瀏覽至 （在左側的導覽列中，[**更多服務**] 底下找到） 入口網站的 「 Azure Active Directory 」 一節
3.  您應該會自動登入到 「 預設目錄]，如果不是您可以在右上角中您帳戶的名稱，即可切換目錄。
4.  從 [**快速的工作**] 區段中，選擇 [**新增使用者**]。
5.  在 [新增使用者表單，提供下列詳細資料︰

    - 名稱: （選擇適當的值）
    - 使用者名稱: （選擇此系統管理員的使用者名稱）
    - 設定檔: （填入第一個名稱，最後一個名稱、 職稱和部門適當的值）
    - 全域管理員的角色︰

6.  當您完成新增使用者表單，並接收新的系統管理使用者的暫時密碼時，請務必記錄此密碼時，您必須使用這個新使用者登入，才能變更密碼。 您也可以直接給使用者時，使用替代電子郵件傳送的密碼。
7.  按一下 [**建立**]，建立新的使用者。
8.  若要變更暫時密碼，登入[https://login.microsoftonline.com](https://login.microsoftonline.com)以這個新使用者帳戶，然後變更要求時的密碼。


## <a name="use-an-organizational-azure-subscription"></a>使用組織的 Azure 訂閱
如果您有先前已註冊的 Azure 訂閱與您的組織帳戶，您已經有租用戶 ！  在[Azure 入口網站](https://portal.azure.com)，您應該租用戶時找到您瀏覽至 「 其他服務 」 和 「 Azure Active Directory 」。  您可以自由使用此租用戶，根據您的需要。 


## <a name="start-from-scratch"></a>從頭開始
如果以上皆是胡說八道給您時，不必擔心。  只要造訪[https://account.windowsazure.com/organization](https://account.windowsazure.com/organization)註冊 Azure 與新的組織。  當您完成程序時，您會有您專屬的 Azure AD 租用戶，且您選擇在註冊時設定的網域名稱。  您可以在[Azure 入口網站](https://portal.azure.com)中，找到您的租用戶瀏覽至 「 Azure Active Directory 」，在左側導覽。

註冊 Azure 的程序的一部分，您都必須提供信用卡詳細資料。  您可以繼續信賴-您不需要付費 Azure AD 中發佈應用程式，或建立新的租用戶。
