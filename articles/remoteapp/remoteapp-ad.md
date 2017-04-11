
<properties 
    pageTitle="Azure AD + Active Directory Azure RemoteApp 需求 |Microsoft Azure" 
    description="瞭解如何使用 Azure RemoteApp 設定 Active Directory。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + Active Directory Azure RemoteApp 需求

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。


Azure RemoteApp 混合式集合或您想要建立同盟使用 AD Connect 雲端集合中，您需要執行下列動作。

### <a name="connect-azure-ad-and-active-directory"></a>連線 Azure AD 與 Active Directory

如果您要連接您 Azure AD 租用戶和您的內部部署 Active Directory 環境時，使用 AD 連線。 需要您只[4 按下滑鼠按鈕](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/)以連接兩個目錄。

附註-目錄同步處理時需要混合式集合。

### <a name="make-sure-your-domaincom-match"></a>請確定您"@domain.com"符合
開始之前，請確定您的內部部署樹系的 UPN 符合 Azure AD 網域的尾碼。 

所有的使用者登入 Azure RemoteApp 您設定網域的 UPN 尾碼 Azure AD 之後，會以登入“user@ <the suffix you set up>」。 請確定使用者也可以使用相同記錄user@suffix將內部部署的網域。 在某些情況下您可以設定一個網域名稱時指定不同的網域後置字元的使用者在-prem.Azure AD 在此情況下，您的使用者將無法連線至任何網域的電腦或透過 Azure RemoteApp 資源。

例如，如果您設定為 contoso.com，AAD 中您 UPN 網域後置字元，但部署/AD 一些使用者都能登入@contoso.uk,然後這些使用者將無法正確登入其集合。 使用者在 AAD 和 AD UPN 必須是相同的登入可能 」

### <a name="create-objects-for-azure-remoteapp"></a>建立物件 Azure RemoteApp
您也需要建立下列內部部署 Active Directory 物件︰

- 藉由內部部署網域加入 RDSH 結束點提供存取網域資源 RemoteApp 程式服務帳戶。
- 組織單位 (OU) 包含 RemoteApp 電腦物件。 使用 OU 的是建議 （但並非必要） 隔離的帳戶和與 RemoteApp 所要使用的原則。

您會需要兩個物件時建立 RemoteApp 集合，因此請務必先執行下列步驟執行。