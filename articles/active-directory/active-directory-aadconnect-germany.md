<properties
    pageTitle="Azure AD Connect Microsoft Cloud 德國"
    description="Azure AD Connect 會將您的內部部署目錄整合與 Azure Active Directory。 這個選項可讓您提供常見的身分識別與 Azure AD 整合 Office 365、 Azure 和 SaaS 應用程式。"
    keywords="簡介至 Azure AD Connect，Azure AD Connect 概觀，什麼是 Azure AD Connect，安裝 active directory 德國，黑色樹系"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect Microsoft Cloud 德國-公用預覽中

## <a name="introduction"></a>簡介
Azure AD Connect 提供您的內部部署的 Active Directory 和 Azure Active Directory 之間的同步處理。
目前的[Microsoft Cloud](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx)德國案例必須由運算子。 在使用 Microsoft Cloud 德國時，您必須注意下列︰


- 在同步處理成功的 proxy 伺服器，必須先開啟下列 Url:
    - *。 microsoftonline.de
    - *。 windows.net
    - + 憑證撤銷清單

- 當您登入您的 Azure AD 目錄時，您必須使用 onmicrosoft.de 網域中的帳戶。
- 不提供下列功能︰
    - Azure AD Connect 健康狀況
    - 自動更新
    - 密碼回寫

## <a name="download"></a>下載
您可以從入口網站中 Azure AD Connect 刀下載 Azure AD Connect。  若要找出 Azure AD Connect 刀使用下列指示進行。

### <a name="the-azure-ad-connect-blade"></a>Azure AD 連線刀

一旦您有登入 Azure 入口網站，請執行下列動作︰

1. 移至 [瀏覽
2.  選取 Azure Active Directory
3.  然後選取 [Azure AD Connect

您應該會看到下列動作︰

![Azure AD Connect 刀](media\active-directory-aadconnect-germany\germany1.png)

 
下表說明刀中顯示的功能。


標題|描述|
----- | ----- |
同步處理狀態|現在就讓我們就會知道是否啟用或停用同步處理。|
上次同步處理|上一次成功完成同步處理。|
同盟的網域|顯示目前設定的網域同盟數目。|


## <a name="installation"></a>安裝
若要安裝 Azure AD Connect，您可以使用文件[以下](active-directory-aadconnect.md#install-azure-ad-connect)。

## <a name="advanced-features-and-additional-information"></a>進階的功能及其他資訊
其他資訊及自訂設定] 或 [進階的設定，開頭[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。  本頁面提供資訊和其他指南的連結。
