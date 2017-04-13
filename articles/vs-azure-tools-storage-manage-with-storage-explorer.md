<properties
    pageTitle="快速入門存放裝置總管 （預覽版本） |Microsoft Azure"
    description="管理 Azure 儲存體資源儲存檔案總管 （預覽版本）"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>快速入門存放裝置總管 （預覽版本）

## <a name="overview"></a>概觀 

Microsoft Azure 儲存檔案總管 （預覽版本） 是一種獨立應用程式，可讓您輕鬆地處理 Azure 儲存體資料上 Windows 與 OS X Linux。 在本文中，您將學習連線到和管理您的 Azure 儲存體帳戶的各種方式。

![Microsoft Azure 存放裝置總管 （預覽版本）][15]

## <a name="prerequisites"></a>必要條件

- [下載並安裝存放裝置總管 （預覽版本）](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>連線到儲存帳戶或服務

儲存檔案總管 （預覽版本） 提供連線至儲存帳戶的各種方法。 這包含連線至您 Azure 訂閱]，或是連線到儲存帳戶與其他 Azure 訂閱]，從共用服務相關聯的儲存空間帳戶和甚至連線到和管理使用 Azure 儲存模擬器本機的儲存空間︰

- [連線至 Azure 訂閱](#connect-to-an-azure-subscription)-管理屬於 Azure 訂閱的儲存空間資源。
- [使用本機開發儲存空間](#work-with-local-development-storage)-管理使用 Azure 儲存模擬器本機的儲存空間。 
- [附加至外部的儲存空間](#attach-or-detach-an-external-storage-account)-管理屬於使用儲存帳戶的帳戶名稱和索引鍵的另一個 Azure 訂閱的儲存空間資源。
- [附加儲存帳戶使用 SA](#attach-storage-account-using-sas) -管理屬於使用 SA 的另一個 Azure 訂閱的儲存空間資源。
- [使用 SA 附加服務](#attach-service-using-sas)-管理特定的儲存空間服務 （blob 容器、 佇列中或資料表） 屬於使用 SA 的另一個 Azure 訂閱。

## <a name="connect-to-an-azure-subscription"></a>連線至 Azure 的訂閱

> [AZURE.NOTE] 如果您沒有安裝 Azure 帳戶，可以[免費試用版註冊](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)] 或 [[啟動您的 Visual Studio 訂閱權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

1. 在儲存總管 （預覽版本） 中，選取 [ **Azure 帳戶設定**]。 

    ![Azure 帳戶設定][0]

1. 在左的窗格現在會顯示所有您已登入的 Microsoft 帳戶。 連線至另一個帳戶，請選取 [**新增帳戶**]，然後依照對話方塊，至少有一個作用中的 Azure 訂閱相關聯的 Microsoft 帳戶登入。

    ![新增帳戶][1]

1. 當您順利登入 Microsoft 帳戶時，左的窗格中將會填入 Azure 該帳戶相關聯的訂閱。 選取您想要的 Azure 訂閱，然後選取 [**套用]**。 （選取**所有訂閱**切換選取所有或不列出 Azure 訂閱）。

    ![選取 [Azure 訂閱][3]

1. 在左的窗格現在會顯示所選的 Azure 訂閱相關聯的儲存空間帳戶。

    ![選取 Azure 的訂閱][4]

## <a name="work-with-local-development-storage"></a>使用 [本機開發儲存空間

儲存檔案總管 （預覽版本） 可讓您使用 Azure 儲存模擬器本機存放區上工作。 這個選項可讓您針對撰寫程式碼，並無需 （因為儲存帳戶被模擬 Azure 儲存模擬器） 部署上 Azure 儲存體帳戶測試儲存空間。

>[AZURE.NOTE] Azure 儲存模擬器目前僅適用於 Windows 支援。 

1. 在儲存檔案總管 （預覽版本） 的左窗格中，展開**(本機和附加** > **儲存帳戶** > **（開發）**節點。

    ![本機開發節點][21]

1. 如果您尚未安裝 Azure 儲存模擬器，您會提示您執行此作業，透過 [資料列。 如果顯示的資料列，請選取 [**下載最新版本**，，並安裝模擬器。 

    ![下載 Azure 儲存模擬器提示][22]

1. 模擬器安裝後，您必須建立並使用本機二進位大型物件、 佇列和資料表的功能。 若要瞭解如何使用每個儲存的帳戶類型，選取適當的下列連結︰

    - [管理 Azure blob 儲存體資源](./vs-azure-tools-storage-explorer-blobs.md)
    - 管理 Azure 檔案共用存放資源-*即將推出*
    - 管理 Azure 佇列中儲存資源-*即將推出*
    - 管理 Azure 資料表儲存體資源-*即將推出*

## <a name="attach-or-detach-an-external-storage-account"></a>附加或卸離外部儲存帳戶

儲存檔案總管 （預覽版本） 提供的功能，因此可以輕易地共用儲存帳戶附加至外部儲存帳戶。 本節說明如何將附加至 （和中斷） 外部儲存帳戶。

### <a name="get-the-storage-account-credentials"></a>取得儲存帳戶認證

才能共用外部儲存帳戶，該帳戶的擁有者，必須先將認證-[客戶名稱] 和 [索引鍵有-取得帳戶，然後與想要附加至該 （外部） 帳戶的人員共用資訊。 取得儲存帳戶認證即可透過 Azure 入口網站執行下列步驟︰ 

1.  [Azure 入口網站](https://portal.azure.com)登入。
1.  選取 [**瀏覽**]。
1.  選取**儲存的帳戶**。
1.  在**儲存帳戶**刀中，選取所要的儲存空間帳戶。
1.  在選取的儲存帳戶的**設定**刀，選取 [**便捷鍵**。

    ![存取鍵] 選項][5]
    
1.  附加到儲存帳戶時，請在**便捷鍵**刀中，複製**儲存體帳戶名稱**] 和 [**索引鍵 1**的值，以使用。 

    ![便捷鍵][6]

### <a name="attach-to-an-external-storage-account"></a>附加至外部儲存帳戶
若要將附加至外部儲存帳戶，您需要帳戶的名稱與索引鍵。 *取得儲存帳戶認證*] 區段中說明如何從 Azure 入口網站取得這些值。 不過，請注意，在入口網站帳戶金鑰稱為 「 按鍵 1 」，因此位置 （預覽版本） 的 [儲存檔案總管] 的 [帳戶金鑰要求，您會輸入 （或貼上） 的 「 主要 1] 的值。 
 
1.  在儲存檔案總管 （預覽版本），選取 [**連接至 Azure 儲存體**。

    ![連線至 Azure 儲存選項][23]

1.  在 [**連接至 Azure 儲存體**] 對話方塊中，指定帳戶金鑰 （「 按鍵 1 」 值從 Azure 入口網站），然後選取**下一步**。

    ![連線至 Azure 儲存體] 對話方塊][24] 

1.  **附加外部儲存**在對話方塊中，輸入儲存體帳戶名稱**帳戶名稱**] 方塊中，指定所要的設定，並選取**下一個**完成。 

    ![附加外部儲存體] 對話方塊][8]

1.  在 [**連線摘要**] 對話方塊中，確認資訊。 如果您想要進行任何變更，選取 [**上一步**，然後重新輸入所需的設定。 完成之後，選擇 [**連線**]。

1.  連線之後，就會顯示外部儲存帳戶**（外部）**附加至儲存體帳戶名稱的文字。 

    ![結果是連線到外部儲存的帳戶][9]

### <a name="detach-from-an-external-storage-account"></a>中斷外部儲存帳戶

1.  以滑鼠右鍵按一下您想要中斷的連結，外部儲存帳戶和-從操作功能表-選取 [**中斷連結**。

    ![中斷儲存選項][10]

1.  確認訊息方塊出現時，選取**[是]**以確認分隊從外部儲存的帳戶。

## <a name="attach-storage-account-using-sas"></a>附加 SA 儲存帳戶

[SA （共用 Access 簽章）](storage/storage-dotnet-shared-access-signature-part-1.md)可讓您能夠授與存取權以暫時為基礎的儲存空間帳戶，而不需要提供 Azure 訂閱認證 Azure 訂閱的管理員。 

若要描繪出這種情況，假設使用者 a Azure 訂閱的管理員，而且想要允許使用者 b，以存取儲存帳戶有限的時間有某些權限的使用者 a:

1. 針對特定期間並以所要的權限，使用者 a 會產生 SA （包含的儲存空間帳戶的連線字串）。
1. 使用者 a 與共用文件 SA 想以存取儲存帳戶-使用者 b，在此範例中的人員。  
1. 使用者 b 使用存放裝置總管 （預覽版本） 附加至屬於使用所提供的 SA 使用者 a 的帳戶。 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>SA 取得您想要共用的帳戶

1.  在存放裝置總管 （預覽版本），以滑鼠右鍵按一下您想要共用，請儲存帳戶，-從操作功能表-選取 [**取得共用的 Access 簽名**。

    ![取得 SA 快顯功能表選項][13]

1. 在 [**共用的 Access 簽章**] 對話方塊中，指定的時間範圍與您的帳戶，您要的權限，然後選取 [**建立**]。

    ![取得 SA] 對話方塊][14]
 
1. 第二個的**共用存取簽章**] 對話方塊會顯示 SA。 選取要將其複製到剪貼簿的**連線字串**] 旁的**複本**]。 選取 [**關閉**以關閉對話方塊。

### <a name="attach-to-the-shared-account-using-the-sas"></a>附加至 SA 共用帳戶

1.  在儲存檔案總管 （預覽版本），選取 [**連接至 Azure 儲存體**。

    ![連線至 Azure 儲存選項][23]

1.  在 [**連接至 Azure 儲存體**] 對話方塊中，指定連接字串，，然後選取 [**下一步**。

    ![連線至 Azure 儲存體] 對話方塊][24] 

1.  在 [**連線摘要**] 對話方塊中，確認資訊。 如果您想要進行任何變更，選取 [**上一步**，然後重新輸入所需的設定。 完成之後，選擇 [**連線**]。

1.  一旦附加，儲存帳戶會顯示附加到您所提供的帳戶名稱的文字 (SA)。

    ![附加至 SA 帳戶的結果][17]

## <a name="attach-service-using-sas"></a>附加服務使用 SA

[] 區段中[使用 SA 附加儲存帳戶](#attach-storage-account-using-sas)說明如何 Azure 訂閱管理員可以暫時存取權授予儲存帳戶產生 （與共用） SA 儲存帳戶。 同樣地，SA 可能會產生儲存帳戶內特定服務 （blob 容器、 佇列中或資料表）。  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>產生的 SA 您想要共用的服務

在此情況下，服務可以是 blob 容器、 佇列中或表格。 下列各節說明如何產生的 SA 列出服務︰

- [SA 取得 blob 容器](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- SA 取得檔案共用-*即將推出*
- SA 取得佇列-*即將推出*
- 取得 SA 表格-*即將推出*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>附加至使用 SA 共用的帳戶服務

1.  在儲存檔案總管 （預覽版本），選取 [**連接至 Azure 儲存體**。

    ![連線至 Azure 儲存選項][23]

1.  在 [**連接至 Azure 儲存體**] 對話方塊中，指定 SA URI，然後選取 [**下一步**。

    ![連線至 Azure 儲存體] 對話方塊][24] 

1.  在 [**連線摘要**] 對話方塊中，確認資訊。 如果您想要進行任何變更，選取 [**上一步**，然後重新輸入所需的設定。 完成之後，選擇 [**連線**]。

1.  之後，就會顯示新附加的服務**(服務 SA)**節點下。

    ![附加至使用 SA 的共用服務的結果][20]

## <a name="search-for-storage-accounts"></a>儲存帳戶的搜尋

如果您有儲存帳戶的完整清單，快速找出特定的儲存空間帳戶是使用 [搜尋] 方塊的左窗格的頂端。 

您輸入搜尋方塊中，在左的窗格會顯示只儲存空間，符合您最多輸入搜尋值的帳戶點。 以下螢幕擷取畫面所示我已搜尋所有的儲存空間帳戶儲存體帳戶名稱包含 「 tarcher 」 文字的位置。

![儲存帳戶搜尋][11]
    
若要清除搜尋，請在 [搜尋] 方塊中選取 [ **x** ] 按鈕。

## <a name="next-steps"></a>後續步驟
- [管理 Azure blob 儲存體資源儲存檔案總管 （預覽版本）](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
