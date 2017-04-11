<properties
   pageTitle="部署 StorSimple 虛擬陣列中 HYPER-V 佈建"
   description="此 StorSimple 虛擬陣列部署中的第二個教學課程涉及佈建 HYPER-V 中的虛擬裝置。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>部署 StorSimple 虛擬陣列-佈建 HYPER-V 虛擬陣列

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>概觀

本教學課程中佈建適用於 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署的虛擬裝置或 StorSimple 虛擬裝置） 執行 2016 年 3 月上市 (GA) 版本。 本教學課程說明如何佈建 Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2 上執行 HYPER-V 主機系統上 StorSimple 虛擬陣列。 本文適用於 StorSimple 虛擬陣列中 Azure 傳統入口網站，以及 Microsoft Azure 政府雲端部署。

您會需要進行佈建系統管理員權限，並設定虛擬裝置。 佈建和初始設定可以需要大約 10 分鐘的時間才能完成。


## <a name="provisioning-prerequisites"></a>佈建的先決條件

您會在這裡找到佈建虛擬裝置在 Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2 上執行 HYPER-V 主機系統的必要條件。

### <a name="for-the-storsimple-manager-service"></a>如需 StorSimple 管理員服務

在開始之前，請確定︰

-   您已完成[準備 StorSimple 虛擬陣列的入口網站](storsimple-ova-deploy1-portal-prep.md)中的所有步驟。

-   您已下載的 HYPER-V 虛擬裝置圖像，從 Azure 入口網站。 如需詳細資訊，請參閱[步驟 3︰ 下載虛擬裝置圖像](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image)。

    > [AZURE.IMPORTANT] 配合 Storsimple 管理員服務可能只能用於 StorSimple 虛擬陣列上執行軟體。

### <a name="for-the-storsimple-virtual-device"></a>StorSimple 虛擬裝置

部署虛擬裝置之前，請確定︰

-   您有權存取主機系統執行 HYPER-V Windows Server 2008 R2 或更新版本可用於佈建裝置。

-   Host （主機） 系統是能夠所佈建虛擬裝置下列資源︰

    -   4 核心的最小值。

    -   至少 8 GB 的 RAM。

    -   網路介面。

    -   系統資料 500 GB 虛擬磁碟。

### <a name="for-the-network-in-the-datacenter"></a>在資料中心的網路

您開始之前，請檢閱部署 StorSimple 虛擬裝置和妥善設定的資料中心網路的網路需求。 如需詳細資訊，請參閱[StorSimple 虛擬陣列網路需求](storsimple-ova-system-requirements.md#networking-requirements)。

## <a name="step-by-step-provisioning"></a>逐步佈建

若要佈建並連線到虛擬的裝置，您將需要執行下列步驟︰

1.  請確定主機系統有足夠的資源，符合最小的虛擬裝置需求。

2.  佈建在您 hypervisor 虛擬裝置。

3.  啟動虛擬裝置，並取得 IP 位址。

下列各節說明這些步驟。

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>步驟 1︰ 確認主機系統符合最小的虛擬裝置需求

若要建立虛擬裝置，您將需要︰

-   安裝 Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2 SP1 上 HYPER-V 角色。

-   在 Microsoft Windows 用戶端的 Microsoft HYPER-V 管理員連接到主機。

您必須確定基礎硬體 （主機系統） 建立虛擬裝置都能虛擬裝置所下列資源︰

- 4 核心的最小值。
- 至少 8 GB 的 RAM。
- 網路介面。
- 系統資料 500 GB 虛擬磁碟。

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>步驟 2︰ 佈建中 hypervisor 的虛擬裝置

執行下列步驟，以提供您 hypervisor 中的裝置。

#### <a name="to-provision-a-virtual-device"></a>佈建虛擬裝置

1.  在您的 Windows Server 主機上的虛擬裝置圖像複製到本機磁碟機上。 這是您下載透過 Azure 入口網站的圖像 （VHD 或 VHDX）。 記下當您將使用此程序的更新版本中，複製圖像的位置。

2.  開啟 [**伺服器管理員**。 在右上角，按一下 [**工具]**並選取**HYPER-V 管理員**。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    如果您正在執行 Windows Server 2008 R2，開啟 [HYPER-V 管理員]。 在 [伺服器管理員] 中，按一下 [**角色 > HYPER-V > HYPER-V 管理員**。

1.  **HYPER-V 管理員**] 中 [範圍] 窗格中，以滑鼠右鍵按一下您系統節點，以開啟 [內容] 功能表，然後按一下 [**新增** > **虛擬機器**。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  在 [新的虛擬機器精靈的 [**之前**] 頁面，按一下 [**下一步**]。

1.  在 [**指定名稱及位置**] 頁面上提供的**名稱**虛擬裝置。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  在 [**指定產生器**] 頁面中，選擇裝置圖像類型，然後按 [**下一步**。 如果您使用的 Windows Server 2008 R2，才會出現此頁面。

    * 如果您已下載的.vhdx 圖像的 Windows Server 2012 或更新版本，請選擇 [**產生 2** ]。
    * 如果您下載.vhd 映像 Windows Server 2008 R2 或更新版本，請選擇 [**產生 1** ]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  **指派記憶體**頁面上，指定的**啟動記憶體**至少**8192 MB**，不要啟用動態記憶體，然後再按 [**下一步**。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  在**網路設定**] 頁面中，指定連線到網際網路虛擬切換，然後再按 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  在**虛擬硬碟連線**] 頁面上，選擇 [**使用現有的虛擬硬碟**指定虛擬裝置圖像 （.vhdx 或.vhd） 的位置，然後按 [**下一步**。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  檢閱**摘要**，然後按一下 [**完成**] 以建立虛擬機器。 但不跳尚未-您仍需要新增一些 CPU 核心和第二個磁碟機。 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  若要符合的最低需求，請您將需要 4 個核心。 若要新增虛擬處理器，與您在 [ **HYPER-V 管理員**] 視窗中，按一下清單底下**虛擬機器**，右窗格中選取的主機系統找出您剛剛建立的虛擬機器。 選取並以滑鼠右鍵按一下電腦名稱，然後選取 [**設定**]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  在 [**設定**] 頁面左窗格中，按一下 [**處理器**]。 在右窗格中，設定**的虛擬處理器數目**4 （或更多）。 按一下 [**套用**]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  若要符合的最低需求，您也需要新增 500 GB 虛擬資料磁碟。 在 [**設定**] 頁面︰

    1.  在左窗格中，選取 [ **SCSI 控制器**。
    2.  在右窗格中，選取**硬碟，**然後按一下 [**新增**]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  **硬碟**在頁面上，選取 [**虛擬硬碟**] 選項，然後按一下 [**新增**]。 這會啟動**新的虛擬硬碟精靈**。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  在 [新的虛擬硬碟精靈的 [**之前**] 頁面，按一下 [**下一步**]。

1.  在**選擇磁碟格式] 頁面**上，接受預設選項 [ **VHDX**格式。 按一下 [**下一步**]。 如果您正在執行 Windows Server 2012 R2 或 Windows Server 2008 R2，您就不會看到這個畫面。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  在**選擇磁碟類型] 頁面**中，將虛擬硬碟類型設定為**動態擴充**（建議使用）。 如果您選擇**固定的大小**磁碟，也運作，但您可能必須等候長的時間。 我們建議您不要使用**Differencing**選項。 按一下 [**下一步**]。 請注意，**以動態方式展開**Windows Server 2012 R2 與 Windows Server 2012 中的預設值。 在 Windows Server 2008 R2，預設會是**固定的大小**。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  在 [**指定名稱及位置**] 頁面上提供資料磁碟**名稱**與**位置**（您可以瀏覽至其中一個）。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  **設定磁碟**在頁面上，選取 [**建立新空白虛擬硬碟**並指定大小以**500 GB** （或更多）。 時 500 GB 的最低需求，您永遠可以提供更大的磁碟。 請注意，您無法展開或縮小一次佈建後的磁碟。 如需大小進行佈建磁碟的詳細資訊，檢閱[最佳作法](storsimple-ova-best-practices.md#configuration-best-practices)文件中的 [縮放] 區段。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  在 [**摘要**] 頁面上，檢閱虛擬資料磁碟的詳細資料，如果滿足，按一下 [**完成**] 以建立磁碟。 精靈會關閉，虛擬硬碟便會新增至您的電腦。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  您會回到 [**設定**] 頁面。 按一下**[確定]**以關閉 [**設定**] 頁面，並返回 [HYPER-V 管理員] 視窗。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>步驟 3︰ 開始虛擬裝置，並取得 IP

執行下列步驟，以啟動虛擬裝置，以便連線。

#### <a name="to-start-the-virtual-device"></a>若要開始虛擬裝置

1.  啟動虛擬裝置。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  裝置正在執行之後，請選取的裝置，請以滑鼠右鍵按一下，然後選取**連線**。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  您可能必須等候 5 到 10 分鐘準備就緒的裝置。 狀態訊息會顯示在指出進度主控台。 裝置已就緒後，請移至 [**動作**。 按下`Ctrl + Alt + Delete`登入虛擬裝置。 預設的使用者*StorSimpleAdmin* ，而預設密碼是*密碼 1*。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  基於安全性理由，裝置系統管理員的密碼到期日在第一個記錄檔。 系統會提示您變更密碼。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    輸入包含至少 8 個字元的密碼。 密碼必須符合至少有 3 登出 4 下列需求︰ 大寫、 小寫字母、 數字及特殊字元。 重新輸入密碼以進行確認。 將會通知您的密碼已變更。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  順利變更密碼之後，可能會重新啟動虛擬裝置。 若要啟動裝置，請等候。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    Windows PowerShell 主控台的裝置將會顯示進度列以及。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  6-8 步驟只適用於非 DHCP 環境中啟動時。 如果您是在 DHCP 環境中，接著略過這些步驟，然後移至步驟 9。 如果您啟動非 DHCP 環境中的裝置，您會看到下列畫面。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    您現在必須設定網路。

1.  使用`Get-HcsIpAddress`清單網路介面啟用虛擬裝置上的命令。 如果您的裝置有啟用單一網路介面，指派給這個介面的預設名稱是`Ethernet`。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  使用`Set-HcsIpAddress`cmdlet 來設定網路。 範例如下所示︰

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  初始安裝完成，而且裝置已開機之後，您會看到裝置橫幅文字。 請記下 [IP 位址和橫幅文字中顯示的 URL，來管理裝置。 連線至網頁虛擬裝置的 UI，並完成本機的設定，並登錄，您會使用這個 IP 位址。

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. （選用）如果您部署您政府雲端中的裝置，請執行此步驟。 您現在會在您的裝置上啟用美國聯邦資訊處理標準 (FIPS) 模式。 FIPS 140 標準定義加密演算法來保護機密資料的美國聯邦政府電腦系統核准使用。
    1. 若要啟用 FIPS 模式，請執行下列 cmdlet:

        `Enter-HcsFIPSMode`

    2. 重新啟動裝置之後您已啟用 FIPS 模式，讓密碼編譯驗證才會生效。

        > [AZURE.NOTE] 您可以啟用或停用您的裝置上的 FIPS 模式。 不支援交替 FIPS 與非 FIPS 模式之間的裝置。

如果您的裝置不符合最低組態需求，您會看到有錯誤 （如下所示） 的橫幅文字。 您必須修改裝置設定，讓它有足夠的資源，以符合最低需求。 然後，您可以重新啟動，並連接至裝置。 參照中的最小設定需求[步驟 1︰ 確認主機系統符合最小的虛擬裝置的需求](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements)。

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

如果您在初次設定使用本機網頁 UI 面對任何其他錯誤，請參閱下列的工作流程中[使用本機網頁 UI 管理 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。

-   若要[疑難排解 web UI 安裝](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)執行診斷測試。

-   [產生記錄封裝並檢視記錄檔](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

![[視訊] 圖示](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **可用的視訊**

觀看影片，請參閱如何佈建 HYPER-V StorSimple 虛擬陣列。

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>後續步驟

-   [為檔案伺服器設定 StorSimple 虛擬陣列](storsimple-ova-deploy3-fs-setup.md)

-   [設定您的 StorSimple 虛擬陣列 iSCSI 伺服器](storsimple-ova-deploy3-iscsi-setup.md)
