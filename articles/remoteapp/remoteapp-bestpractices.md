<properties
    pageTitle="Azure RemoteApp 最佳作法 |Microsoft Azure"
    description="設定和使用 Azure RemoteApp 的最佳作法。"
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

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>設定和使用 Azure RemoteApp 的最佳作法

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

下列資訊可協助您設定與使用 Azure RemoteApp 生產力。

## <a name="connectivity"></a>連線


- 一律使用最新的用戶端版本。 使用較舊的用戶端可能會導致連線問題和其他降級的體驗。 啟用自動應用程式更新您的裝置，就能確定永遠安裝最新的用戶端。
- 一律使用最穩定及可靠的網際網路連線提供給您。  
- 使用連線最佳效能只支援 proxy 連線。  不支援 SOCKS proxy。

## <a name="applications"></a>應用程式


- 儲存並關閉 RemoteApp 應用程式，當您完成應用程式。 不關閉應用程式可能會造成資料遺失。
- 在使用中 Azure RemoteApp 前驗證自訂的應用程式。 這包含確保多工作階段的平台上使用，而且沒有使用不必要的資源，例如記憶體和可能挨餓相同的集合中的另一位使用者的 CPU。 如需的資訊，請下載並檢閱[應用程式的相容性的最佳作法遠端桌面服務](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf)。

## <a name="configuration-and-management"></a>設定及管理


- 將您的範本圖像保持在最，視需要安裝軟體更新和其他重要的修正。 這樣當 Azure RemoteApp 自動縮放以符合您的容量，每個執行個體修正。  
- 請確定您的 Active Directory Federation Services (AD FS) 部署安全和可靠。 否則用戶端驗證可能會失敗，防止使用者存取 Azure RemoteApp。
- 設定範本圖像安裝的應用程式、 角色或功能，例如為無狀態。 他們不應使用虛擬機器中持續的狀態 RemoteApp 服務中的任何執行個體。
    - 儲存所有使用者資料在使用者設定檔或其他服務中，外部的儲存位置，例如內部部署檔案共用或 OneDrive。
    - 共用的資料儲存儲存位置外部服務，例如內部部署檔案共用或 OneDrive。
    - 設定任何系統設定中的範本圖像，而不是在服務中的個別虛擬機器上。
    - 停用自動軟體更新已發佈的應用程式-請改為手動套用至範本圖像並部署從範本之前先測試。
