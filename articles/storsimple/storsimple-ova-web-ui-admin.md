<properties 
   pageTitle="StorSimple 虛擬陣列 web UI 管理 |Microsoft Azure"
   description="說明如何執行基本的裝置管理工作，在 StorSimple 虛擬陣列 web ui。"
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>使用網路使用者介面，來管理您的 StorSimple 虛擬陣列

![設定程序的流程](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>概觀

教學課程此文件中的套用到 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置） 執行 2016 年 3 月上市 (GA) 版本。 本文將說明一些複雜的工作流程和 StorSimple 虛擬陣列可以執行的管理工作。 您可以使用 StorSimple 管理員 StorSimple 虛擬陣列，管理服務 （又稱為入口網站使用者介面） 的使用者介面及本機網頁 UI 的裝置。 本文著重於您可以執行使用網頁 UI 的工作。

本文包含下列的教學課程︰

- 取得服務資料加密金鑰
- 疑難排解 web UI 安裝錯誤
- 產生記錄套件
- 關閉或重新啟動您的裝置

## <a name="get-the-service-data-encryption-key"></a>取得服務資料加密金鑰

當您註冊您的第一個裝置 StorSimple 管理員服務，就會產生服務資料加密金鑰。 此按鍵，就需要其他裝置註冊 StorSimple 管理員服務服務登錄機碼。

如果您擁有您的服務資料加密金鑰和需要擷取它，請執行下列步驟本機 web 裝置的使用者介面中註冊服務。

#### <a name="to-get-the-service-data-encryption-key"></a>若要取得服務資料加密金鑰

1. 連接至本機 web ui。 移至 [**設定** > **雲端設定**。
  

2. 在頁面底部，按一下 [**取得服務資料加密金鑰**]。 索引鍵會出現。 複製並儲存此按鍵。
    
    ![取得服務的資料加密金鑰 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>疑難排解 web UI 安裝錯誤

在某些情況下當您設定的裝置在本機的 web UI，您可能會遇到錯誤。 診斷和疑難排解這類錯誤，您可以執行的診斷測試。

#### <a name="to-run-the-diagnostic-tests"></a>若要執行診斷測試

1. 在本機的 web 使用者介面，請移至**疑難排解** > **診斷測試**。

    ![執行診斷 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. 在頁面底部，按一下 [**執行診斷測試**。 這會啟動測試診斷任何可能的問題，您的網路裝置、 網頁 proxy 的時間或雲端設定。 您會收到通知裝置正在執行測試。

3. 測試完成之後，就會顯示結果。 下列範例顯示診斷測試的結果。 請注意，web proxy 設定沒有設定此裝置上，因此無法執行 web proxy 測試。 網路設定、 DNS 伺服器及時間設定的所有其他測試成功。

    ![執行診斷 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>產生的記錄套件

記錄套件包含的所有相關記錄，可協助 Microsoft 支援服務疑難排解任何裝置問題。 在這個版本中，可以透過本機網頁 UI 產生記錄套件。

#### <a name="to-generate-the-log-package"></a>若要產生記錄套件

1. 在本機的 web 使用者介面，請移至**疑難排解** > **系統記錄**。

    ![產生記錄套件 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. 在頁面底部，按一下 [**建立記錄檔套件**]。 將會建立的系統記錄。 需要幾分鐘。

    ![產生記錄套件 2](./media/storsimple-ova-web-ui-admin/image32.png)

    順利建立套件，並頁面會更新，表示套件建立的日期與時間後，將會通知您。

    ![產生記錄套件 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. 按一下 [**下載記錄套件**]。 在系統不會下載壓縮的套件。

    ![產生記錄套件 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. 您可以將它解壓縮。 下載的記錄套件，並檢視系統記錄檔。

## <a name="shut-down-and-restart-your-device"></a>關閉並重新啟動您的裝置

您可以關閉或重新啟動虛擬裝置使用本機網頁 UI。 我們建議您重新啟動之前，請採取區或離線主應用程式，然後使用的裝置上的共用。 這會最小化資料損毀的任何可能性。 

#### <a name="to-shut-down-your-virtual-device"></a>若要關閉虛擬裝置

1. 在本機的 web 使用者介面，請移至**進行的維修作業** > **Power 設定**。

2. 在頁面底部，按一下 [**關閉**]。

    ![裝置關閉 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. 將會出現警告，指出關機的裝置會中斷結果停機時間中進行任何 IO。 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-web-ui-admin/image3.png).

    ![裝置關閉警告](./media/storsimple-ova-web-ui-admin/image37.png)

    您會收到通知關機已經啟動。

    ![開始使用裝置關閉](./media/storsimple-ova-web-ui-admin/image38.png)

    裝置現在會關閉。 如果您想要啟動您的裝置，您必須執行這項作業透過 HYPER-V 管理員。

#### <a name="to-restart-your-virtual-device"></a>若要重新啟動虛擬裝置

1. 在本機的 web 使用者介面，請移至**進行的維修作業** > **Power 設定**。

2. 在頁面底部，按一下 [**重新啟動**]。

    ![裝置重新啟動](./media/storsimple-ova-web-ui-admin/image36.png)

3. 將會出現警告，指出重新啟動裝置會中斷結果停機時間中進行任何 IOs。 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-web-ui-admin/image3.png).

    ![重新啟動警告](./media/storsimple-ova-web-ui-admin/image37.png)

    您將會收到重新啟動已經啟動。

    ![重新啟動發起的租用戶](./media/storsimple-ova-web-ui-admin/image39.png)

    在進行重新啟動時，您將失去連線的 ui。 您可以監視重新啟動定期重新整理 UI。 或者，您可以監視裝置重新啟動狀態，透過 HYPER-V 管理員。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 StorSimple 管理員服務，管理您的裝置](storsimple-manager-service-administration.md)。
