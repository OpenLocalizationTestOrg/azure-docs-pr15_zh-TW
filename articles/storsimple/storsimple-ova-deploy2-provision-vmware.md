<properties
   pageTitle="部署 StorSimple 虛擬陣列中 VMware 佈建"
   description="此 StorSimple 虛擬陣列部署數列中的第二個教學課程涉及佈建中 VMware 的虛擬裝置。"
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
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>部署 StorSimple 虛擬陣列-佈建中 VMware 的虛擬陣列

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>概觀 
本教學課程中佈建適用於 StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署的虛擬裝置或 StorSimple 虛擬裝置） 執行 2016 年 3 月上市 (GA) 版本。 本教學課程說明如何佈建並連線到 StorSimple 的虛擬陣列執行 VMware ESXi 5.5 主機系統上和上方。 本文適用於 StorSimple 虛擬陣列中 Azure 傳統入口網站，以及 Microsoft Azure 政府雲端部署。

您將需要進行佈建系統管理員權限，並連線至虛擬裝置。 佈建和初始設定可以需要大約 10 分鐘的時間才能完成。


## <a name="provisioning-prerequisites"></a>佈建的先決條件

您會在這裡找到佈建虛擬裝置執行 VMware ESXi 5.5 主機系統上和上方的必要條件。

### <a name="for-the-storsimple-manager-service"></a>如需 StorSimple 管理員服務

在開始之前，請確定︰

-   您已完成[準備 StorSimple 虛擬陣列的入口網站](storsimple-ova-deploy1-portal-prep.md)中的所有步驟。

-   您已下載的 VMware 虛擬裝置圖像，從 Azure 入口網站。 如需詳細資訊，請參閱[步驟 3︰ 下載虛擬裝置圖像](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image)。

### <a name="for-the-storsimple-virtual-device"></a>StorSimple 虛擬裝置 

部署虛擬裝置之前，請確定︰

-   您有權存取執行 HYPER-V 主機系統 (2008 R2 或更新版本)，可用於佈建裝置。

-   Host （主機） 系統是能夠所佈建虛擬裝置下列資源︰

    -   4 核心的最小值。

    -   至少 8 GB 的 RAM。

    -   網路介面。

    -   系統資料 500 GB 虛擬磁碟。

### <a name="for-the-network-in-datacenter"></a>在資料中心的網路 

在開始之前，請確定︰

-   已檢閱部署 StorSimple 虛擬裝置的網路需求和設定資料中心網路依照需求。 如需詳細資訊，請參閱[StorSimple 虛擬陣列系統需求](storsimple-ova-system-requirements.md)。

## <a name="step-by-step-provisioning"></a>逐步佈建 

若要佈建並連線到虛擬的裝置，您將需要執行下列步驟︰

1.  請確定主機系統有足夠的資源，符合最小的虛擬裝置需求。

2.  佈建在您 hypervisor 虛擬裝置。

3.  啟動虛擬裝置，並取得 IP 位址。

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>步驟 1︰ 確定主機系統符合最小的虛擬裝置的需求

若要建立虛擬裝置，您將需要︰

-   存取執行 VMware ESXi 伺服器 5.5 主機系統和上方。

-   VMware vSphere 用戶端上您的系統管理 ESXi 主機。

    -   4 核心的最小值。

    -   至少 8 GB 的 RAM。

    -   網路介面連線到網際網路流量的網路。 最小的網際網路頻寬應該 5 Mbps 允許的最佳使用的裝置。

    -   資料 500 GB 虛擬磁碟。

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>步驟 2︰ 佈建中 hypervisor 的虛擬裝置

執行下列步驟，以提供您 hypervisor 中的虛擬裝置。

1.  在系統的虛擬裝置圖像複製。 這是您已透過 Azure 傳統入口網站下載的圖像。 
    1.  請確定這是最新的圖像檔案下載。 如果您先前下載圖像，請再次以確定您有最新的圖像。 最新的圖像具有兩個檔案 （而非一個）。
    2.  記下當您將使用此程序的更新版本中，複製圖像的位置。

2.  登入 ESXi 伺服器使用 vSphere 用戶端。 您必須具有管理員權限來建立虛擬機器。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  在 vSphere 用戶端的左窗格中，[清單] 區段中，選取 [ESXi 伺服器]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  第一次會將 VMDK 上傳至 ESXi 伺服器。 瀏覽至右窗格中的 [**設定**] 索引標籤。 在 [**硬體**] 底下，選取 [**儲存**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  在右方窗格的 [下**Datastores**，選取您要上傳 VMDK 資料存放的區。 資料存放區必須具備足夠的空間的作業系統與資料磁碟。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  以滑鼠右鍵按一下，然後選取 [**瀏覽資料存放區**。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  會顯示**資料存放區瀏覽器**視窗。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  在 [工具] 列中，按一下 [![](./media/storsimple-ova-deploy2-provision-vmware/image7.png)圖示，即可建立新的資料夾。 指定的資料夾名稱，並記下它。 稍後建立虛擬機器 （建議使用最佳作法） 時，您會使用此資料夾的名稱。 按一下**[確定]**。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  新的資料夾會出現在**資料存放區瀏覽器**的左窗格中。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  按一下 [上傳] 圖示![](./media/storsimple-ova-deploy2-provision-vmware/image10.png)，然後選取 [**上傳的檔案**。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  您現在應該瀏覽]，然後指向 [下載 VMDK 檔案。 會有兩個檔案。 選取要上傳的檔案。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  按一下 [**開啟**]。 現在，則會開啟指定的資料存放區的 VMDK 檔案上傳。 可能需要幾分鐘的時間要上傳的檔案。


1.  上傳完畢之後，您會看到您所建立的資料夾中的資料存放區中的檔案。 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    您現在必須在第二個的 VMDK 檔案上傳至相同的資料存放區。

1.  返回 vSphere 用戶端視窗。 與選取的 ESXi 伺服器，以滑鼠右鍵按一下，然後選取**新的虛擬機器**。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  **建立新的虛擬機器**會出現一個視窗。 在 [**設定**] 頁面中，選取 [**自訂**] 選項。 按一下 [**下一步**]。
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  在 [**名稱與位置**] 頁面上指定虛擬機器的名稱。 此名稱應符合您之前在步驟 8 中指定的資料夾名稱 （建議的最佳方式）。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  在 [**儲存**] 頁面中，選取您想要使用佈建您 VM 資料存放的區。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  在 [**虛擬機器版本**] 頁面上選取 [**虛擬機器版本︰ 8**。 請注意，版本 8 到 11 均受支援。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  在**來賓作業系統**頁面上，選取**來賓作業系統**為**Windows**。 **版本**，請從下拉式清單中，選取 [ **Microsoft Windows Server 2012 （64 位元）**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  [ **Cpu** ] 頁面上調整**的虛擬通訊端的數字**及**每虛擬通訊端核心數目**使**總數核心**4 （或更多）。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  在**記憶體**頁面上，指定 8 GB （或更多） 的 RAM。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  在 [**網路**] 頁面中，指定網路介面的數目。 最低需求是一個網路介面。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  在 [ **SCSI 控制器**] 頁面接受預設**LSI 邏輯 SA 控制器**。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  在 [**選取磁碟**] 頁面上，選擇 [**使用現有的虛擬磁碟**]。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  在 [**選取現有的磁碟**] 頁面在**磁碟檔案路徑**] 底下，按一下 [**瀏覽**。 這會開啟 [**瀏覽 Datastores** ] 對話方塊。 瀏覽至您上傳 VMDK 的位置。 現在已合併兩個一開始上傳的檔案時，您會看到只有一個資料存放區中的檔案。 選取檔案，然後按一下**[確定**]。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  在 [**進階選項**] 頁面接受預設值，然後按一下 [**下一步**。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  在**準備好要完成**的頁面，檢閱所有新的虛擬機器相關聯的設定。 核取 [**編輯完成前的虛擬機器設定**]。 按一下 [**繼續**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  在**虛擬機器屬性**] 頁面中，在 [**硬體**] 索引標籤中，找出裝置硬體。 選取**新的硬碟**。 按一下 [**新增**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  如此會開啟 [**新增硬體**] 視窗。 在**裝置類型**] 頁面的 [**選擇您想要新增的裝置類型**] 下選取**硬碟**，然後按一下 [**下一步**。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  在 [**選取磁碟**] 頁面上，選擇 [**建立新的虛擬磁碟**]。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  在 [**建立磁碟**] 頁面中，變更**磁碟大小**500 GB （或更多）。 在**磁碟佈建**，選取**細佈建**。 按一下 [**下一步**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  在 [**進階選項**] 頁面接受預設值。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  在**準備好要完成**的頁面，檢閱 [磁碟] 選項。 按一下 [**完成**]。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  您現在會回到 [虛擬機器屬性] 頁面。 新的硬碟會新增至您的虛擬機器中。 按一下 [**完成**]。
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  在右窗格中選取您的虛擬機器，以瀏覽至 [**摘要**] 索引標籤。 檢閱您的虛擬機器的設定。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

您的虛擬機器現在佈建後。 下一步是在這台電腦上的 power，以取得 IP 位址。

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>步驟 3︰ 開始虛擬裝置，並取得 IP

執行下列步驟，以啟動虛擬裝置，以便連線。

#### <a name="to-start-the-virtual-device"></a>若要開始虛擬裝置

1.  啟動虛擬裝置。 在 vSphere 組態管理員] 的左窗格中，選取您的裝置，以顯示快顯功能表上按一下滑鼠右鍵。 選取**電源**，然後選取 [ **Power 上**。 此應該電源虛擬機器。 您可以檢視狀態 vSphere 用戶端的底部**最近的工作**] 窗格中。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  設定工作會需要幾分鐘，才能完成。 一旦裝置正在執行，瀏覽至 [**主控台**] 索引標籤。 傳送登入裝置 Ctrl + Alt + Delete。 或者，您可以在 [主控台] 視窗將游標指向，按下 Ctrl + Alt + Insert。 預設的使用者*StorSimpleAdmin* ，而預設密碼是*密碼 1*。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  基於安全性理由，裝置系統管理員的密碼到期日在第一個記錄檔。 系統會提示您變更密碼。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  輸入包含至少 8 個字元的密碼。 密碼必須包含 3 的這些需求 4︰ 大寫、 小寫字母、 數字及特殊字元。 重新輸入密碼以進行確認。 將會通知您的密碼已變更。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  順利變更密碼之後，可能要重新啟動虛擬裝置。 等待重新開機，才能完成。 Windows PowerShell 主控台的裝置可能會顯示進度列以及。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  6-8 步驟只適用於非 DHCP 環境中啟動時。 如果您是在 DHCP 環境中，接著略過這些步驟，然後移至步驟 9。 如果您啟動非 DHCP 環境中的裝置，您會看到下列畫面。 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    您現在必須設定網路。

1.  使用`Get-HcsIpAddress`清單網路介面啟用虛擬裝置上的命令。 如果您的裝置有啟用單一網路介面，指派給這個介面的預設名稱是`Ethernet`。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  使用`Set-HcsIpAddress`cmdlet 來設定網路。 範例如下所示︰


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  初始安裝完成，而且裝置已開機之後，您會看到裝置橫幅文字。 請記下 [IP 位址和橫幅文字中顯示的 URL，來管理裝置。 連線至網頁虛擬裝置的 UI，並完成本機的設定，並登錄，您會使用這個 IP 位址。

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. （選用）如果您部署您政府雲端中的裝置，請執行此步驟。 您現在會在您的裝置上啟用美國聯邦資訊處理標準 (FIPS) 模式。 FIPS 140 標準定義加密演算法來保護機密資料的美國聯邦政府電腦系統核准使用。
    1. 若要啟用 FIPS 模式，請執行下列 cmdlet:
        
        `Enter-HcsFIPSMode`

    2. 重新啟動裝置之後您已啟用 FIPS 模式，讓密碼編譯的驗證才會生效。

        > [AZURE.NOTE] 您可以啟用或停用您的裝置上的 FIPS 模式。 不支援交替 FIPS 與非 FIPS 模式之間的裝置。


如果您的裝置不符合最低組態需求，您會看到有錯誤 （如下所示） 的橫幅文字。 您必須修改裝置設定，讓它有足夠的資源，以符合最低需求。 然後，您可以重新啟動，並連接至裝置。 參照中的最小設定需求[步驟 1︰ 確認主機系統符合最小的虛擬裝置的需求](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)。

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

如果您在初次設定使用本機網頁 UI 面對任何其他錯誤，請參閱下列的工作流程中[使用本機網頁 UI 管理 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。

-   若要[疑難排解 web UI 安裝](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)執行診斷測試。

-   [產生記錄封裝並檢視記錄檔](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

## <a name="next-steps"></a>後續步驟

-   [為檔案伺服器設定 StorSimple 虛擬陣列](storsimple-ova-deploy3-fs-setup.md)

-   [設定您的 StorSimple 虛擬陣列 iSCSI 伺服器](storsimple-ova-deploy3-iscsi-setup.md)

