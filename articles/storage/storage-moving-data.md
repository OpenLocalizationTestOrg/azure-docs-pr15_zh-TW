<properties
    pageTitle="將資料移至和從 Azure 儲存體 |Microsoft Azure"
    description="本文提供概觀與 Azure 儲存體移動資料的不同方法。"
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>移動資料，以及從 Azure 儲存體

如果您想要移動至 Azure 儲存體 （或反向操作） 的內部部署資料，還有各種不同的方式執行此動作。 最適合您的方式取決於您的狀況。 本文將提供不同情況和每個適當方案的快速概觀。

## <a name="building-applications"></a>建置應用程式

如果您正在建立的應用程式開發 REST API，或我們許多用戶端文件庫的內容之一是 Azure 儲存體來回移動資料的好方法。

Azure 儲存體提供.NET、 iOS、 Java、 Android、 通用 Windows 平台 (UWP)、 Xamarin、 c + +、 Node.JS、 PHP、 [注音標示和 Python 豐富型用戶端文件庫。 用戶端程式庫提供進階的功能，例如重試邏輯、 記錄及平行上傳。 您也可以直接對 REST API，這可讓 HTTP/HTTPS 要求任何語言所開發。

請參閱若要深入瞭解的 [[快速入門 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

此外，我們也提供[Azure 儲存資料移動文件庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement)是專為高效能與 Azure 資料複製文件庫。 請參閱若要深入瞭解問題資料移動文件庫的[文件](https://github.com/Azure/azure-storage-net-data-movement)。 

## <a name="quickly-viewinginteracting-with-your-data"></a>快速檢視/互動您的資料

如果您想要輕鬆檢視 Azure 儲存體資料時也需要上傳和下載您的資料，請考慮使用 Azure 儲存的檔案總管]。

請參閱[Azure 儲存體探險者](storage-explorers.md)，若要深入瞭解我們清單。

## <a name="system-administration"></a>系統管理

如果您需要或更多熟悉的命令列公用程式 （例如系統管理員），以下是幾個選項，供您︰

### <a name="azcopy"></a>AzCopy

AzCopy 是專為高效能複製的資料與 Azure 儲存體 Windows 命令列公用程式。 您也可以複製一個儲存的帳戶，或不同的儲存帳戶之間的資料。

若要深入瞭解，請參閱[將資料以 AzCopy 命令列公用程式傳送](storage-use-azcopy.md)。

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 是一個模組，可提供 cmdlet 來管理上 Azure 服務。 它位於工作為基礎的命令列殼層指令碼語言專門用來系統管理。

請參閱若要深入瞭解[使用 Azure PowerShell 的 Azure 儲存體](storage-powershell-guide-full.md)。

### <a name="azure-cli"></a>Azure CLI

Azure CLI 會提供一組開啟來源]，跨平台命令的 [Azure 服務使用。 Azure CLI 位於 Windows、 OSX，以及 Linux。

請參閱若要瞭解更多的 [[使用 Azure CLI 與 Azure 儲存體](storage-azure-cli.md)。

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>移動大量的資料與變得很慢的網路

最大的挑戰相關聯移動的大量資料內容之一是傳送時間。 如果您想為從 Azure 儲存體的資料，不必擔心網路成本或撰寫程式碼，則 Azure 匯入/匯出為適當的解決方案。

請參閱[Azure 匯入/匯出](storage-import-export-service.md)]，若要深入瞭解。

## <a name="backing-up-your-data"></a>備份您的資料

如果您只需要備份您的資料至 Azure 儲存體，Azure 備份方法移。 這是用於備份內部部署資料和 Azure Vm 功能強大的解決方案。

若要深入瞭解[Azure 備份](../backup/backup-introduction-to-azure-backup.md)，請參閱。

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>存取您資料的內部部署和雲端

如果您需要解決方案，以存取您資料的內部部署和雲端，您應該考慮使用 Azure 的混合式雲端儲存空間方案，StorSimple。 此方案包含實體 StorSimple 裝置的聰明地經常儲存資料上 SSDs，有時候資料上使用 HDDs，以及停用/備份/保存資料 Azure 儲存體。

若要深入瞭解[StorSimple](../storsimple/storsimple-overview.md) ，請參閱。

## <a name="recovering-your-data"></a>復原資料

當您擁有內部部署負載和應用程式時，您需要，可讓您繼續執行損毀事件的商務解決方案。 Azure 網站復原處理複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 複製的資料會儲存於 Azure 儲存空間，讓您不需要的次要現場資料中心。

請參閱[Azure 網站復原](../site-recovery/site-recovery-overview.md)若要深入瞭解。
