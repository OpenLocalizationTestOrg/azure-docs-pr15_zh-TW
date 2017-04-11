<properties
   pageTitle="更新您的 Azure RemoteApp 集合 |Microsoft Azure"
   description="瞭解如何更新您的 Azure RemoteApp 集合"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>更新集合中 Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

會有的時間，而，當您需要更新應用程式或 Azure RemoteApp 集合中的圖像。 如果您使用其中一個 Azure RemoteApp 訂閱後，在雲端或混合式集合中，包含圖像任何及所有的更新會處理 Azure RemoteApp 本身，因此您可以輕鬆將。

不過，如果您使用自訂的圖像 （內建從頭或您建立修改其中一個我們圖像），您負責維持圖像和應用程式。 如果您需要更新您的圖像或任何內的應用程式，您需要建立新的更新版本的圖像，然後使用此新更新的圖像取代現有的圖像，在您的集合。

因此，您該如何更新您的集合？ 是相當簡單︰

1. 更新您使用集合中的圖像。 套用任何修補程式或需要的更新，然後將其儲存為新名稱。
2. [上傳](remoteapp-uploadimage.md)或[匯入](remoteapp-image-on-azurevm.md)的圖像即可 RemoteApp。
3. 現在，在集合頁面上，按一下 [**更新**。
4. 從**範本圖像**清單中選擇新的圖像。
4. 以下是複雜-您必須決定如何處理任何集合中目前正在使用應用程式的使用者。 您有以下選擇︰
    - **授與使用者的更新後的 60 分鐘**。 一旦完成更新時，Azure RemoteApp 會顯示一則訊息任何作用中的使用者，儲存工作並登出再重新登入。 60 分鐘之後，必須登出任何作用中使用者會自動已登出。 使用者可以立即重新登入。
    - **立即登入查看的使用者**。 一旦完成更新時，請登出自動沒有任何警告的所有使用者。 如果您選擇此選項時，使用者可能會遺失資料。 不過，他們可以重新連線至應用程式立即。

1. 按一下 [核取記號，以啟動更新]。
