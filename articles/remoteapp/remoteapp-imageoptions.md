<properties
    pageTitle="建立 Azure RemoteApp 圖像 |Microsoft Azure"
    description="瞭解如何建立圖像 Azure RemoteApp 可用的選項"
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



# <a name="create-an-azure-remoteapp-image"></a>建立 Azure RemoteApp 圖像

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 使用圖像保留您與您的使用者共用的應用程式。 （我們需要您的圖像，並使用它來建立 Vm-的哪些使用者存取權，當使用者登入 Azure RemoteApp。）無論您是雲端或混合式部署，請以您所選擇的應用程式]，建立 Azure RemoteApp 集合，請先安裝這些應用程式建立圖像。 然後，建立使用圖像的集合，將使用者指派至該集合，並發佈] 應用程式使用者。

您有幾個選項以建立或使用的圖像。 基本[需求](remoteapp-imagereqs.md)圖像是執行 Windows Server 2012 R2，且已安裝的遠端桌面工作階段 host （主機) (RDSH) 角色。 如何取得的會變得感興趣。

圖像時，您有下列選項︰

- 您可以匯入並使用[圖像根據 Azure 虛擬機器](remoteapp-image-on-azurevm.md)。 這是很好的業務需要自訂設定的應用程式。 您可以自訂應用程式搭配使用的圖像。
- 您可以[建立並上傳的自訂的圖像](remoteapp-create-custom-image.md)。 這是很好，如果您已經有您用於內部部署遠端桌面服務部署的圖像。
- 您可以使用其中一個包含您 RemoteApp 訂閱中的[範本圖像](remoteapp-images.md)。 這些影像建立和維護 RemoteApp 小組，並且包含一些標準的應用程式 （例如 Office 套件） 您可以提供給使用者。 請注意，只有 Office 365 專業增強版影像可以用於生產設定。

無論您存取圖像的位置，或您建立的方式，您需要，請確定您瞭解[應用程式需求](remoteapp-appreqs.md)，以確保您的應用程式中 RemoteApp 也可搭配運作。 然後下, 一步是建立[雲端](remoteapp-create-cloud-deployment.md)或[混合式](remoteapp-create-hybrid-deployment.md)集合。
