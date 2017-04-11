<properties 
    pageTitle="您需要使用 Azure RemoteApp 哪一類的集合？ |Microsoft Azure" 
    description="瞭解集合 Azure RemoteApp 提供的類型。" 
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



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>您需要使用 Azure RemoteApp 哪一類的集合？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 可讓您與任何裝置上的使用者共用應用程式與資源。 我們來建立要保留的應用程式和資源，集合執行這項，然後與使用者共用這些集合。 有 2 不同集合選項]，使用不同的網路和驗證選項-這是最適合您嗎？

現在就讓我們逐步執行的其他考量及充分利用您 Azure RemoteApp 集合您需要的選項。 


## <a name="quick-differences-between-the-collection-types"></a>快速集合類型之間的差異

|           | 雲端 | 混合式部署 |
|-----------|-------|--------|
|使用現有的 VNET| [是]| [是]|
|需要 AD 連線的帳戶 (DirSync)| 無| [是]|
|需要網域加入| 無| [是]|
|需要存取 VNET 網域控制站| 無| [是]|

## <a name="cloud-collections"></a>雲端集合
- 快速建立-集合快速佈建後，這表示您的應用程式使用者更快速地取得。
- 將您自己的應用程式，或共用跑到。 您可以使用自訂的圖像 （從 Azure VM 內建） 或其中一項訂閱隨附的圖像。
- 您不需要設定您的集合，與您的內部部署網域之間的連線。
- 但提供存取至您的內部部署環境資料共用，或使用非 Windows 驗證 （使用資料庫驗證） 的 SQL Server 之類的資源將，您可以選擇性地使用您自己的 Azure VNET。


[確定] 之後如何建立一個？

- 只存在於雲端？ 建立與入口網站中的 [**快速建立**] 選項。
- 雲端 + VNET 嗎？ 建立使用**建立與 VNET** ] 選項，但不要選擇加入網域。

## <a name="hybrid-collections"></a>混合式集合
- 提供內部部署網路 + Azure VNET 的完整功能。
- 包含網域加入 access 應用程式和資料。 遠端應用程式可以針對您的內部部署 Active Directory 驗證-他們可以存取您的網域中的資源。
- 啟用進階的監控和管理現有的系統管理中心解決方案與 Windows 群組原則 （透過內建在 Windows Server 2012 R2 的自訂圖像）
- 支援[ExpressRoute](https://azure.microsoft.com/services/expressroute/)您 Azure VNET 連線至您的本機 VNET。

建立使用**建立與 VNET** ] 選項，然後選擇要加入網域。

## <a name="authentication-options"></a>驗證選項
Azure RemoteApp 支援 Microsoft 帳戶和 Azure Active Directory 帳戶，但非全部集合支援所有的方法。 

| 帳戶類型                      |                                                             | 雲端 | 雲端 + VNET | 混合式部署 |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Microsoft 帳戶                 |                                                             | [是]   | [是]          | 無     |
| Azure Active Directory (Azure AD) |                                                             |       |              |        |
|                                   | Azure AD 只                                               | [是]   | [是]          | 無     |
|                                   | 使用密碼同步處理的 AD 連線                               | [是]   | [是]          | [是]    |
|                                   | AD 連線不密碼同步處理                            | [是]   | [是]          | 無     |
|                                   | AD 連線與 AD FS                                       | [是]   | [是]          | [是]    |
|                                   | 第 3 方 Azure 支援身分識別提供者 （例如偵測 （ping）） | [是]   | [是]          | [是]    |
| 多重因素驗證       |                                                             | [是]   | [是]          | [是]    |



### <a name="cloud-and-cloud--vnet"></a>雲端和雲端 + VNET 
使用雲端集合] 中，您可以使用 Microsoft 帳戶、 Azure AD 帳戶或混合兩者。 使用最適合您的使用者帳戶。

有無特定需求使用 Microsoft 帳戶。 

如果您想要使用 Azure AD 帳戶，您需要，請確定您的 Azure AD 租用戶符合一個與您的訂閱相關聯。 當您建立 Azure RemoteApp 訂閱時，您所使用的 Azure AD 租用戶已自動與您的訂閱相關聯。 授予權限的任何 Azure AD 使用者必須是該相同的租用戶。 如有需要您可以[變更 Azure AD 租用戶的學生](remoteapp-changetenant.md)與您的訂閱相關聯。
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>混合式 (或雲端 + Azure AD + AD)

使用 Azure AD + 內部部署 Active Directory 是混合式集合的必要條件。 您需要使用 AD Connect 整合兩個目錄。 但您仍需一些選項，當您將 AD 連線的設定。 

有 2 AD 連線案例-使用密碼同步處理，或使用 AD 同盟。 請參閱[AD 連線資訊](../active-directory/active-directory-aadconnect.md)，以找出哪一項這些最適合您的運作方式。

您也可以使用 Azure AD + AD 雲端集合。 請確定您所追蹤相同的設定步驟。

請查看[Azure AD + Active Directory Azure RemoteApp 需求](remoteapp-ad.md)設定 Azure AD 所需的步驟與 Active Directory。

## <a name="go-create-your-collection"></a>請建立集合 ！
好了，我想我們已想到，現在，因此只有一個項目，以執行-建立您的第一個 Azure RemoteApp 集合。

[建立雲端集合](remoteapp-create-cloud-deployment.md)] 或 [[建立混合式集合](remoteapp-create-hybrid-deployment.md)-取得建立。
