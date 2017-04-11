<properties 
   pageTitle="安裝更新的 StorSimple 虛擬陣列 |Microsoft Azure"
   description="說明如何使用 StorSimple 虛擬陣列網頁 UI 套用更新使用入口網站及 hotfix 的方法"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>您 StorSimple 虛擬陣列安裝的更新

## <a name="overview"></a>概觀

本文將說明在 StorSimple 虛擬陣列透過本機網站使用者介面，並透過 Azure 傳統入口網站上安裝的更新所需的步驟。 您需要的軟體更新套用快速修正程式，讓您 StorSimple 虛擬陣列最新版本。 

請記住，安裝的更新或 hotfix 重新啟動您的裝置。 假設 StorSimple 虛擬陣列是單一節點裝置，進行中的任何 I/O 中斷，您的裝置體驗停機時間。 

將更新套用之前，建議您進行區或共用離線主機上第一個，然後使用的裝置。 這會最小化資料損毀的任何可能性。

> [AZURE.IMPORTANT] 如果您正在執行更新 0.1 或 GA 軟體版本，您必須使用透過本機網站使用者介面的 hotfix 方法來安裝更新 0.3。 如果您執行的更新 0.2，我們建議您安裝的更新，透過 Azure 傳統入口網站。

## <a name="use-the-local-web-ui"></a>使用本機網路使用者介面 
 
使用本機網頁 UI 時，有兩個步驟︰

- 下載更新或 hotfix
- 安裝更新或 hotfix

### <a name="download-the-update-or-the-hotfix"></a>下載更新或 hotfix

執行下列步驟，以從 Microsoft 更新目錄下載軟體更新。

#### <a name="to-download-the-update-or-the-hotfix"></a>若要下載更新或 hotfix

1. 啟動 Internet Explorer，然後瀏覽至[http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。

2. 如果這是您第一次，在這台電腦上使用 Microsoft 更新目錄時，請按一下 [**安裝**當畫面提示您安裝 Microsoft 更新目錄附加元件]。
  
3. Microsoft 更新目錄的 [搜尋] 方塊中輸入您要下載的 hotfix 知識庫 (KB) 數。 輸入**3182061**更新 0.3，然後再按一下 [**搜尋]**。

    Hotfix 清單隨即出現，例如**StorSimple 虛擬陣列更新 0.3**。

    ![搜尋目錄](./media/storsimple-ova-install-update-01/download1.png)

4. 按一下 [**新增**]。 更新會新增至選取籃]。

5. 按一下 [**檢視籃**]。

6. 按一下 [**下載**]。 指定或**瀏覽**至您要顯示的下載本機位置。 下載至指定的位置並使用更新的相同名稱的子資料夾中放置更新。 資料夾也可以複製到網路共用可從裝置存取。

7. 開啟 [複製] 資料夾，您應該會看到 Microsoft 更新獨立套件檔案`WindowsTH-KB3011067-x64`。 此檔案用來安裝的更新或 hotfix。


### <a name="install-the-update-or-the-hotfix"></a>安裝更新或 hotfix

更新或 hotfix 安裝之前，請確定您擁有更新，或 hotfix 下載至本機上您的主機或透過網路共用。 

使用此方法來執行 GA 在裝置上安裝的更新，或更新 0.1 軟體版本。 此程序會帶領小於 2 分鐘才能完成。 執行下列步驟來安裝的更新或 hotfix。


#### <a name="to-install-the-update-or-the-hotfix"></a>安裝更新或 hotfix

1. 在本機的 web 使用者介面，請移至**進行的維修作業** > **軟體更新**。

    ![更新裝置](./media/storsimple-ova-install-update-01/update1m.png)

2. 在 [**更新檔案路徑**，請輸入更新或 hotfix 的檔案名稱。 您也可以瀏覽至更新或 hotfix 安裝檔案如果放在網路共用。 按一下 [**套用**]。

    ![更新裝置](./media/storsimple-ova-install-update-01/update2m.png)

3.  會顯示警告。 指定這是單一節點裝置之後更新，重新啟動裝置有停機時間。 按一下 [檢查] 圖示。

    ![更新裝置](./media/storsimple-ova-install-update-01/update3m.png)

4. 更新會啟動。 裝置已成功更新後，會在重新啟動。 無法存取此工期在本機的 ui。

    ![更新裝置](./media/storsimple-ova-install-update-01/update5m.png)

5. 重新啟動完成之後，您會移至 [**登入**] 頁面。 若要確認裝置軟體已更新，在本機的 web UI，移至**進行的維修作業** > **軟體更新**。 顯示的軟體版本應該**10.0.0.0.0.10288.0**更新 0.3。

    > [AZURE.NOTE] 我們在本機的 web 使用者介面中的位置略有不同方式和 Azure 傳統入口網站中報告的軟體版本。 例如，本機網頁 UI 報表**10.0.0.0.0.10288** ，和 Azure 傳統入口網站報表**10.0.10288.0**同一版本的。 

    ![更新裝置](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>使用 Azure 傳統入口網站

如果執行更新 0.2，我們建議您安裝透過 Azure 傳統入口網站的更新。 入口網站的程序會要求使用者掃描、 下載及安裝更新。 此程序會需要大約 7 分鐘，才能完成。 執行下列步驟來安裝的更新或 hotfix。

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

安裝完成 （為 100%的比例工作狀態指定） 之後，請移至**裝置 > 進行的維修作業 > 軟體更新**。 顯示的軟體版本應該 10.0.10288.0。

![更新裝置](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>後續步驟

深入瞭解[管理 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。
