<properties 
   pageTitle="設定您的 StorSimple 虛擬陣列主機 MPIO |Microsoft Azure"
   description="說明如何針對您 StorSimple 虛擬陣列連線至執行 Windows Server 2012 R2 的主機設定多重路徑 I/O (MPIO)。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>設定多重路徑 I/O StorSimple 虛擬陣列的 Windows Server host （主機）

## <a name="overview"></a>概觀

本文將說明如何安裝在您的 Windows Server 主機上的多重路徑 I/O 功能 (MPIO)、 套用特定組態設定 StorSimple 專用區，然後確認 MPIO StorSimple 區。 程序假設您 StorSimple 1200 虛擬陣列，具有兩個網路介面已連線到 Windows Server 主機具有兩個網路介面。 本文中所包含的資訊僅適用於虛擬陣列。 StorSimple 8000 數列裝置上的資訊，請移至 [[設定 MPIO StorSimple 主機](storsimple-configure-mpio-windows-server.md)。 

在 Windows Server 可協助建立可用性、 容錯儲存設定 MPIO 功能。 MPIO 使用多餘的實體路徑元件，卡、 纜線及選項 — 建立邏輯伺服器與存放裝置之間的路徑。 如果有元件失敗，導致失敗，邏輯路徑多重路徑邏輯使用替代路徑 i/o 讓應用程式仍然可以存取他們的資料。 此外根據您的設定，MPIO 可以也以提升效能重新平衡載入，在所有的路徑。 如需詳細資訊，請參閱[MPIO 概觀](https://technet.microsoft.com/library/cc725907.aspx "MPIO 概觀與功能")。  

高可用性的 StorSimple 方案，請在 [連線到您 StorSimple 1200 虛擬陣列 （也稱為內部部署虛擬裝置） 的 Windows Server 主機上設定 MPIO。 連結、 網路或介面失敗，然後可容許 host （主機） 伺服器。 

您必須遵循下列步驟設定 MPIO: 

- 設定的先決條件

- 步驟 1︰ 安裝 Windows Server 主機上 MPIO

- 步驟 2︰ 設定 MPIO StorSimple 區

- 主機上的步驟 3︰ 裝載 StorSimple 區

下列各節討論上述步驟。


## <a name="prerequisites"></a>必要條件

本節詳細 Windows Server host （主機） 和您的虛擬陣列設定的先決條件。

### <a name="on-windows-server-host"></a>在 Windows Server host （主機）

-  請確定您的 Windows Server 主機已啟用 2 網路介面。


### <a name="on-storsimple-virtual-array"></a>在 StorSimple 虛擬陣列

- 虛擬陣列應為 iSCSI 伺服器設定。 若要深入瞭解，請參閱[設定為 iSCSI 伺服器的虛擬陣列](storsimple-ova-deploy3-iscsi-setup.md)。 陣列應該啟用一或多個網路介面。   

- 在您的虛擬陣列的網路介面應該可從 Windows Server host （主機）。

- 您 StorSimple 虛擬陣列應該建立一或多個區。 若要深入瞭解，請參閱[新增大量](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)您 StorSimple 1200 虛擬陣列上。 此程序，我們建立虛擬陣列 3 區 （2 至本機固定和 1 分層大量為顯示下方）。
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>StorSimple 虛擬陣列的硬體設定

下圖顯示的硬體設定高可用性和負載平衡多重路徑，您的 Windows Server host （主機） 和 StorSimple 虛擬陣列使用此程序。  

![mpio 硬體設定](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

前圖所示︰

- 佈建後，在 HYPER-V StorSimple 虛擬陣列是設定為 iSCSI 伺服器的單一節點作用中的裝置。

- 在您的陣列時啟用兩個虛擬網路介面。 在本機的 web 1200 虛擬陣列的 UI，請確定兩個網路介面啟用瀏覽至 [**網路設定**，如下所示︰

    ![網路介面 1200年上啟用](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    請注意 IPv4 位址，啟用的網路介面 （乙太網路，預設的乙太網路 2），然後儲存以供日後使用主機上。

- 在您的 Windows Server 主機時啟用兩個網路介面。 如果是相同的子網路連線的介面主機和陣列，然後會有 4 路徑可用。 這是在本程序的大小寫。 不過，如果在陣列和主機介面上的每個網路介面是在不同的 IP 子網路 （與無法舉例來說），然後只 2 路徑可。

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>步驟 1︰ 安裝 Windows Server 主機上 MPIO

MPIO 是在 Windows Server 上的選擇性功能，預設不會安裝。 您應該安裝做為功能透過伺服器管理員。 若要在您的 Windows Server host （主機） 上安裝此功能，請完成下列程序。

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>步驟 2︰ 設定 MPIO StorSimple 區

MPIO 必須設定來識別 StorSimple 區。 若要設定 MPIO 辨識 StorSimple 區，請執行下列步驟。

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>主機上的步驟 3︰ 裝載 StorSimple 區

Windows 伺服器上設定 MPIO 之後，區建立 StorSimple 陣列可以裝載，然後可以再利用 MPIO 重複性的。 若要裝載音量，請執行下列步驟。

#### <a name="to-mount-volumes-on-the-host"></a>主機上裝載區

1. 開啟 Windows Server 主機上的 [ **iSCSI 啟動器屬性**] 視窗。 按一下 [**伺服器管理員] > 儀表板 > 工具 > iSCSI 啟動器**。
2. 在 [ **iSCSI 啟動器屬性**] 對話方塊中，按一下 [探索] 索引標籤，然後按一下**探索目標入口網站**。
3. 在 [**探索目標入口網站**] 對話方塊中，執行下列動作︰
    
    - 輸入您 StorSimple 虛擬陣列的第一個網路介面的 IP 位址。 根據預設，這是**乙太網路**。 
    - 按一下 [若要返回 [ **iSCSI 啟動器屬性**] 對話方塊的**[確定]** 。

    >[AZURE.IMPORTANT] **如果您使用 iSCSI 連線私人的網路，請輸入已連線至私人網路的資料連接埠的 IP 位址。**

4. 在您的陣列，重複步驟 2-3 的第二個網路介面 (例如，乙太網路 2)。 

5. 在 [ **iSCSI 啟動器屬性**] 對話方塊中選取**目標**] 索引標籤。 在虛擬的陣列，您應該會看到每個大量曲面圖為**探索到的目標**下的目標。 在此情況下，您會發現 （對應到三個區） 的三個目標。

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. 按一下 [建立 iSCSI 與工作階段 StorSimple 陣列的**連線**。 **連線到目標**] 對話方塊會顯示。 選取 [**啟用多重路徑**] 核取方塊。 按一下 [**進階**]。

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. 在 [**進階設定**] 對話方塊中，執行下列動作︰                                       
    -    在 [**本機介面卡**] 下拉式清單中，選取 [ **Microsoft iSCSI 啟動器**]。
    -    在 [**啟動器 IP**下拉式清單中，選取主應用程式的 IP 位址。
    -    在 [**目標入口網站**IP 下拉式清單中，選取 [陣列介面的 IP。
    -    按一下 [若要返回 [ **iSCSI 啟動器屬性**] 對話方塊的**[確定]** 。

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. 按一下 [**屬性**]。 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. 在 [**屬性**] 對話方塊中，按一下 [**新增工作階段**。

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. 在 [**連線至目標**] 對話方塊中，選取 [**啟用多重路徑**] 核取方塊。 按一下 [**進階**]。
11. 在 [**進階設定**] 對話方塊中︰                                        
    -  在 [**本機介面卡**] 下拉式清單中，選取 [Microsoft iSCSI 啟動器]。
    -  在 [**啟動器 IP**下拉式清單中，選取對應至主應用程式的 IP 位址。 在此情況下，您連線網路介面兩個陣列上到單一網路介面 」 主機上。 因此，此介面是針對第一個工作階段提供相同。
    -  在**目標入口網站的 IP**下拉式清單中，選取 [啟用陣列的第二個資料介面的 IP 位址。
    -  按一下**[確定]**返回 iSCSI 啟動器屬性] 對話方塊。 您的第二個工作階段新增至目標。

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - 新增後所要的工作階段 （路徑），在**iSCSI 啟動器屬性**] 對話方塊中，選取目標並按一下 [**內容**。 在 [**屬性**] 對話方塊的 [工作階段] 索引標籤中，注意的四個階段會對應到路徑排列方式的識別碼。 若要取消工作階段，選取工作階段識別碼] 旁的核取方塊，然後再按一下 [**中斷連線**。
 
    - 若要檢視工作階段中呈現的裝置，請選取 [**裝置**] 索引標籤。 若要設定所選的裝置的 MPIO 原則，按一下 [ **MPIO**]。 * *
    -  詳細資料**對話方塊便會出現。在**MPIO** ] 索引標籤，您可以選取適當的**負載平衡原則**設定。您也可以檢視**作用中**或**待命 * * 路徑類型。

10. 重複步驟 8-11 若要新增額外的工作階段 （路徑） 至目標。 主機上的兩個介面及兩個虛擬陣列上，您可以新增的每個目標的四個階段總計。 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. 您需要的每個大量 （為目標表面） 重複這些步驟。

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. 開啟 [瀏覽至**電腦管理****伺服器管理員] > 儀表板 > 電腦管理**。 在左窗格中，按一下 [**儲存 > 磁碟管理**。 區建立 StorSimple 虛擬陣列上會顯示這個主機會顯示**磁碟管理**] 下，為新的磁碟。

13. 初始化磁碟，然後建立新的區。 格式過程中，選取 [64 KB 的配置單位大小 （澳洲）]。 重複此程序的所有可用的區。

    ![磁碟管理](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. 在 [**磁碟管理**] 底下，以滑鼠右鍵按一下**磁碟**，然後選取 [**內容**。

15. 在 [**多重路徑磁碟裝置內容**] 對話方塊中，按一下 [ **MPIO** ] 索引標籤。

    ![磁碟內容](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. **DSM 名稱**] 區段中按一下 [**詳細資料**，並確認參數會設定為預設參數。 預設的參數為︰

    - 路徑驗證期間 = 30
    - 重試計數 = 3
    - PDO 移除期間 = 20
    - 重試間隔 = 1
    - 路徑驗證啟用 = 取消核取。

    >[AZURE.NOTE] **請勿修改預設參數。**


## <a name="next-steps"></a>後續步驟

瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 虛擬陣列](storsimple-ova-manager-service-administration.md)。
 
