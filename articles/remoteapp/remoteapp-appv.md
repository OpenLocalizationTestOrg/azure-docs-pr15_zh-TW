<properties
    pageTitle="使用 Azure RemoteApp 應用程式 V 應用程式 |Microsoft Azure"
    description="瞭解如何使用應用程式 V 應用程式中 Azure RemoteApp。"
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



# <a name="using-app-v-apps-in-azure-remoteapp"></a>Azure RemoteApp 中使用應用程式 V 應用程式

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

您可以在需要網域加入 Azure RemoteApp 混合式集合中使用應用程式 V 應用程式。

在您開始之前，請確定應用程式 V 5.1 用戶端安裝最新的更新。 您必須建立[自訂的圖像](remoteapp-create-custom-image.md)，包括應用程式 V 用戶端。  

您可以輕鬆使用 Azure RemoteApp 使用現有的應用程式 V 基礎結構。 由於混合式集合部署到 Azure VNET 有權存取您的網域，而且 Vm 會加入網域，您可以利用您的現有應用程式 v 基礎架構與部署方法輕言主應用程式 V 應用程式中 Azure RemoteApp。 以下是一些您應該注意您目前擁有的應用程式 V 部署的類型為根據的考量︰

| 設定選項 |                       | 正數                                                               | 負數                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| 傳送方法       | 串流 （視需要） | 應用程式一定是最新和新                                     | 第一個時間延遲                                                                                    |
|                       | 裝載               | 最快;應用程式已經存在於 VM                              | 膨脹-會花費圖像空間 （127 GB 限制）                                                           |
| 應用程式的位置儲存空間  | 共用的內容        | Azure RemoteApp 執行個體的記憶體中執行的應用程式                         | 會吞掉記憶體和建議的連線到資料流 （檔案） 伺服器應用程式所在的位置                      |
|                       | 磁碟 （快取）         | 快速執行。 內容來源的可用性與無關的應用程式 | 膨脹-會花費圖像空間 （127 GB 限制）                                                           |
| 針對選取目標             | 使用者                  | 需要完整的獨立應用程式 V 基礎結構                          |                                                                                                       |
|                       | 全域 （電腦）      |  預先發佈或使用發佈為目標伺服器                         |  需要更新 Azure 圖像，如果您想要更新的應用程式 （大）。 會使用一些影像的空間。 |

 建立自訂圖像，您混合式集合之後，發佈您的應用程式、 指派給使用者，享受裝載於 Azure RemoteApp 傳遞到任何位置的任何裝置將現有的應用程式 V 應用程式。
