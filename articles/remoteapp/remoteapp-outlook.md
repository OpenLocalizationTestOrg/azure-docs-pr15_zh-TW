<properties
    pageTitle="Azure RemoteApp 中使用 Outlook |Microsoft Azure" 
    description="瞭解如何設定和使用 Outlook 中 Azure RemoteApp |Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>使用 Microsoft Outlook 中 Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 支援 Microsoft Outlook O365。 進一步瞭解如何[在 Azure RemoteApp 運作的 Office](remoteapp-officesubscription.md)。 有一些建議的設定，outlook Azure RemoteApp 中使用時。

## <a name="cached-mode"></a>快取的模式
快取的模式中 Azure RemoteApp 使用 Outlook 時建議的設定。 當您設定 Outlook 2013 帳戶使用快取 Exchange 模式時，Outlook 2013 的運作方式與儲存於離線資料檔 （OST 檔案） 的使用者在電腦上，與離線通訊錄 (OAB) 的使用者的 Microsoft Exchange 信箱的本機複本。 快取的信箱 」 和 「 OAB，則會從 O365 服務會定期更新。 進一步瞭解[快取及線上模式之間的差異](https://technet.microsoft.com/library/jj683103.aspx)。

在 [帳戶設定或變更帳戶設定，使用者可以選取**快取 Exchange 模式**] 或 [**線上模式**。 您也可以使用 「 Office 自訂工具 (OCT) 或群組原則部署一種模式或其他。  

請閱讀[啟用快取的模式的逐步指示](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc)。

## <a name="search"></a>搜尋
Azure RemoteApp 中使用 [在 Outlook 中的搜尋有限制。 Azure RemoteApp 使用集區式的 Vm 以容納使用者工作階段。 搜尋編製索引作業，取決於電腦識別碼，這是不同的 Vm 不同。 有可能每次使用者登入 Azure RemoteApp，他們會導向至新的 VM。 這表示，如果我們啟用本機搜尋，索引器會在每次執行電腦識別碼變更 （當使用者在不同的 VM）。 根據大小。OST 檔案，索引器花很長時間完成，並使用其他應用程式所需的資源。 搜尋不只會變得很慢，但可能不會產生結果。 使用線上模式帳戶的設定檔要解決此問題，但整體效能會遇到的本機快取不足 （請參閱上方的連結，如需快取及線上模式之間的差異）。 很抱歉，無法停用索引/本機搜尋和線上搜尋無法在 Outlook 2013 中的預設會啟用。
