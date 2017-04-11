<properties
   pageTitle="Azure RemoteApp 任何裝置上取得相同的 Office 365 體驗 |Microsoft Azure"
   description="瞭解如何使用 Azure RemoteApp 與使用者共用任何 Office 365 應用程式。"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Azure RemoteApp 任何裝置上取得相同的 Office 365 體驗

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

本文將討論如何在您公司內的任何裝置上部署 Office 365。 您的使用者可以取得的相同的功能及在 Android、 Apple 和 Windows 上的 UI 體驗。

我們會使用完成的首要這 Azure RemoteApp 比例能夠虛擬機器中的使用者可以連線至 Azure 上管理 Office 365。 此設定的虛擬機器中，我們稱為 「 雲端集合 」。

## <a name="create-a-cloud-collection"></a>建立雲端集合

第一次建立 Azure 帳戶後，瀏覽至**RemoteApp**左側的連結] 即可。
![Azure 入口網站上顯示 Azure RemoteApp](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

然後按一下 [**新**和 「 快速建立 「 繼續集合。 提供的名稱、 地區、 訂閱、 計劃，以及我們提供的圖像 「 Office Proffesional 2013 」。
![建立] 對話方塊](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

當您完成應該能如常表單集合建立程序。 此最多可能需要一小時左右。

![等待](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

一旦完成程序後，看起來應該像這樣。 如果我們按一下**發佈**我們可以看到的大部分的 Office 應用程式已發佈的我們已。
![建立的集合](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![已發佈的應用程式](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

此時您也可以新增更多使用者的**使用者存取權限**，即可存取這個集合。
![設定使用者存取權](./media/remoteapp-tutorial-o365anywhere/6-user.png)

現在讓我們來嘗試連線到 Office 365 ！

## <a name="connect-to-office-365"></a>連線至 Office 365

我們會向移至[https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/)向下捲動，然後按一下您在裝置上安裝 Azure RemoteApp 用戶端的 [**下載用戶端**。 在 Windows 版，是下面的螢幕擷取畫面。

在應用程式啟動後會要求您登入您的 Microsoft 帳戶 （之前稱為 「 Live 識別碼 」），現在使用同一個為您 Azure 帳戶。 當您登在您應該看到新的邀請通知中，按一下那里和您應該會看到如下清單下方。 接受邀請符合 Azure 帳戶擁有者電子郵件。

![新的邀請](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

看起來像是當有新的邀請。

![接受應用程式](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

一旦您接受邀請，您應該會看到 Azure RemoteApp 用戶端中的所有 Office 應用程式。

![應用程式清單](./media/remoteapp-tutorial-o365anywhere/9-work.png)

當您按一下任何一種應用程式應該在上啟動 Azure 虛擬機器，而且您應該所有設定 ！ 享受 ！

![啟動](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![powerpoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
