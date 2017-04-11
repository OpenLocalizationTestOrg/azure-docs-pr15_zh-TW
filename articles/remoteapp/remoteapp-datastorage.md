
<properties
    pageTitle="永遠不儲存機密資料自訂圖像上的 Azure RemoteApp |Microsoft Azure"
    description="瞭解如何將資料儲存於 Azure RemoteApp 中的自訂圖像的指導方針"
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


# <a name="never-store-sensitive-data-on-custom-images"></a>請勿將機密資料存放自訂圖像上

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

當您主控自己的應用程式中 Azure RemoteApp 時，請的第一步是建立自訂的圖像。 我們來建立您的應用程式提供給使用者的 VM 執行個體中使用自訂圖像。 僅應用程式與永不機密資料的可能會遺失，例如 SQL 資料庫、 個人檔案或特殊的資料檔案，例如 QuickBooks 公司檔案包含自訂的圖像。 所有的機密資料應該放置在檔案伺服器上，另一個 Azure VM，或 SQL Azure 中外部 Azure RemoteApp。 圖像應該只會裝載的應用程式連線到資料來源及呈現資料。 如需詳細資訊，檢閱[Azure RemoteApp 圖像的需求](remoteapp-imagereqs.md)。 

若要瞭解為何應該不儲存機密資料，您需要瞭解 Azure RemoteApp 的運作方式。 當集合建立或更新，在幕後多個複製或圖像的複本會建立。 所有這些 VM 執行個體是確切的自訂的圖像。當使用者啟動應用程式時，連接到這些 VM 執行個體之一。 但不是保證相同的執行個體，並應不重要，因為這些非持續。 主機應用程式的 VM 執行個體的非持續和可以損毀或刪除基礎，例如，在集合更新。 

使用者資料後集合也會佈建後，使用者可讓您啟動連線到 Vm，是常設受因為它儲存在個別的儲存空間中 VHD 我們呼叫[使用者設定檔磁碟 (UPD)](remoteapp-upd.md)，這是使用者設定檔中 c:\users\<userprofile >。 應用程式啟動時，UPD 裝載，而由作業系統的處理方式就像在本機的使用者設定檔。 進一步瞭解如何[Azure RemoteApp 儲存使用者資料與設定](remoteapp-upd.md)。

不應位於圖像的範例資料︰

- 共用的使用者存取資料
- SQL 資料庫或 QuickBooks DB
- D:\ 中的任何資料

複製到每個使用者的 UPD 的預設設定檔可以位於的範例資料︰

- 每個使用者的設定檔
- 指令碼，使用者必須保留在其 UPD

重要事項︰

- 永遠不會儲存的機密資料可以在建立自訂的圖像時遺失的圖像。
- 機密資料應該一律位於另一個檔案伺服器上，另一個 Azure VM，在雲端，並隨時外部裝載您的應用程式中 Azure RemoteApp VM 執行個體。 
- 使用者資料會儲存並保留在使用者設定檔磁碟 (UPD)


