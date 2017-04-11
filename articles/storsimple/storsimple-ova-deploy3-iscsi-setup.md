<properties 
   pageTitle="StorSimple 虛擬陣列 iSCSI 伺服器設定 |Microsoft Azure"
   description="說明如何執行初始設定，以註冊 StorSimple iSCSI 伺服器，並完成裝置設定。"
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
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>部署 StorSimple 虛擬陣列 – 設定為 iSCSI 伺服器虛擬裝置

![iscsi 設定程序的流程](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>概觀

本教學課程中部署適用於 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署的虛擬裝置或 StorSimple 虛擬裝置） 執行 2016 年 3 月上市 (GA) 版本。 本教學課程說明如何執行初始設定、 註冊 StorSimple iSCSI 伺服器、 完成裝置設定]，然後建立、 裝載、 初始化，並格式化 StorSimple 虛擬裝置 iSCSI 伺服器上的區。 本文中的 StorSimple 設定資訊只適用於 StorSimple 虛擬陣列。 

所述的程序以下大約需要 30 分鐘為 1 小時，才能完成。 資訊的本文只適用於 StorSimple 虛擬陣列。

## <a name="setup-prerequisites"></a>設定的先決條件

設定並設定 StorSimple 虛擬裝置之前，請確定︰

- 您有佈建虛擬裝置，而且連線至該[部署 StorSimple 虛擬陣列-佈建 HYPER-V 中的虛擬陣列](storsimple-ova-deploy2-provision-hyperv.md)或[部署 StorSimple 虛擬陣列-佈建中 VMware 的虛擬陣列](storsimple-ova-deploy2-provision-vmware.md)中所述。

- 您有服務登錄機碼，從您要建立管理 StorSimple 虛擬裝置 StorSimple 管理員服務。 如需詳細資訊，請參閱**步驟 2︰ 取得服務登錄機碼**[部署 StorSimple 虛擬陣列](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)中-準備入口網站。

- 如果這是第二或後續虛擬裝置註冊您現有的 StorSimple 管理員服務，您應該服務資料加密金鑰。 此按鍵產生時的第一個裝置已成功註冊此服務。 如果您遺失此鍵，請參閱[使用 Web UI 來管理您的 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)中的 [**取得服務資料加密金鑰**。

## <a name="step-by-step-setup"></a>逐步設定 

使用下列的逐步指示安裝和設定 StorSimple 虛擬裝置︰

-  [步驟 1︰ 完成本機 web UI 設定和註冊您的裝置](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [步驟 2︰ 完成所需的裝置設定](#step-2-complete-the-required-device-setup)
-  [步驟 3︰ 新增大量](#step-3-add-a-volume)
-  [步驟 4︰ 裝載、 初始化以及設定格式的音量](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>步驟 1︰ 完成本機 web UI 設定和註冊您的裝置 

#### <a name="to-complete-the-setup-and-register-the-device"></a>若要完成的設定和註冊裝置

1. 開啟瀏覽器視窗，然後輸入連線至網頁 UI:

    `https://<ip-address of network interface>`

    使用上一個步驟所述的連線 URL。 您會看到通知您網站的安全性憑證問題發生錯誤。 按一下 [**繼續] 以這個網頁**]。

    ![安全性憑證時發生錯誤](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. **StorSimpleAdmin**登入 web 虛擬裝置的使用者介面。 在步驟 3 中輸入您要變更的裝置系統管理員密碼︰[部署 StorSimple 虛擬陣列-佈建 HYPER-V 中虛擬裝置](storsimple-ova-deploy2-provision-hyperv.md)或[部署 StorSimple 虛擬陣列](storsimple-ova-deploy2-provision-vmware.md)中-佈建虛擬裝置中 VMware 開始虛擬裝置。

    ![登入頁面](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. 您會移至 [**首頁**] 頁面。 本頁面說明來設定和註冊虛擬裝置 StorSimple 管理員服務所需的不同設定。 請注意，**網路設定**、 **Web proxy 設定**與**時間設定**選用。 只需要的設定的**裝置設定**和**雲端設定**。

    ![首頁](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. **網路介面**] 下的 [**網路設定**] 頁面上資料 0 會自動設定您。 每個網路介面預設設定為自動取得 IP 位址 (DHCP)。 因此，IP 位址、 子網路及閘道器會自動指派 （適用於 IPv4 和 IPv6）。

    當您規劃部署您的裝置 （若要佈建區塊存放） iSCSI 伺服器，我們建議您停用**自動取得 IP 位址**] 選項，並設定靜態 IP 位址。

    ![網路設定] 頁面](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    如果您的裝置提供時新增多個網路介面，您可以在這裡設定。 請注意，您可以設定您的網路介面為 IPv4 只或為 IPv4 和 IPv6。 不支援 IPv6 只設定。

5. 因為您的裝置嘗試進行通訊的雲端儲存服務提供者，或依名稱解決您的裝置，如果其設定為在檔案伺服器時，會用來，就需要 DNS 伺服器。 在 [**網路設定**] 頁面底下的 [ **DNS 伺服器**︰

    1. 主要和次要 DNS 伺服器會自動設定。 如果您選擇設定靜態 IP 位址，您可以指定 DNS 伺服器。 高可用性，我們建議您設定的主要與次要 DNS 伺服器。

    2. 按一下 [**套用**]。 這將會套用，然後驗證網路設定。

6. 在 [**裝置設定]**頁面︰

    1. 指定唯一**名稱**至您的裝置。 此名稱 1-15 個字元，而且可以包含字母、 數字和連字號。

    2. 按一下**iSCSI 伺服器**圖示![iSCSI 伺服器圖示](./media/storsimple-ova-deploy3-iscsi-setup/image7.png)您所建立的裝置**類型**。 ISCSI server 可讓您佈建區塊儲存空間。

    3. 指定是否要讓此裝置網域。 如果您的裝置是 iSCSI 伺服器，然後加入網域為選用。 如果您決定不將 iSCSI 伺服器加入網域，請按一下 [**套用**]，設定會套用，然後跳至下一個步驟，請等候。

        如果您想要加入網域的裝置。 輸入**網域名稱**，然後再按一下 [**套用**]。

        > [AZURE.NOTE] 如果網域加入 iSCSI 伺服器，請確定虛擬陣列是在自己的組織單位 (OU) 中的 Microsoft Azure Active Directory 群組原則物件 (GPO) 就會套用至該。

    5. 對話方塊會顯示。 輸入您的網域的認證以指定的格式。 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). 將確認網域認證。 如果是不正確的認證，您會看到一則錯誤訊息。

        ![認證](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. 按一下 [**套用**]。 這將會套用，然後驗證裝置設定。
 
7. （選擇性） 設定您的 web proxy 伺服器。 雖然 web proxy 設定是選擇性的請注意，如果您使用 web proxy，您可以只設定以下。

    ![設定網頁 proxy](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    在 [**網頁 proxy**頁面︰

    1. 提供此格式**proxy 網址**︰ *http://host-IP 地址*或*FDQN:Port 號碼*。 請注意 HTTPS Url 不受支援。

    2. 指定**驗證**與 [**基本**] 或 [**無]**。

    3. 如果您使用的 「 驗證 」，您也必須提供**使用者名稱**和**密碼**。

    4. 按一下 [**套用**]。 這會驗證並套用設定的網頁 proxy 設定。
 
8. （選擇性） 設定您的裝置，例如時區和主要和次要 NTP 伺服器的時間設定。 因為您的裝置必須同步處理的時間，讓它可以驗證您的雲端服務提供者，就需要 NTP 伺服器。

    ![時間設定](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    在 [**時間設定**] 頁面中︰

    1. 從下拉式清單中，選取 [根據部署裝置的地理位置**和時區**]。 預設時區，您的裝置是 PST。 您的裝置會使用這個時區所有排程作業。

    2. 指定您的裝置的**主索引 NTP 伺服器**或接受 time.windows.com 的預設值。 請確定您的網路，讓 NTP 流量，若要從您的資料中心傳遞至網際網路。

    3. 您也可以指定在您的裝置的**次要 NTP 伺服器**。

    4. 按一下 [**套用**]。 這會驗證並套用設定的時間設定。

9. 設定您的裝置的雲端設定。 在此步驟中，您就會完成本機裝置設定，然後再登錄 StorSimple 管理員服務的 [裝置。

    1. 輸入您從中取得**服務登錄機碼****步驟 2︰ 取得服務登錄機碼**[部署 StorSimple 虛擬陣列](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)中-準備入口網站。

    2. 如果這不是您要註冊此服務的第一個裝置，您必須提供**服務的資料加密金鑰**。 使用服務登錄機碼，其他裝置註冊 StorSimple 管理員服務需要此按鍵。 如需詳細資訊，請參閱[取得服務資料加密金鑰](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)您的本機 web UI 上。

    3. 按一下 [**註冊**]。 這會重新啟動裝置。 您可能需要 2-3 分鐘之後的裝置已成功註冊，請等候。 重新啟動裝置之後，您會被重新導向至登入頁面。

       ![註冊裝置](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. 返回 Azure 傳統入口網站。 在 [**裝置**] 頁面上確認裝置已成功連線到服務查看狀態。 裝置狀態] 應為**作用中**。

    ![裝置頁面](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>步驟 2︰ 完成所需的裝置設定

若要完成 StorSimple 裝置的裝置設定，您需要︰

- 選取儲存的帳戶與您的裝置。

- 選擇 [傳送至雲端的資料的加密設定]。

若要完成所需的裝置安裝 Azure 傳統入口網站中，執行下列步驟。

#### <a name="to-complete-the-minimum-device-setup"></a>若要完成的最小值的裝置設定

1. 在 [**裝置**] 頁面中，選取您剛剛建立的裝置。 此裝置會顯示為 [**作用中**。 按一下箭號下一步裝置名稱，然後按一下 [**快速入門**。

    ![裝置頁面](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. 按一下 [開始設定裝置精靈**完成裝置設定**]。

    ![設定裝置精靈](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. 在設定裝置精靈中，在 [**基本的設定**] 頁面上執行下列動作︰

   1. 指定儲存帳戶以搭配您的裝置。 在此訂閱]，您可以從下拉式清單中，選取現有的儲存空間帳戶或您可以指定**新增多個**帳戶選擇不同的訂閱。

   2. 定義的加密設定將會傳送至雲端的其餘部分的所有資料。 （StorSimple 使用 AES 256 加密）。若要加密資料，請選取 [**啟用雲端儲存加密**核取方塊。 輸入包含 32 個字元的雲端儲存加密。 重新輸入的金鑰以進行確認。

   3. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![基本設定](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    設定就會更新。 設定成功更新後，將無法使用完整的裝置設定] 按鈕。 您會回到裝置的 [**快速入門**] 頁面。                                                        

>[AZURE.NOTE]您可以隨時修改其他裝置設定，以存取 [**設定**] 頁面。

## <a name="step-3-add-a-volume"></a>步驟 3︰ 新增大量

若要建立大量 Azure 傳統入口網站中，執行下列步驟。

#### <a name="to-create-a-volume"></a>若要建立區

1. 在 [裝置**快速入門**] 頁面上按一下 [**新增音量**。 此舉會啟動 [新增大量精靈。

2. 在 [新增大量精靈，在 [**基本設定**] 底下，執行下列動作︰

    1. 提供您大量的唯一名稱。 名稱必須包含要 127 3 個字元的字串。

    2. 提供大量的描述。 描述可協助您識別大量擁有者。

    3. 選取區的使用狀況類型。 使用類型可**Tiered 音量**或**本機釘選大量。** （**Tiered 大量**是預設值）。需要本機的保證與低延遲，較高的效能的工作量，選取 [**本機釘選****音量**]。 所有其他的資料中，選取 [ **Tiered** **音量**]。

        本機固定的大量 thickly 佈建後，並確保區中的主資料保持在裝置上並不會不要至雲端。 如果您建立本機固定的音量，裝置會檢查佈建大量要求的大小本機層上的可用空間。 建立本機固定的大量溢出從裝置的現有資料至雲端，可能會要求，並建立音量所花的時間長度。 能夠的音量、 可用的網路頻寬和您的裝置上的資料大小而定的總時間。

        分層的大量另一方面狗熊佈建後，您可以快速建立。 當您建立分層的音量時，大約 10%的間距在本機層佈建後，90%的間距在雲端佈建後。 比方說，如果您佈建 1 TB 音量，100 GB 也可以位於本機的空間和 900 GB 就會使用雲端中當資料層。 這在表示是，如果您執行所有本機的空間在裝置上，您無法佈建分層的共用 （因為無法使用 10%）。

    4. 指定您音量能夠的容量。 請注意，指定的容量應該小於可用的容量。 如果您正在建立分層的音量，應該 500 GB 介於 5 TB 大小。 本機固定的音量，請指定 50 GB 介於 500 GB 的音量大小。 使用可用的容量佈建大量的指南。 如果使用本機容量是 0 GB，您不允許佈建本機固定或分層的音量。

        ![基本設定](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. 按一下箭號圖示 ![箭號圖示](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) 若要移至下一個頁面。

3. 在 [**其他設定**] 頁面上新增存取控制記錄 (ACR):

    1. 針對您 ACR 中提供的**名稱**。

    2. 在**iSCSI 啟動器名稱**，提供 iSCSI 完整名稱 (IQN) 的 Windows 主機。 如果您沒有安裝 IQN，請移至[附錄 a︰ 取得 IQN 的 Windows Server host （主機）](#appendix-a-get-the-iqn-of-a-windows-server-host)。

    3. 我們建議您選取 [**啟用此大量的預設備份**] 核取方塊以啟用預設備份。 預設備份將會建立在 22:30 天 （裝置時間） 執行，並建立雲端快照的此大量的原則。

        ![其他設定](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). 此舉會啟動大量建立工作。 您會看到類似以下的進度訊息。

        ![進度訊息](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        使用指定的設定，將會建立區。 根據預設，監控和備份啟用音量。

    5. 若要確認已成功建立音量，請移至 [**區**] 頁面。 您應該會看到列出音量。

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>步驟 4︰ 裝載、 初始化以及設定格式的音量

執行下列步驟來裝載、 初始化以及格式化 StorSimple 區上的 Windows Server 主機。

#### <a name="to-mount-initialize-and-format-a-volume"></a>裝載、 初始化，及格式化大量

1. 啟動 Microsoft iSCSI 啟動器。

2. 在**iSCSI 啟動器屬性**視窗中，在 [**探索**] 索引標籤上按一下 [**探索入口網站**]。

    ![探索入口網站](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. 在 [**探索目標入口網站**] 對話方塊中，提供您 iSCSI 啟用網路介面的 IP 位址，然後按一下**[確定]**。

    ![IP 位址](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. 在**iSCSI 啟動器屬性**視窗中，在 [**目標**] 索引標籤上找出**Discovered 目標**。 （每個大量會發現的目標。）裝置狀態應該會顯示為**非使用**。

    ![探索的目標](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. 選取目標裝置，然後按一下 [**連線**。 裝置連接之後，應該變更**已連線**狀態。 (如需有關如何使用 Microsoft iSCSI 啟動器的詳細資訊，請參閱[安裝及設定 Microsoft iSCSI 啟動器] [1]。

    ![選取目標裝置](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. 在您的 Windows 主機，按下 Windows 標誌鍵 + X，然後再按一下 [**執行**。

7. 在 [**執行**] 對話方塊中，輸入**Diskmgmt.msc**。 按一下**[確定**]，然後**磁碟管理**] 對話方塊便會顯示。 右窗格會顯示在您的主機上的區。

8. 在**磁碟管理**] 視窗中，如下圖所示，會出現裝載的區。 以滑鼠右鍵按一下發現的音量 （按一下磁碟名稱），然後按一下 [**線上**。

    ![磁碟管理](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. 以滑鼠右鍵按一下，然後選取 [**初始化磁碟**。

    ![初始磁碟 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. 在對話方塊中，選取要初始化的磁碟，然後按一下**[確定]**。

    ![初始磁碟 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. 新的簡單大量精靈] 啟動。 大小選取 [磁碟]，然後按一下 [**下一步**。

    ![新增大量精靈 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. 音量]，以指定磁碟機字母，然後按一下 [**下一步**。

    ![新增大量精靈 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. 輸入要格式化大量參數。 **在 Windows Server 上只 NTFS 功能受支援。** 設定 64k 澳洲。 提供您的音量標籤。 是建議的最佳作法，此為您提供 StorSimple 虛擬裝置上的區名稱相同的名稱。 按一下 [**下一步**]。

    ![新增大量精靈 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. 檢查您的音量的值，然後按一下 [**完成]**。

    ![新增大量精靈 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    在 [**磁碟管理**] 頁面上，區會顯示為 [**線上**。

    ![線上區](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>後續步驟

瞭解如何使用本機網路使用者介面，來[管理您的 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>附錄 a︰ 取得 IQN 的 Windows Server host （主機）

執行下列步驟，以取得 iSCSI 完整名稱 (IQN) 的 Windows 主機正在執行 Windows Server 2012。

#### <a name="to-get-the-iqn-of-a-windows-host"></a>若要取得 Windows 主機的 IQN

1. 在您的 Windows 主機上啟動 Microsoft iSCSI 啟動器。

2. 在**iSCSI 啟動器屬性**視窗中，在 [**設定**] 索引標籤上選取，然後複製 [**起始者名稱**] 欄位中的字串。

    ![iSCSI 啟動器屬性](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. 儲存此字串。

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



