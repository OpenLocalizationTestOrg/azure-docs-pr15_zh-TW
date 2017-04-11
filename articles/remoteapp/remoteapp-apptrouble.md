<properties 
    pageTitle="Azure RemoteApp 疑難排解-應用程式啟動和連線失敗 |Microsoft Azure" 
    description="瞭解如何開始與應用程式中 Azure RemoteApp 連線問題進行疑難排解。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>疑難排解 Azure RemoteApp-應用程式啟動和連線失敗 

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

裝載於 Azure RemoteApp 應用程式可能無法啟動的幾個不同的原因。 本文將說明各種原因而嘗試啟動應用程式錯誤訊息，使用者可能會收到的時機。 並提到連線失敗。 （但本文會說明問題，登入 Azure RemoteApp 用戶端）。  

閱讀有關常見的錯誤訊息，因為應用程式啟動和連線失敗。

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>我們收到您設定...請嘗試再次在 10 分鐘的時間。

此錯誤表示 Azure RemoteApp 進行調整，以符合您的使用者容量需要。 在背景中正在處理的使用者需要建立更多 Azure RemoteApp VM 執行個體。 通常這約 5 分鐘，但可以採取最多 10 分鐘的時間。 有時候，不會發生此快速而立即所需資源。 例如 9 AM 情況許多使用者需要以使用您的應用程式中 Azure RemoteAppn，同時的位置。 如果發生這種情況您我們可以啟用後端的**容量模式**。 若要執行此動作開啟 Azure 支援票證及或電子郵件與我們連絡[remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)。 一定要在邀請中包含您的訂閱識別碼。  

![我們已準備您設定](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>可能不自動重新連線到您的應用程式，請重新啟動您的應用程式  

這會看到錯誤訊息通常如果使用 Azure RemoteApp，然後將您的電腦，再於 4 小時休眠，然後喚醒您的電腦和 Azure RemoteApp 用戶端嘗試自動重新連線並超過逾時。  指示使用者瀏覽回應用程式，並嘗試從 Azure RemoteApp 用戶端中開啟它。

![無法自動-連接到您的應用程式](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>暫存的設定檔的問題 

當您的使用者設定檔 （使用者設定檔磁碟） 無法裝載及使用者接收的暫時設定檔時，會發生此錯誤。  系統管理員應瀏覽至 Azure 入口網站中的集合，然後移至 [**工作階段**] 索引標籤和嘗試**登出**使用者。 這會強制完整的記錄檔的使用者工作階段中的人員，然後嘗試再次啟動應用程式的使用者。 如果失敗與 Azure 支援人員連絡，或電子郵件與我們連絡[remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)。

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp 已停止運作

此錯誤訊息表示 Azure RemoteApp 用戶端有問題，而且必須重新啟動。 指示使用者關閉︰ 選取 [**關閉程式**，然後再次啟動 Azure RemoteApp 用戶端。  如果問題持續開啟並 Azure 支援票證及或電子郵件與我們連絡[remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)。

![Azure RemoteApp 已停止運作](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>遠端桌面連線已存取此資源時，發生錯誤。 重試連線，或連絡您的系統管理員

這是一般錯誤訊息-連絡 Azure 支援及或電子郵件與我們連絡[remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)，我們可以調查。 

![一般 Azure RemoteApp 訊息](./media/remoteapp-apptrouble/ra-apptrouble4.png) 