
<properties 
    pageTitle="設定安全性與存取權 Azure RemoteApp 超出 |Microsoft Azure"
    description="瞭解如何安全存取 Azure RemoteApp Azure Active Directory 中使用條件的存取"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>設定安全性與存取權 Azure RemoteApp 超出

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

本文中，我們會提供的系統管理員如何設定安全存取頻道從使用者的 [透過 Azure RemoteApp 開始及結束安全的資源，例如 SQL 資料庫或另一個應用程式後端的概觀。 目標是要確認會議所需的條件的唯一授權的使用者可以存取遠端應用程式]，然後從控制 Azure RemoteApp 環境，而不是從其他位置，僅可存取安全的後端。

有管理員需要查看的 3 個主要領域︰

![Azure RemoteApp 條件存取考量](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

在已讀取資訊及這些問題的解答。

## <a name="who-can-access-the-collection"></a>誰可以存取集合？
系統管理員選擇可存取遠端應用程式，集合中的使用者。 您可以使用 Azure Active Directory (Azure AD) 工作或學校帳戶 （先前稱為，「 組織帳戶 」） 或 Microsoft 帳戶 (例如@outlook.com)。 大部分的企業案例使用 Azure AD 帳戶。它們可以讓您使用條件 access 功能稍後討論也是唯一的選擇網域的集合。 本文其餘部分假設您使用的 Azure AD 帳戶與 Azure RemoteApp。

**我們已完成內容︰**

使用 Azure AD 帳戶來控制存取權 Azure RemoteApp 會提供我們兩件事︰

1.  永遠知道誰能夠存取的應用程式，我們已發佈並存取任何後端那些應用程式連線至。
2.  讓我們可以建立及刪除使用者帳戶]，[設定密碼原則，使用多重因素驗證等，我們會控制基礎 Azure AD。 

## <a name="how-is-the-collection-accessed-from-where"></a>如何存取集合？ 從何處？
經常系統管理員會要定義的存取公用網際網路的環境，例如 Azure RemoteApp 原則。 例如，要確保使用者存取從公司網路以外的環境必須使用多重因素驗證 (MFA) 來存取應用程式。或也許應該封鎖所有。

若要設定其 Azure RemoteApp 環境的條件存取原則，azure RemoteApp 系統管理員可以使用透過 Azure AD 進階版中可用的功能。 監控如何存取環境，他們也可以使用豐富的報告及提醒功能。

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>如何為 Azure RemoteApp 設定條件的存取權
我們會逐步範例案例 – Azure RemoteApp 管理員想要封鎖的存取權的環境，當公司網路以外的使用者。

>[AZURE.NOTE] 我們假設您已升級 Azure AD 進階版層及您已建立至少有一個 Azure RemoteApp 集合。

1.  在 Azure 入口網站中，按一下 [ **Active Directory** ] 索引標籤。 然後按一下您想要設定的目錄。

    讓目錄層級完成所有設定，請記住︰ 條件存取是 Azure RemoteApp 目錄和不屬性。 這也表示您必須進行這些變更目錄管理員。

2.  按一下 [**應用程式**，，然後按一下 [ **Microsoft Azure RemoteApp**設定條件的存取權。 請注意，您可以設定的每一個 「 軟體與服務 」 應用程式的條件存取您的目錄中分別。
![針對 Azure RemoteApp 設定條件的存取權](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  在 [**設定**] 索引標籤中，設定**啟用存取規則**至 [開啟]。
![Azure RemoteApp 啟用存取規則](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  您現在可以設定不同的規則，然後選擇誰套用即可︰

    1. 選擇 [完全防止使用者存取 Azure RemoteApp 外部網路環境您指定的 [**封鎖時不在公司的存取權**。
    2. 按一下以定義的 IP 位址範圍構成 「 信任的網路中 」 下的選項。 以外的所有項目會被拒絕。

5.  測試您的設定啟動 Azure RemoteApp 用戶端，從您所指定的範圍以外的 IP 位址。 您使用 Azure AD 認證登入之後，您應該會看到如下的訊息︰

![拒絕的存取 Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>未來的條件存取功能 
Azure Active Directory 小組正在處理條件在 Access 中的新功能。 系統管理員可以建立新的類型的網路以外的規則位置基礎的規則。 新功能的公用預覽應該是推出。

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>如何監視 Azure RemoteApp 存取
若要同時條件 access 使用特別的功能是 Azure Active Directory 進階版的報告功能。 您可以使用報表來存取您的環境的螢幕並偵測到任何可疑的活動。

例如，您可以看到存取 Azure RemoteApp 一樣，次數的使用者名稱和時間。

1.  在 Azure 入口網站中，按一下 [ **Active Directory**，然後按一下您的目錄。

2.  移至 [**報表**] 索引標籤。

3.  從清單中的報表，選取下**整合式應用程式**的**應用程式的使用方式**。

    您會看到一些彙總的統計資料的 Azure RemoteApp。 
![彙總的 Azure RemoteApp 存取統計資料](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  按一下 [應用程式，以顯示存取 Azure RemoteApp 的使用者資訊]。
![Azure RemoteApp 的使用者存取統計資料](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>摘要
Azure Active Directory 進階版與您可以設定存取規則，以 Azure RemoteApp （及其他服務應用程式可透過 Azure AD 軟體）。 規則僅限於目前網路位置以原則，但會在未來可延伸至企業管理的其他方面。

Azure AD 進階版也會提供報告，並監控進一步擴充管理員控制項的功能已透過 Azure RemoteApp 環境。

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>如何讓確定我安全的資源是只能從我的 Azure RemoteApp 環境？
本文的前一節我們致力於保護存取權的 Azure RemoteApp 環境。 我們已完成，選擇 [允許存取權的使用者，並設定存取規則，以進一步控制如何使用服務。

常見的案例 Azure RemoteApp 部署是遠端應用程式需要進行通訊的後端資源，例如 SQL 資料庫。 這項資源裝載任一內部部署 （例如在公司網路） 或在雲端 （例如在 Azure IaaS)。 系統管理員通常要確認的後端資源只能存取透過 Azure RemoteApp 部署的應用程式並不例如直接在使用者的電腦上執行，以及存取公用網際網路上的應用程式。 Azure RemoteApp 視為集中管理及保護安全的環境，僅透過該使用者應互動後端資源的路徑。

解決方法是將 Azure RemoteApp 環境和 [安全的資源放在同一個 Azure 虛擬網路 (VNET)。 如果資源位於不同的網站，您可以建立網站-VPN 連線，例如建立橫跨 Azure 資料中心，客戶的內部部署環境 VNet。

Azure RemoteApp 支援兩種類型的集合部署，您可以在此提供您自己的 VNET:

-   非網域聯結︰ 應用程式中會有 「 」 的視野其他的資源 VNET。 例如，這可用來連線到 SQL 資料庫使用 SQL 驗證的應用程式 （應用程式使用者進行驗證，直接對資料庫）

-   加入網域︰ 使用 Azure RemoteApp 虛擬機器加入 VNET 網域控制站。 應用程式需要 Windows 網域控制站進行驗證，才能存取後端資源時，這是很有用。
![Azure RemoteApp 網域的集合](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>如何建立 Azure 與我的內部部署環境之間安全的連線
有數種連線 Azure 及內部部署環境的設定選項。 以下有選項的概觀。

Azure RemoteApp 與您需要先設定您的 VNet，並使用它期間的集合建立程序。 

## <a name="the-complete-solution"></a>完整的解決方案
下圖顯示完整的解決方案，我們已內建安全存取頻道從使用者透過 Azure RemoteApp 其後, 端資源。
![安全 Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png)中階段 1 我們選取使用者，並建立管理如何可以存取其存取規則。 在下面的範例我們只允許存取工作從公司網路的使用者。 不相容的使用者無法存取其環境完全。
在 「 階段 2 」 我們有公開僅透過我們控制 VNet/VPN 設定後端資源。 Azure RemoteApp 置於同一個 VNet。 結果是只可以透過在其環境存取資源。


