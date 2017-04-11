<properties
    pageTitle="Azure RemoteApp 中的 [發佈應用程式 |Microsoft Azure"
    description="瞭解如何在 Azure RemoteApp 發佈應用程式與資源。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>如何在 RemoteApp 發佈應用程式

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

建立您 RemoteApp 集合之後，必須先發佈應用程式或您想要讓使用者使用的資源。 與您的訂閱所提供的範本圖像只需要幾個預設-共用其他應用程式發佈的應用程式，必須先將它們發行。

> [AZURE.NOTE] 您需要更新應用程式嗎？ 必須更新[圖像](remoteapp-update.md)第一次。

在 [**發佈**] 索引標籤在入口網站上 [**發佈**]。 您可以從您的範本圖像的 [**開始**] 功能表中新增應用程式，或提供應用程式範本圖像上的安裝位置的路徑。 如果您選擇新增 [**開始**] 功能表，選擇 [從清單發佈的應用程式]。 如果您選擇提供應用程式的路徑，請輸入應用程式的應用程式，路徑的名稱。 使用中的路徑-，例如 「 系統磁碟機 %」 的變數而不是 「 c:\"。

> [AZURE.NOTE] 如果您想要從**[開始**] 功能表中新增您的應用程式，您必須具備*新增至該應用程式**開始**範本圖像上的 [功能表。* 否則，RemoteApp 只會看到哪些**在 [**開始**] 功能表上，您必須在混淆。 

>若要確保您的應用程式會在**[開始**] 功能表中，將 [捷徑檔案- **.lnk** -%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 資料夾內。

> 如果您忘記密碼將應用程式新增至**[開始**] 功能表，您建立的範本時，選擇 [若要新增應用程式的路徑。 （或重新建立範本圖像，但這是稍微工作）。


 