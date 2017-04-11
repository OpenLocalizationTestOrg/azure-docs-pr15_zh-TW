
<properties
    pageTitle="Azure RemoteApp 搭配使用 Office |Microsoft Azure" 
    description="瞭解 Office 和 Azure RemoteApp 如何共同合作"
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

# <a name="using-office-with-azure-remoteapp"></a>Azure RemoteApp 搭配使用 Office

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

您有兩種裝載 Office 應用程式中 Azure RemoteApp: Office 365 專業增強版或 Office 2013 Professional Plus 試用版。

**沒有您知道有新增]，較高的推出便會取代這樣的文件？查看[如何使用 Azure RemoteApp 與 Office 365 訂閱](remoteapp-officesubscription.md)。矩形，您需要使用 Office 365 + Azure RemoteApp 的所有資訊。**

## <a name="office-365-proplus"></a>Office 365 專業增強版
您可以建立使用 Office 365 專業增強版的範本圖像 RemoteApp 集合。 這個選項可讓您將您的 Office 365 服務延伸到 RemoteApp。 您必須在現有的訂閱方案，使用者必須 Office 365 專業增強版服務，其中一個獨立的授權，或透過 Office 365 服務方案。

RemoteApp 支援 Office 365 共用電腦啟動。 當您啟用共用的電腦啟動，並使用[Office 部署工具](http://www.microsoft.com/download/details.aspx?id=36778)安裝時，Office 365 專業增強版安裝不會啟動。 當使用者符號將包含 Office 365 的集合時，Office 會檢查是否使用者擁有的 Office 365 專業增強版佈建。 如果是這樣，Office 暫時啟動 Office 365 專業增強版-此啟動會保留直到拉服務的使用者符號。

若要使用 Office 365 共用電腦啟動，您需要建立[的自訂範本](remoteapp-create-custom-image.md)，並安裝 Office 365 專業增強版，以下[這些指示進行](https://technet.microsoft.com/library/dn782858.aspx)。

您可以管理您的使用者在[Office 365 管理入口網站](https://portal.office365.com/)的 Office 365 授權。 閱讀有關[Office 365 服務方案](http://technet.microsoft.com/library/office-365-plan-options.aspx)的詳細資訊。  


## <a name="office-2013-professional-plus-trial"></a>Office 2013 Professional Plus 試用版
期間的 RemoteApp 30 天的試用版，您可以使用 Office 2013 專業增強版 （試用版） 的範本圖像建立 RemoteApp 集合。 您可以將使用者指派給使用他們的 Azure Active Directory 公司帳戶或 Microsoft 帳戶此試用集合。 需要任何其他訂閱不。

這是很棒的選項，以開始輪胎並取得好知道哪些 RemoteApp 中的 Office。 不過，此選項才預定的評估和測試。 建立使用 Office 2013 專業增強版 （試用版） 的範本圖像的 RemoteApp 集合無法轉換為生產模式和結尾的試用期停用。

## <a name="switching-from-trial-to-production"></a>從試用版切換到生產
當您啟動 30 天免費試用版時，在入口網站的 [RemoteApp] 區段中的筆記會告訴您多久剩餘試用前必須先轉換為付費的帳戶。 您可以啟動您的帳戶，並切換到 [實際執行模式下使用此筆記中的連結。

當您啟動您的帳戶時，這會影響您帳戶中的所有 RemoteApp 集合。

- 執行 Windows Server 2012 R2 或 Office 365 專業增強版的範本圖像的集合會流暢地轉換到產品。 所有使用者資料與設定，包括進行中的工作階段，則會都保持不變。
- 如果您的自訂範本圖像上傳，使用這些圖像的集合會也流暢地轉換。
- Office 2013 專業增強版 （試用版） 的範本圖像是僅供評估。 生產無法要轉換執行此範本圖像的集合。 他們會放置在 「 停用 」 的狀態。


如果您未執行您的試用版已過期轉換到生產模式，您 RemoteApp 集合會停用。 別擔心，您的設定和使用者的資料會儲存為另一個 90 天，，讓您還是可以啟動您的服務並切換到生產模式，而不遺失任何資料。
