<properties
    pageTitle="Azure RemoteApp 授權 |Microsoft Azure"
    description="進一步瞭解授權的運作方式 Azure RemoteApp 中。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>如何授權中運作 Azure RemoteApp？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

讓您已設定 Azure RemoteApp 服務，建立您的範本，並準備好要將應用程式發佈到您的使用者。 但仍有一個找出的最後一個項目︰ 授權。 只是如何運作授權 RemoteApp，以及透過 RemoteApp 共用的應用程式？

RemoteApp 不需要任何 Windows 授權] 或 [遠端桌面 Cal。 您的訂閱會處理 RemoteApp 側本身。 （請參閱[資費方案](https://azure.microsoft.com/pricing/details/remoteapp)的詳細資料）。

如果您是使用其中一個影像中您的訂閱包含，您可以將任何圖像，並不需要另外取得授權安裝的應用程式共用。 例如，如果您使用 Windows Server 2012 R2 範本圖像來建立您的集合時，您可以共用系統管理中心端點保護與您的使用者。 此規則唯一的例外是 Office 365 專業增強版，需要個別訂閱，並無法共用生產集合中的 Office 2013。

如果您想要使用 Office 365 範本圖像隨附 RemoteApp，您必須是*現有*的 Office 365 專業增強版方案。 是一樣的任何使用自訂範本您發佈的 Office 365 應用程式。 您需要啟動應用程式與您的訂閱。 這是適用於試用及付費訂閱，則為 true。 如果您想要使用 Office 365 範本圖像期間試用版，*和您還沒有訂閱*，請移至 Office 365 頁面[註冊](https://go.microsoft.com/fwlink/p/?LinkID=403802)試用的訂閱。 請參閱[如何執行 RemoteApp 和 Office 搭配運作？](remoteapp-o365.md)如需詳細資訊。

如果試用期間您不想要取得 Office 365 試用版訂閱，請使用 Office 2013 專業增強版範本圖像的隨附 RemoteApp。 此範本圖像只能用於 30 天，無法轉換為付費的集合。

其他應用程式，您需要，請確定您已將共用應用程式。

意義，正確嗎？ 您可以發佈您的共用合法情況下具備資格的任何應用程式。 然後，您需要請確定您是真正有權存取共用您的程式。

請注意，您無法在雲端集合中使用 CAL 或大量授權合約。 啟動應用程式在混合式集合 （除了 Office)，您*可以*使用大量授權合約。 您只需安裝您從大量授權媒體的範本圖像上。 追蹤應用程式廠商遠端桌面環境中安裝授權資訊。
