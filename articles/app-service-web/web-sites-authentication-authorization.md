<properties 
    pageTitle="Azure 應用程式中的內部部署 Active Directory 驗證方法 |Microsoft Azure" 
    description="深入了解 Azure 應用程式服務中的線條的商務應用程式與內部部署的 Active Directory 驗證不同的選項" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Azure 應用程式中的內部部署 Active Directory 驗證方法 #

本文將示範如何使用內部部署 Active Directory (AD) 中[Azure 應用程式服務](../app-service/app-service-value-prop-what-is.md)進行驗證。 Azure 應用程式存放在雲端，但有項目會驗證方法內部部署 AD 使用者安全地。 

## <a name="authenticate-through-azure-active-directory"></a>透過 Azure Active Directory 驗證
Azure Active Directory 租用戶可目錄同步處理內部部署與 AD。 這種方法可讓 AD 使用者從網際網路存取您的應用程式，並使用他們的內部部署認證進行驗證。 此外，Azure 應用程式服務，提供[開啟機碼解決方案，此方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。 按幾下滑鼠，按鈕的您可以啟用驗證目錄同步處理的租用戶的 Azure 應用程式。 此方法均有下列優點︰

-   不需要任何應用程式中的驗證碼。 可讓應用程式服務執行您的驗證及花時間提供您的應用程式中的功能。
-   [Azure AD 圖形 API](http://msdn.microsoft.com/library/azure/hh974476.aspx)可存取讓您 Azure 應用程式目錄的資料。
-   提供[支援的 Azure Active Directory 的所有應用程式](/marketplace/active-directory/)，包括 Office 365、 Dynamics CRM Online、 Microsoft Intune 和數以千計的非 Microsoft 雲端應用程式 SSO。 
-   Azure Active Directory 支援角色型存取控制。 您可以使用 [Authorize(Roles="X")] 模式的最小的變更，您的程式碼。

若要查看如何撰寫與 Azure Active Directory 驗證的業務 Azure 應用程式，請參閱[建立 Azure Active Directory 驗證的業務 Azure 應用程式](web-sites-dotnet-lob-application-azure-ad.md)。

## <a name="authenticate-through-an-on-premises-sts"></a>透過內部部署 STS 驗證
如果您擁有內部部署安全性 token (STS) 等服務 Active Directory Federation Services (AD FS)，您可以使用的聯盟驗證 Azure 應用程式。 這個方法最適合的公司原則禁止 AD 資料儲存於 Azure 時。 不過，請注意下列各項︰

-   STS 拓撲必須已部署內部部署，成本和管理負擔與。
-   只有 AD FS 系統管理員可以設定[依賴廠商信任和宣告規則](http://technet.microsoft.com/library/dd807108.aspx)，可能會限制開發人員的選項。 另一方面，就可以管理與自訂每個應用程式為基礎的[宣告](http://technet.microsoft.com/library/ee913571.aspx)。
-   存取內部部署 AD 資料需要不同的方案，透過公司防火牆。

若要查看如何使用內部部署 STS 撰寫驗證的業務 Azure 應用程式，請參閱[建立 AD FS 驗證的業務 Azure 應用程式](web-sites-dotnet-lob-application-adfs.md)。
 
