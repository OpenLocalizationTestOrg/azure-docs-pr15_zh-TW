<properties
   pageTitle="Azure RemoteApp 任何裝置上執行任何 Windows 應用程式 |Microsoft Azure"
   description="瞭解如何使用 Azure RemoteApp 與使用者共用任何 Windows 應用程式。"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Azure RemoteApp 任何裝置上執行任何 Windows 應用程式

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

您可以執行任何位置的 Windows 應用程式在任何裝置上，會-只要使用 Azure RemoteApp。 無論您是 10 年前的自訂應用程式或 Office 應用程式，您的使用者將不再需要繫結至特定的作業系統 （例如 Windows XP) 那些幾個應用程式。

Azure RemoteApp 與您的使用者也可以使用自己的 Android 或 Apple 裝置，並取得相同的經驗了 Windows （或在 Windows 手機上）。 這是您的 Windows 應用程式，集合中的 Windows Azure 上的虛擬機器裝載-您的使用者可以存取他們可以從任何地方有網際網路連線。 

請參閱如何執行此動作的範例。

本文中，我們要存取所有的使用者共用。 不過，您可以使用任何應用程式。 只要您可以在 Windows Server 2012 R2 的電腦上安裝應用程式，您可以使用以下步驟共用。 您可以檢閱[應用程式需求](remoteapp-appreqs.md)，以確定您的應用程式都能運作。

請注意，因為 Access 資料庫，而我們想要有幫助該資料庫，我們會進行一些額外的步驟，讓使用者存取權存取資料共用。 如果您的應用程式沒有資料庫，或您不需要您的使用者將無法存取檔案共用，您可以略過這些步驟在本教學課程

> [AZURE.NOTE] <a name="note"></a>您必須完成本教學課程 Azure 帳戶︰
> - 您可以[開啟 Azure 帳戶免費](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)︰ 取得貸項總計試用付費 Azure 服務，您可以使用和即使使用這些之後最多，您可以將該帳戶，然後使用免費 Azure 服務，例如網站。 您的信用卡不需要付費，除非您明確地變更您的設定，並要求付費。
> - 您可以[啟動 MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)︰ 您的 MSDN 訂閱可讓您貸項總計付費 Azure 服務，您可以使用每個月。


## <a name="create-a-collection-in-remoteapp"></a>建立集合中 RemoteApp

開始建立集合。 集合做為您的應用程式和使用者的容器。 每個集合根據圖像-您可以建立您自己，或使用其中一個提供您的訂閱。 在此教學課程中，我們要使用的 Office 2013 試用圖像-它包含我們想要共用的應用程式。

1. 在 Azure 入口網站中，向下捲動左側導覽樹狀目錄中，直到您看到 RemoteApp。 開啟該頁面。
2. 按一下 [**建立 RemoteApp 集合**。
3. 按一下 [**快速建立**，然後輸入您的集合名稱。
4. 選取您要用來建立集合的地區。 為了獲得最佳體驗，選取 [地理位置的位置，在您的使用者會存取應用程式最接近的區域]。 例如，在此教學課程中，使用者會位於雷蒙市，州。 最接近的 Azure 區域是**西美國**。
5. 選取您想要使用的帳單計劃。 基本的帳單計劃放置 16 使用者大型 Azure VM 時標準帳單計劃在大型的 Azure VM 有 10 個使用者。 一般範例中，為基本的計劃的運作很好的資料項目類型的工作流程。 生產力應用程式，例如 Office，您可以使用標準的計劃。
6. 最後，選取 [Office 2013 專業版圖像]。 此圖像包含 Office 2013 應用程式。 提醒-這個圖像是僅適用於試用集合和 POCs。 您 「 無法生產集合中使用此影像。
7. 現在，請按一下 [**建立 RemoteApp 集合**]。

![建立雲端集合中 RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

此舉會啟動建立您的集合，但就可以在一小時。

現在您已準備好將新增您的使用者。

## <a name="share-the-app-with-users"></a>與使用者共用應用程式

當已成功建立您的集合時，就發佈給使用者的存取權，並新增給應有存取權的使用者。

如果您瀏覽 Azure RemoteApp 節點離開時所建立的集合，請先進行回到它的方式，從 Azure 的 [首頁] 頁面。

2. 按一下您建立存取其他選項，並設定集合更舊版本的集合。
![新的 RemoteApp 雲端集合](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. 在 [**發佈**] 索引標籤上按一下 [**發佈**] 畫面的底部，然後按一下**發佈開始功能表程式。**
![發佈 RemoteApp 程式](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. 選取您想要從清單發佈的應用程式。 我們的目的，我們之所以選擇這個存取。 按一下 [**完成**]。 若要完成發佈的應用程式，請等候。
![發佈存取 RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. 應用程式完成之後發佈，不對移至新增需要存取您的應用程式的所有使用者的**使用者存取權限**] 索引標籤。 輸入您的使用者的使用者名稱 （電子郵件地址），然後再按一下 [**儲存**]。

![將使用者新增至 RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. 現在，則若要判斷您的使用者瞭解這些新的應用程式，以及存取他們的時間。 若要這麼做，請傳送您的使用者遠端桌面用戶端下載 URL 指向他們的電子郵件。
![用戶端的 RemoteApp 下載 URL](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>設定存取的存取

有些應用程式在透過 RemoteApp 部署之後，需要額外設定。 特別的存取權，我們建立 Azure 任何使用者可以存取的檔案共用。 (如果您不想要執行這項作業，您可以建立[混合式集合](remoteapp-create-hybrid-deployment.md)[，而不是雲端集合]，可讓您的使用者存取檔案與您的區域網路上的資訊。)然後，我們需要告訴我們本機磁碟機對應到他們的電腦上 Azure 檔案系統的使用者。

您身為管理員第一個部分。 然後，我們會有一些步驟，為您的使用者。

1. 啟動發佈命令列介面 (cmd.exe)。 在 [**發佈**] 索引標籤中，選取**cmd**，然後按一下 [**發佈 > 使用路徑的發佈程式**。
2. 輸入應用程式和路徑的名稱。 針對我們的目的，使用 「 檔案總管] 為 [名稱] 和 ["%systemdrive%\windows\explorer.exe 」 的路徑。
![發佈 cmd.exe 檔案。](./media/remoteapp-anyapp/ra-publishcmd.png)
3. 現在，您需要建立 Azure[儲存體帳戶](../storage/storage-create-storage-account.md)。 命名為 [我們 「 accessstorage 」，因此挑選有意義的名稱。 （若要 misquote Highlander，有可能只有一個 「 accessstorage 」。）![我們 Azure 儲存體帳戶](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. 現在回到您的儀表板，您可以前往您的儲存空間 （結束點位置） 中的路徑。 您會使用這項元，因此請確定某個位置複製。
![儲存帳戶路徑](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. 接下來，只要存放區帳戶建立之後，您會需要主要便捷鍵。 按一下 [**管理便捷鍵**，然後複製 [主要便捷鍵。
6. 現在，設定儲存帳戶的內容，然後建立新的檔案共用存取。 提高權限的 Windows PowerShell 視窗中，執行下列 cmdlet:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    讓我們共用，以下是我們執行 cmdlet:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


現在，則使用者開啟。 首先，讓您安裝[RemoteApp 用戶端](remoteapp-clients.md)的使用者。 接下來，使用者需要從帳戶的磁碟機，Azure 檔案共用您建立對應，並新增其存取檔案。 這是如何他們執行這項作業︰

1. 在 RemoteApp 用戶端存取的已發佈的應用程式。 啟動 cmd.exe 程式。
2. 執行下列命令以對應至檔案共用從您的電腦磁碟機︰

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    如果您設定為 [是**/ 常設**參數，對應的磁碟機會維持跨工作階段。
1. 現在，啟動 RemoteApp 中的檔案總管] 應用程式。 複製您想要共用的檔案共用應用程式中使用任何存取檔案。
![將 Access 檔案放入 Azure 的共用](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. 最後，開啟 Access，然後開啟您剛才共用的資料庫。 您應該會看到您在 Access 執行雲端中的資料。
![從雲端執行實數 Access 資料庫](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

現在，您可以使用 Access 在任何裝置-只要確定您安裝 RemoteApp 用戶端。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

現在，您熟悉建立的集合，請嘗試建立[Office 365 使用的集合](remoteapp-tutorial-o365anywhere.md)。 或者，您也可以建立[混合式集合](remoteapp-create-hybrid-deployment.md)可以存取您的區域網路的。

<!--Image references-->
 
