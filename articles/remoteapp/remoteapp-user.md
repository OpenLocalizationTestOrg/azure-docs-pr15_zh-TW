<properties
    pageTitle="將使用者新增至 Azure RemoteApp 集合 |Microsoft Azure"
    description="瞭解如何將使用者新增至 Azure RemoteApp 集合"
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

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>如何將使用者新增至 Azure RemoteApp 集合

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

您的使用者可以查看及使用您的應用程式中 Azure RemoteApp 之前，您必須授與他們存取您的集合。 這是最簡單的部分︰ 在**使用者存取權限**] 索引標籤中，輸入使用者的帳戶資訊，然後按一下 [核取記號。

您需要哪些帳戶資訊？ 類型而定的集合建立 （雲端或混合式） 後您是否使用 Office 365 專業增強版該集合中。

## <a name="supported-user-identities"></a>支援的使用者身分識別

不同的集合類型 （混合式部署與雲端） 支援的 access 應用程式中使用不同的使用者身分識別。  

混合式 RemoteApp 的集合，您需要設定 Active Directory 網域基礎結構的部署和目錄整合 Azure Active Directory 租用戶 （和選擇性的單一登入）。 此外，您必須在內部部署目錄建立一些 Active Directory 物件。  

雲端 RemoteApp 的集合，任何具有支援身分識別的 Azure Active Directory 的使用者可以授與使用者存取權 RemoteApp 包含 Microsoft 帳戶。  請參閱下表。

Office 365 使用者是 Azure Active Directory。 如果他們有 Azure Active Directory 混合式部署時，目錄同步處理的帳戶，他們可以授與 RemoteApp 混合式部署中的使用者存取權。   

您可以使用此表格的身分識別和中支援的集合 Active Directory 需求的快速參考。

|使用者帳戶 |雲端   |混合式部署|
|--------------|--------|------|
|Microsoft 帳戶|     [是]|    無|
|Azure Active Directory (Azure AD)| | |
|Azure AD 只存在於雲端    |[是]    |無 |
|使用密碼同步處理 ADsync  |[是]    |[是]    |
|ADsync 不密碼同步處理|  [是] |無 |
|與 AD FS ADsync  |[是]    |[是]    |
|[第 3 廠商 Azure 支援身分識別提供者](https://msdn.microsoft.com/library/azure/jj679342.aspx) （範例偵測 （ping）） |[是]    |[是]|
|多重因素驗證    |[是]    |[是]    |

查看設定 Active Directory RemoteApp 的相關[詳細資訊](remoteapp-ad.md)。


> [AZURE.NOTE] Azure Active Directory 使用者必須與您的訂閱相關聯的租用戶。 （您可以檢視及修改您的訂閱在入口網站中的 [**設定**] 索引標籤上。 請參閱[變更租用戶的 Azure Active Directory RemoteApp 使用](remoteapp-changetenant.md)如需詳細資訊。）

## <a name="office-365-proplus-user-account-information"></a>Office 365 專業增強版的使用者帳戶資訊
如果您使用的 Office 365 專業增強版的範本圖像集合*或*如果您建立的自訂圖像，使用 Office 365，您只能新增您的訂閱的 Office 365 訂閱的預設網域的 Azure Active Directory 使用者。 如需詳細資訊，請參閱[Azure RemoteApp 使用 Office 365](remoteapp-o365.md) 。
