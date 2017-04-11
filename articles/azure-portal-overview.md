<properties
    pageTitle="Microsoft Azure 入口網站的概觀"
    description="瞭解如何使用 Microsoft Azure 入口網站。"
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Microsoft Azure 入口網站的概觀

Microsoft Azure 入口網站是您可以在此佈建和管理 Azure 資源的中央位置。  本教學課程中會您熟悉入口網站，並說明如何使用某些按鍵功能︰
- **服務商場全面涵蓋所有內容**，可讓您瀏覽數以千計的 Microsoft 和其他廠商的可以購買，及/或佈建後的項目。
- **整合和擴充的瀏覽體驗**，可讓您輕鬆找到您關注的資訊，並執行各種管理作業的資源。
- **一致的管理頁面**（或刀），可讓您管理 Azure 的各種不同的服務，透過一致的方式公開設定、 動作、 帳單資訊、 狀況監控及使用方式資料及其中許多的更多。
- 登入**個人的體驗**，可讓您建立的自訂的開始畫面，顯示您想要查看時的資訊。  您也可以自訂任何一個包含磚管理刀。

 ![Azure 入口網站的使用者介面方向][UIOrientation]

## <a name="before-you-get-started"></a>在開始之前

您必須瀏覽此教學課程的有效 Azure 訂閱。  如果沒有帳戶，然後[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/)今天。  一旦您有訂閱，您可以存取入口網站，在 [https://portal.azure.com]。

## <a name="how-to-create-a-resource"></a>如何建立資源

Azure 有 marketplace 千位數，您可以建立從單一位置的項目。  例如，假設您想要建立新的 Windows Server 2012 VM。  + 新中心是從服務商場的主要類別 curated 設定您進入點。  每個類別顯示所有類別及搜尋完整服務商場必須一主要的項目，還有一個連結。 若要建立的新的 Windows Server 2012 VM，請執行下列動作︰  

1.  Windows Server 2012 的功能，因此您可以從 [計算] 類別中選取它。  
2.  填寫表單上的一些基本輸入。
3.  按一下 [建立]，然後您 VM 會佈建立即開始。

當您的資源已經建立及管理刀隨即會開啟時，通知中心會通知您 （您可以隨時瀏覽至資源更新版本）。

![入口網站的類別][PortalCategories]


## <a name="how-to-find-your-resources"></a>如何尋找您的資源

您可以隨時將固定經常存取的資源您 startboard，但您可能需要瀏覽至您不常存取的項目。  以下所示的 [瀏覽] 中心是您移至所有資源的方式。  您可以篩選訂閱、 選擇/調整欄及管理刀個別項目上按一下 [瀏覽。

![瀏覽中心][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>如何管理及委派給資源的存取權限

您可以連線至虛擬機器使用遠端桌面此刀，從監視關鍵效能指標、 控制存取權使用角色為基礎的存取權 (RBAC) 此 VM、 設定 VM，以及執行其他重要管理工作。  委派角色為基礎的 access 很重要在管理。  按一下[這裡](./active-directory/role-based-access-control-configure.md)若要進一步瞭解。 若要委派存取資源，請執行下列動作︰

1.  瀏覽至您的資源。
2.  按一下 [基本資訊] 區段中的 [所有設定]。
3.  在 [設定] 清單中，按一下 [使用者]。
4.  命令列中，按一下 [新增]。
5.  選擇 [使用者與角色]。

![管理資源][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>如何自訂資源刀

Azure 會預先設定的資源，刀，但這些刀的磚中有您的帳戶控制項。  您也可以輕鬆地移到自訂模式來新增、 移除、 調整大小，或重新排列方塊。 若要自訂刀，請執行下列動作︰

1.  瀏覽至您的資源。
2.  按一下 [...] 按一下頂端的刀您想要自訂。
3.  按一下 [新增組件]。
4.  開始將拖放組件。  

![自訂刀][CustomizeBlades]

## <a name="how-to-get-help"></a>如何取得說明

如果您有問題，我們可以為您。  入口網站有可以指向正確的方向的說明及支援] 頁面。  根據您[支援的方案](https://azure.microsoft.com/support/plans/)，您也可以直接在入口網站中建立支援票證。  建立支援票證之後，您可以管理票證入口網站中的生命週期。 您可以前往說明及支援網頁瀏覽至 [瀏覽]-> [說明 + 支援。  

![說明及支援][HelpSupport]

## <a name="summary"></a>摘要

讓我們來檢閱複習所學內容在本教學課程︰
- 您學到如何註冊取得的訂閱，與入口網站瀏覽
- 您取得方向入口網站使用者介面，並瞭解如何建立及瀏覽資源
- 您學到如何建立的資源，並瀏覽資源
- 您學到結構或管理與如何一致的方式管理不同類型的資源
- 您學到如何自訂入口網站，以將資訊您關注的資訊至最上層和中心
- 您學到如何控制使用角色為基礎的存取權 (RBAC) 的資源的存取權
- 您學到如何取得說明及支援

建立並管理您的應用程式在雲端，完全可以簡化 Microsoft Azure 入口網站。  看看要保持最新狀態我們不斷地正在[收聽的意見反應](https://feedback.azure.com/forums/223579-azure-preview-portal/)，並改善[管理部落格](https://azure.microsoft.com/blog/topics/management/)。  [ScottGu 的部落格](http://weblogs.asp.net/scottgu)是另一個查詢的所有 Azure 更新的絕佳位置。

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
