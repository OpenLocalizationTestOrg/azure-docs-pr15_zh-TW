<properties
    pageTitle="使用 Azure AD 應用程式 Proxy 連接器 |Microsoft Azure"
    description="說明如何建立及管理群組的 Azure AD 應用程式 Proxy 的連接器。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>發佈在個別的網路和使用連接器群組-公用預覽的位置上的應用程式

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure 傳統入口網站](active-directory-application-proxy-connectors.md)


連接器群組可在各種情況下，包括︰

- 具有多個互連式資料中心的網站。 在此情況下，您會想要保留最多資料中心內的資料傳輸越好，因為跨資料中心的連結成本和變得很慢。 您可以部署服務中的資料中心的應用程式的每個資料中心的連接器。 這個方法最小化跨資料中心的連結，並提供您的使用者完全透明的體驗。
- 管理隔離不屬於主公司網路的網路上安裝應用程式。 您可以使用 [連接器群組隔離也隔離網路的應用程式的網路上安裝專用的連接器。
- 雲端存取 IaaS 上安裝應用程式，連接器群組會提供一般服務的存取權的所有應用程式的安全性。 連接器群組不在您的公司網路上建立其他的相依性或片段的應用程式體驗。 連接器可以安裝在每個雲端資料中心，並提供只有存放在這個網路中的應用程式。 您可以安裝數個連接器達成可用性。
- 多樹系環境中的特定連接器可以部署每個樹系和設定服務特定應用程式的支援。
- 連接器群組可用於 [偵測容錯移轉損毀修復網站或備份主要的網站。
- 連接器群組可以用於提供多個公司的單一租用戶。

## <a name="prerequisite-create-your-connectors"></a>必要︰ 建立您的連接器
若要群組的連接器，您必須以確定您[已安裝多個連接器](active-directory-application-proxy-enable.md)。 當您安裝新的連接器時，它會自動連接的**預設**連接器群組。

## <a name="step-1-create-connector-groups"></a>步驟 1︰ 建立連接器群組
您可以建立多個您想要的連接器群組。 連接器群組建立是[Azure 入口網站](https://portal.azure.com)中完成。

1. 選取要移至您的目錄管理儀表板的**Azure Active Directory** 。 請選取 [**企業應用程式** > **應用程式 proxy**。

2. 選取**連接器群組**] 按鈕。 新的連接器群組刀隨即出現。

3. 輸入新的連接器群組的名稱，然後使用下拉式功能表中選取的連接線屬於此群組。

4. 完成您的連接器群組時，請選取 [**儲存**]。

## <a name="step-2-assign-applications-to-your-connector-groups"></a>步驟 2︰ 分派給您的連接器群組的應用程式
最後一個步驟是設定連接器群組將服務的每個應用程式。

1. 從目錄管理儀表板中，選取 [**企業應用程式** > **所有應用程式**> 您想要指派給連接器群組的應用程式 >**應用程式 Proxy**。
2. 底下的 [**連接器] 群組**中，使用下拉式功能表中選取您想要使用的應用程式的群組。
3. 選取 [**儲存**] 以套用變更。


## <a name="see-also"></a>另請參閱

- [啟用應用程式 Proxy](active-directory-application-proxy-enable.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件的存取](active-directory-application-proxy-conditional-access.md)
- [您正與應用程式 Proxy 的問題進行疑難排解](active-directory-application-proxy-troubleshoot.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
