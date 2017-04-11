
<properties
    pageTitle="從 Azure RemoteApp 移轉使用者資料 |Microsoft Azure"
    description="瞭解如何移轉使用者資料與 Azure RemoteApp 登出。"
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



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>如何將資料移轉到和 Azure RemoteApp 登出

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

您可以使用許多不同的工具和方法，將[使用者資料](remoteapp-upd.md)和 Azure RemoteApp 登出。 以下是幾個方法︰

- 複製並貼上使用剪貼簿共用
- 將檔案與資料複製到 [在檔案伺服器
- 透過瀏覽器時，將檔案複製到商務用 OneDrive
- 使用重新導向的複製檔案

>[AZURE.NOTE] 
> 您無法啟用企業版或消費者同步處理代理程式的 OneDrive 他們 Azure RemoteApp[不受支援](remoteapp-onedrive.md)。

## <a name="use-copy-and-paste-in-file-explorer"></a>使用複製及貼上在檔案總管中

在 RemoteApp 部署[預設](remoteapp-redirection.md)啟用複製與貼上使用剪貼簿。 這個選項可讓使用者在他們的本機電腦和 RemoteApp 應用程式之間複製檔案。 通常，透過標準課程 RemoteApp 中使用應用程式的使用者已將檔案儲存其 UPDs-移動資料從 RemoteApp 輕鬆︰

1. [發佈為應用程式的檔案總管](remoteapp-publish.md)] RemoteApp 集合中。 （請注意，這是 「 系統管理工作）。
2. 直接啟動您發佈的檔案總管] 應用程式，然後複製並貼上的檔案，將其 UPD 和發揮其使用的使用者。

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>上傳檔案並資料檔案伺服器使用標準網路檔案複本

通常組織使用檔案的伺服器儲存一般的資料。 如果您知道伺服器名稱或位置，您的使用者可以瀏覽伺服器的區域網路，然後如同一樣上方，然後複製，其檔案。 一次您需要發佈至 RemoteApp 的 [檔案總管]，然後共用您的使用者。

>[AZURE.NOTE] 
> 檔案伺服器必須舉例來說 RemoteApp 部署到網路上。

## <a name="copy-files-to-onedrive-for-business"></a>將檔案複製到商務用 OneDrive
雖然您無法啟用 OneDrive for Business 同步處理代理程式 RemoteApp 中，您還是可以複製檔案從您 UPD 到商務用 OneDrive 透過瀏覽器。 

1. 發佈至 RemoteApp 的 [檔案總管]，然後告訴使用者透過該應用程式存取檔案。 
2. 則傳輸檔案，如果要壓縮，好讓使用者應建立.zip 檔案包含的所有檔案移至商務用 OneDrive 的最簡單的方法。
3. 要求使用者移至 Office 365 入口網站，然後移至 OneDrive 和.zip 檔案上傳。

## <a name="copy-files-by-using-drive-redirection"></a>使用 [磁碟機重新導向複製檔案

如果您已啟用[磁碟機重新導向](remoteapp-redirection.md)，您已經為使用者建立對應的磁碟機。 在此情況下，他們可以 zip 檔案重新導向的磁碟機上，然後再將其儲存到其本機電腦。