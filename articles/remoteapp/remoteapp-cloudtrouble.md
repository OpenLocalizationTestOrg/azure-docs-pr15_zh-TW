
<properties
    pageTitle="疑難排解 RemoteApp 雲端集合建立 |Microsoft Azure"
    description="瞭解如何疑難排解 RemoteApp 雲端集合建立失敗"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>疑難排解建立 RemoteApp 雲端集合

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

如果您有建立雲端集合的問題，請查看下列資訊。

## <a name="your-image-is-invalid"></a>無效的圖像 ##
如果您正在等候 Azure 佈建您集合時，您會看到像 「 GoldImageInvalid 」 的訊息，，表示您的範本圖像不符合[定義圖像需求](remoteapp-imagereqs.md)。 因此，請移閱讀這些[需求](remoteapp-imagereqs.md)、 修正圖像，以及嘗試建立您的集合，一次。

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Azure 管理入口網站中的常見錯誤

    DNS server could not be reached
    ProvisioningTimeout

雲端集合通常會失敗，因為您建立期間使用的自訂的圖像。  如果您看到的上述錯誤，您使用的自訂圖像建立的集合，請檢查下列項目︰

- 確認您上傳的自訂圖像符合圖像需求。
- 最常常見的問題是，圖像並未正確 syspreped。  
- 驗證圖像可以內 HYPER-V 開機，或請嘗試直接在您使用圖像的 Azure 訂閱建立 IAAS VM。 如果 VM 無法啟動，不會啟動，然後這通常表示未正確準備自訂的圖像。  驗證追蹤如何建立自訂的圖像時，RemoteApp 建立的自訂範本圖像

如果您使用其中一個與您的訂閱包含 Microsoft 影像，嘗試建立一次的集合。 如果問題持續發生請連絡 Microsoft 支援服務。

    PlatformImageTrialModeOnly

如果您看到此錯誤通常這表示您已升級至付費的帳戶，但想要使用的 Microsoft 提供的圖像，只在服務的試用模式是有效的。 在此情況下，請嘗試建立您的雲端集合，但請務必指定正確的圖像。
