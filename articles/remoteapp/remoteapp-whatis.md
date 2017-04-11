<properties 
    pageTitle="什麼是 Azure RemoteApp？ |Microsoft Azure" 
    description="瞭解如何將共用應用程式和 Azure RemoteApp 透過任何裝置的資源。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>什麼是 Azure RemoteApp？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 讓遠端桌面服務] 以備份到 Azure 的內部部署 Microsoft RemoteApp 程式的功能。 Azure RemoteApp 可協助您提供許多不同的使用者裝置從安全、 遠端存取應用程式。 在雲端，azure RemoteApp 基本上主控的非持續終端機伺服器工作階段，您可以使用，並與您的使用者共用。

Azure RemoteApp 與您可以使用幾乎任何裝置上的使用者共用應用程式與資源。 我們會裝載您的應用程式在雲端，表示我們負責的硬體和縮放以符合使用者要求。 您所做的就是上傳您想要共用的應用程式，然後以取得您的使用者可以使用那些應用程式。 [使用者將自己的裝置取得](remoteapp-clients.md)，當您管理透過 Azure 入口網站的所有項目。 您甚至可以使用您公司的認證，可讓您確保安全性的應用程式與資料。

閱讀有關 Azure RemoteApp，或者，如果有已經相信您[立即試用這個功能](https://azure.microsoft.com/services/remoteapp/)。

有 Azure RemoteApp 的問題嗎？ 請查看我們[的常見問題集](remoteapp-faq.md)。

Azure RemoteApp 是[Microsoft 虛擬桌面基礎結構](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)的一部分。

**新 ！** 想要進一步瞭解 Azure RemoteApp？ 或準備好在驗證 Azure RemoteApp？ 加入我們每週[詢問專家網路研討會](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)。

## <a name="azure-remoteapp-collections"></a>Azure RemoteApp 集合
有兩種[Azure RemoteApp 集合](remoteapp-collections.md)︰


- **雲端集合**裝載於，並將程式的資料儲存在雲端。 登入他們的 Microsoft 帳戶或公司認證同步處理，或與 Azure Active Directory 同盟使用者可以存取應用程式。

    雲端集合時選擇您想要共用的應用程式時，不需要任何資源連線貴公司的私人網路 （例如，透過 VPN 裝置）。 如果應用程式會使用網際網路、 OneDrive，或 Azure 資源，將工作雲端集合。 這也是最快速以建立。

- **混合式集合**裝載於，並將資料儲存 Azure 雲端，但也可讓使用者存取資料和儲存在您的區域網路上的資源。 使用者可以存取應用程式登入同步處理，或與 Azure Active Directory 同盟其公司認證。

    如果您需要連線以貴公司的私人網路上的資源，請選擇 [混合式集合]。 例如，如果應用程式需要存取下列其中一項︰

    - 在您的內部網路上的檔案伺服器
    - 加速
    - 在防火牆後的資料庫

    這是通常包含許多資源私人網路無法移動至雲端的大型公司的更多實用。

不同的集合有不同的選項，包括網路，因此[集合](remoteapp-collections.md)出最適合您。 


### <a name="updating-your-collection"></a>更新您的集合
混合式部署和雲端集合之間的主要差異是軟體更新的處理方式。 與使用的預先安裝的 Office 365 專業增強版或 Office 2013 圖像雲端集合中，您不必擔心任何更新。 維護本身及更新準備持續，應用程式和作業系統的服務。

混合式集合] 中，以及使用自訂範本圖像的雲端集合，您負責維持圖像和應用程式。 網域的圖像，您可以使用工具，例如 [Windows Update、 群組原則或系統管理中心來控制更新。

更新您的自訂範本圖像後，您將新的圖像上傳至 Azure 雲端，然後更新要使用新圖像的集合。 （您可以從 Azure RemoteApp 可讓您**快速入門**] 頁面或儀表板。）

如需詳細資訊，請參閱[更新您的集合](remoteapp-update.md)。

## <a name="supported-remoteapp-clients"></a>支援的 RemoteApp 用戶端
Azure RemoteApp Mac、 iOS 和 Android 版支援 for Windows 和 Windows RT RemoteApp 用戶端應用程式，以及您在 Microsoft 遠端桌面應用程式。 您的使用者可以在其行動電話上使用這些應用程式，或計算裝置存取新 Azure RemoteApp 程式。

如需用戶端相關資訊，請參閱[存取您的應用程式中 Azure RemoteApp](remoteapp-clients.md) 。

## <a name="next-steps"></a>後續步驟
移至 ！ 試試看 ！ 這些文章可協助您開始使用 Azure RemoteApp:

- [您需要使用 Azure RemoteApp 哪一類的集合？](remoteapp-collections.md)
- [建立 Azure RemoteApp 圖像](remoteapp-imageoptions.md)
- [如何建立 Azure RemoteApp 雲端集合](remoteapp-create-cloud-deployment.md)
- [如何建立混合式集合 Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [如何授權中運作 Azure RemoteApp？](remoteapp-licensing.md)
- [使用 Azure RemoteApp 的最佳做法](remoteapp-bestpractices.md)
- [Azure RemoteApp 常見問題集](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>協助我們協助您 
您知道，除了評等這份文件] 下方，向下進行註解，您可以進行變更本身的文件嗎？ 遺漏的內容？ 有錯誤？ 我是否撰寫的項目只混亂？ 向上捲動，然後按一下 [**編輯上 GitHub**或**編輯**變更-那些以供檢閱，傳送給我們，然後，我們在登入，就會看到您的變更和這裡改良項目。