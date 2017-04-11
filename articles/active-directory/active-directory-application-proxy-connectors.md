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


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>發佈在個別的網路和使用連接器群組的位置上的應用程式

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure 傳統入口網站](active-directory-application-proxy-connectors.md)


連接器群組可在各種情況下，包括︰

- 具有多個互連式資料中心的網站。 在此情況下，要保留最多流量資料中心內越好，因為昂貴且緩慢通常是跨資料中心的連結。 您可以部署服務中的資料中心的應用程式的每個資料中心的連接器。 這個方法最小化跨資料中心的連結，並提供您的使用者完全透明的體驗。
- 管理隔離不屬於主公司網路的網路上安裝應用程式。 您可以使用 [連接器群組隔離也隔離網路的應用程式的網路上安裝專用的連接器。
- 雲端存取 IaaS 上安裝應用程式，連接器群組會提供一般服務的存取權的所有應用程式的安全性。 連接器群組不在您的公司網路上建立其他的相依性或片段的應用程式體驗。 連接器可以安裝在每個雲端資料中心，並提供只有存放在這個網路中的應用程式。 您可以安裝數個連接器達成可用性。
- 多樹系環境中的特定連接器可以部署每個樹系和設定服務特定應用程式的支援。
- 連接器群組可用於 [偵測容錯移轉損毀修復網站或備份主要的網站。
- 連接器群組可以用於提供多個公司的單一租用戶。

## <a name="prerequisite-create-your-connectors"></a>必要︰ 建立您的連接器
分組的連接器，請確定您[安裝多個連接器](active-directory-application-proxy-enable.md)，和您名稱他們也可以再將它們組成群組。 最後，您必須將他們指派給特定的應用程式。

## <a name="step-1-create-connector-groups"></a>步驟 1︰ 建立連接器群組
您可以建立多個您想要的連接器群組。 連接器群組建立是 Azure 傳統入口網站中完成。

1. 選取您的目錄，然後按一下 [**設定**]。  
    ![應用程式 proxy 設定的螢幕擷取畫面-按一下 [管理連接器群組](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. 在應用程式 Proxy]，按一下 [**管理連接器群組**，然後平常的群組名稱的方式來建立新的連接器群組。  
    ![應用程式 proxy 連接器群組的螢幕擷取畫面的新名稱] 群組](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>步驟 2︰ 將連接器指派給群組
連接器群組建立之後，將連接器移動到適當的群組。

1. 在 [**應用程式 Proxy**]，按一下 [**管理連接器**]。
2. 在 [**群組**] 底下，選取您要針對每個連接線的群組。 可能需要連接線會變成作用中的 [新增] 群組中的最多 10 個分鐘。  
    ![應用程式 proxy 連接器螢幕擷取畫面-從下拉式功能表中選取的群組](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>步驟 3︰ 指派給您的連接器群組的應用程式
最後一個步驟是設定連接器群組將服務的每個應用程式。

1. Azure 傳統入口網站中，在您的目錄中，選取您要指派給該群組，然後按一下 [**設定**] 應用的程式。
2. 底下的 [**連接器] 群組**中，選取您想要使用的應用程式的群組。 這項變更會立即套用。  
    ![應用程式 proxy 連接器] 群組中的螢幕擷取畫面-從下拉式功能表中選取的群組](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>另請參閱

- [啟用應用程式 Proxy](active-directory-application-proxy-enable.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件的存取](active-directory-application-proxy-conditional-access.md)
- [您正與應用程式 Proxy 的問題進行疑難排解](active-directory-application-proxy-troubleshoot.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
