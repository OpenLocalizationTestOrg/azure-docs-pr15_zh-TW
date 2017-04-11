<properties
    pageTitle="如何將 RemoteApp VNET 移轉到 Azure VNET |Microsoft Azure"
    description="瞭解如何將 RemoteApp VNET 移轉到 Azure VNET"
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



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>如何將混合式集合移轉到 Azure VNET RemoteApp VNET

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

好 ！ 我們已啟用您部署直接將現有 Azure 虛擬網路 (VNETs)，而不是建立 RemoteApp 特定 VNETs 混合式 RemoteApp 集合。 這個選項可讓您利用最新 VNET 功能 （例如 ExpressRoute)，您混合式集合直接網路存取權授與其他 Azure 服務和部署至該 VNET 的虛擬機器。  （這可讓您更佳的效能和更容易設定比 VNET-VNET 設定）。


例如，假設您已建立混合式稱為*OriginalCollection*與 RemoteApp VNET 稱為*RemoteAppVNET*RemoteApp 集合。 以下是將其移轉至新的 Azure VNET 稱為*AzureVNET*的步驟。

1.  在[管理入口網站](http://manage.windowsazure.com/)的 [**網路**] 索引標籤上建立 VNET，稱為*AzureVNET*，為您用於*RemoteAppVNET*使用同一個位置的 DNS 設定，與地址 （的至少其中一個*AzureVNET*子網路） 的空間。
2.  設定主機*AzureVNET*或網路連線至*OriginalCollection*是加入的網域 Active Directory 部署。
3.  在 [ **RemoteApps** ] 索引標籤上建立新 RemoteApp 集合稱為*新集合*。 （使用 [**建立與 VNET** ] 選項，**快速建立**）。
3.  設定要部署到子網路中*AzureVNET* *NewCollection* 。
4.  設定*NewCollection*時用於*OriginalCollection*使用相同的圖像和網域加入資訊。
5.  幾個小時後， *NewCollection*會顯示在作用中狀態集合清單中。

現在，如果您不需要從原始集合的任何使用者資訊移轉至新的集合，請執行下列步驟執行下一步︰

6.  刪除*OriginalCollection*。
7.  刪除*RemoteAppVNET*。

然後，大功告成 ！

或者，如果您需要從原始集合的使用者資訊移轉至新的集合，請執行下列步驟執行下一步︰

6.  傳送電子郵件[remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration)您 Azure 訂閱識別碼、 原始集合的名稱與您的新集合的名稱，並要求他們移轉您的使用者資訊。
7.  2 工作天內 RemoteApp 小組會將使用者存取清單及所有使用者的文件與使用者設定原始集合到新的集合。
8.  刪除*OriginalCollection*。
9.  刪除*RemoteAppVNET*。

然後，現在，您完成 ！

如果您有任何問題，或需要特殊的協助，請以電子郵件[remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help)。
