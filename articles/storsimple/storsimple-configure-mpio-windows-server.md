<properties 
   pageTitle="設定 MPIO StorSimple 裝置 |Microsoft Azure"
   description="說明如何針對 StorSimple 裝置連接到執行 Windows Server 2012 R2 的主機設定多重路徑 I/O (MPIO)。"
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>設定多重路徑 I/O StorSimple 裝置

內建協助建立可用性、 容錯舊設定 Windows Server 的多重路徑 I/O (MPIO) 功能支援的 Microsoft。 MPIO 使用多餘的實體路徑元件，卡、 纜線及選項 — 建立邏輯伺服器與存放裝置之間的路徑。 如果有元件失敗，導致失敗，邏輯路徑多重路徑邏輯使用替代路徑 i/o 讓應用程式仍然可以存取他們的資料。 此外根據您的設定，MPIO 可以也以提升效能重新平衡載入，在所有的路徑。 如需詳細資訊，請參閱[MPIO 概觀](https://technet.microsoft.com/library/cc725907.aspx "MPIO 概觀與功能")。  

高-可用性的 StorSimple 方案，MPIO 應該 StorSimple 裝置上設定。 在執行 Windows Server 2012 R2 host （主機） 伺服器上安裝 MPIO 時，伺服器可以再容許連結、 網路或介面失敗。 

MPIO 是在 Windows Server 上的選擇性功能，預設不會安裝。 您應該安裝做為功能透過伺服器管理員。 本主題說明的步驟，您應該安裝並使用 MPIO 功能在執行 Windows Server 2012 R2 的主機上遵循，連線到 StorSimple 實體裝置。

>[AZURE.NOTE] **此程序是適用於 StorSimple 8000 系列。MPIO 目前不支援 StorSimple 虛擬裝置上。**

您必須遵循下列步驟設定 MPIO StorSimple 裝置上︰

- 步驟 1︰ 安裝 Windows Server 主機上 MPIO

- 步驟 2︰ 設定 MPIO StorSimple 區

- 主機上的步驟 3︰ 裝載 StorSimple 區

- 步驟 4︰ 設定高可用性 MPIO 及負載平衡

下列各節討論上述步驟。

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>步驟 1︰ 安裝 Windows Server 主機上 MPIO

若要在您的 Windows Server host （主機） 上安裝此功能，請完成下列程序。

#### <a name="to-install-mpio-on-the-host"></a>若要安裝 MPIO 主機上

1. 開啟您的 Windows Server 主機上的伺服器管理員。 根據預設，伺服器管理員會啟動時的系統管理員群組登入電腦正在執行 Windows Server 2012 R2 或 Windows Server 2012 的成員。 如果 [伺服器管理員尚未開啟，按一下 [**開始 > 伺服器管理員**。
![伺服器管理員](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. 按一下 [**伺服器管理員] > 儀表板 > 加入角色和功能**。 此舉會啟動 [**新增角色和功能**] 精靈。
![新增角色及功能精靈 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. 在 [**新增角色和功能**] 精靈中，執行下列動作︰

    - 在 [**之前**] 頁面上，按一下 [**下一步**]。
    - 在 [**選取 [安裝類型**] 頁面接受預設設定的**角色型或功能為基礎**的安裝。 按一下 [**下一步**]。![新增角色及功能精靈 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - 在 [**選取目的伺服器**] 頁面上，選擇 [**從伺服器資料庫**]。 Host （主機） 伺服器應該會自動探索。 按一下 [**下一步**]。
    - 在 [**選取伺服器角色**] 頁面上，按一下 [**下一步**]。
    - **選取 [功能]**頁面上，選取**多重路徑 I/O**，然後按一下 [**下一步**。![加入角色和功能] 精靈 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - 在 [**確認安裝選項**] 頁面中，確認選取範圍，然後選取**自動如有必要，請重新啟動目的伺服器**，如下所示。 按一下 [**安裝**]。![新增角色及功能精靈 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - 安裝完成後，您會收到通知。 按一下 [**關閉**] 以關閉精靈。![新增角色及功能精靈 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>步驟 2︰ 設定 MPIO StorSimple 區

MPIO 必須設定來識別 StorSimple 區。 若要設定 MPIO 辨識 StorSimple 區，請執行下列步驟。

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>若要設定 MPIO StorSimple 區

1. 開啟**MPIO 設定**]。 按一下 [**伺服器管理員] > 儀表板 > 工具 > MPIO**。

2. 在 [ **MPIO 內容**] 對話方塊中，選取 [**探索多重路徑**] 索引標籤。

3. 選取 [**新增支援 iSCSI 裝置**，，然後再按一下 [**新增**]。  
![MPIO 內容探索多重路徑](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. 重新啟動時提示您的伺服器。
5. 在 [ **MPIO 內容**] 對話方塊中，按一下 [ **MPIO 裝置**] 索引標籤。 按一下 [**新增**]。
    </br>![MPIO 屬性 MPIO 裝置](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. **新增 MPIO 支援**在對話方塊中，在 [**裝置硬體識別碼**，請輸入您的裝置序列值。您可以取得裝置序列值，以存取您 StorSimple 管理員服務，並瀏覽至**裝置 > 儀表板**。 裝置序列值會顯示在右側的裝置儀表板的 [**快速瀏覽**窗格中。
    </br>![新增 MPIO 支援](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. 重新啟動時提示您的伺服器。

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>主機上的步驟 3︰ 裝載 StorSimple 區

Windows 伺服器上設定 MPIO 之後，StorSimple 裝置上建立的區可以裝載，並可以再利用 MPIO 重複性的。 若要裝載音量，請執行下列步驟。

#### <a name="to-mount-volumes-on-the-host"></a>主機上裝載區

1. 開啟 Windows Server 主機上的 [ **iSCSI 啟動器屬性**] 視窗。 按一下 [**伺服器管理員] > 儀表板 > 工具 > iSCSI 啟動器**。
2. 在 [ **iSCSI 啟動器屬性**] 對話方塊中，按一下 [探索] 索引標籤，然後按一下**探索目標入口網站**。
3. 在 [**探索目標入口網站**] 對話方塊中，執行下列動作︰
    
    - 輸入資料連接埠 StorSimple 裝置的 IP 位址 （例如，輸入資料 0）。
    - 按一下 [若要返回 [ **iSCSI 啟動器屬性**] 對話方塊的**[確定]** 。

    >[AZURE.IMPORTANT] **如果您使用 iSCSI 連線私人的網路，請輸入已連線至私人網路的資料連接埠的 IP 位址。**

4. 在您的裝置上重複步驟 2-3 的第二個網路介面 (例如，資料 1)。 請記住，這些介面應該啟用 iSCSI。 若要深入瞭解此問題，請參閱[修改網路介面](storsimple-modify-device-config.md#modify-network-interfaces)。
5. 在 [ **iSCSI 啟動器屬性**] 對話方塊中選取**目標**] 索引標籤。 您應該會看到 StorSimple 裝置目標 IQN 下**探索到的目標**。
 ![iSCSI 啟動器屬性目標] 索引標籤](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. 按一下 [**連線**至與 StorSimple 裝置建立 iSCSI 工作階段。 **連線到目標**] 對話方塊會顯示。

7. 在 [**連線至目標**] 對話方塊中，選取 [**啟用多重路徑**] 核取方塊。 按一下 [**進階**]。

8. 在 [**進階設定**] 對話方塊中，執行下列動作︰                                       
    -    在 [**本機介面卡**] 下拉式清單中，選取 [ **Microsoft iSCSI 啟動器**]。
    -    在 [**啟動器 IP**下拉式清單中，選取主應用程式的 IP 位址。
    -    在 [**目標入口網站**IP 下拉式清單中，選取 [裝置介面的 IP。
    -    按一下 [若要返回 [ **iSCSI 啟動器屬性**] 對話方塊的**[確定]** 。

9. 按一下 [**屬性**]。 在 [**屬性**] 對話方塊中，按一下 [**新增工作階段**。
10. 在 [**連線至目標**] 對話方塊中，選取 [**啟用多重路徑**] 核取方塊。 按一下 [**進階**]。
11. 在 [**進階設定**] 對話方塊中︰                                        
    -  在 [**本機介面卡**] 下拉式清單中，選取 [Microsoft iSCSI 啟動器]。
    -  在 [**啟動器 IP**下拉式清單中，選取對應至主應用程式的 IP 位址。 在此情況下，您連線在裝置上的兩個網路介面到單一網路介面 」 主機上。 因此，此介面是針對第一個工作階段提供相同。
    -  在**目標入口網站的 IP**下拉式清單中，選取 [啟用裝置上的第二個資料介面的 IP 位址。
    -  按一下**[確定]**返回 iSCSI 啟動器屬性] 對話方塊。 您的第二個工作階段新增至目標。

12. 開啟 [瀏覽至**電腦管理****伺服器管理員] > 儀表板 > 電腦管理**。 在左窗格中，按一下 [**儲存 > 磁碟管理**。 會顯示這個主機區 StorSimple 裝置上建立新的磁碟**磁碟管理**] 下方會出現。

13. 初始化磁碟，然後建立新的區。 格式過程中，選取 [封鎖大小為 64 KB。
![磁碟管理](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. 在 [**磁碟管理**] 底下，以滑鼠右鍵按一下**磁碟**，然後選取 [**內容**。
15. StorSimple 模型中 # # #**多重路徑磁碟裝置屬性**] 對話方塊方塊中，按一下 [ **MPIO** ] 索引標籤。
![StorSimple 8100 多重路徑磁碟 DeviceProp。](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. **DSM 名稱**] 區段中按一下 [**詳細資料**，並確認參數會設定為預設參數。 預設的參數為︰

    - 路徑確認期間 = 30
    - 重試計數 = 3
    - PDO 移除期間 = 20
    - 重試間隔 = 1
    - 路徑驗證啟用 = 取消核取。


>[AZURE.NOTE] **請勿修改預設參數。**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>步驟 4︰ 設定高可用性 MPIO 及負載平衡

以多重路徑為主的可用性和負載平衡，多個工作階段必須手動新增至宣告使用不同的路徑。 例如，如果主機有兩個介面連線到舊的裝置有兩個介面連線到舊，然後您需要使用正確的路徑排列方式數目 （只有兩個階段將會需要如果每個資料介面和主介面是在不同的 IP 子網路，而不是使用可路由傳送） 的四個階段。

>[AZURE.IMPORTANT] **我們建議您請勿混合 1 GbE 和 10 GbE 網路介面。如果您使用兩個網路介面，這兩個介面應該相同類型。**

下列程序說明如何新增工作階段時有兩個網路介面 StorSimple 裝置連接具有兩個網路介面主機。

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>若要設定高可用性 MPIO 及負載平衡

1. 執行探索的目標︰ 在**iSCSI 啟動器屬性**] 對話方塊中，在 [**探索**] 索引標籤上按一下 [**探索] 入口網站**。
2. 在 [**連線至目標**] 對話方塊中，輸入一個裝置網路介面的 IP 位址。
3. 按一下 [若要返回 [ **iSCSI 啟動器屬性**] 對話方塊的**[確定]** 。

4. 在**iSCSI 啟動器屬性**] 對話方塊中，選取**目標**] 索引標籤醒目提示發現的目標，，然後按一下 [**連線**。 **連線到目標**] 對話方塊隨即出現。

5. 在 [**連線至目標**] 對話方塊中︰
    
    - 將預設選取的目標設為 [新增**此連線**的最愛的目標清單。 這會使自動嘗試重新啟動連線，每次重新啟動此電腦的裝置。
    - 選取 [**啟用多重路徑**] 核取方塊。
    - 按一下 [**進階**]。

6. 在 [**進階設定**] 對話方塊中︰
    - 在 [**本機介面卡**] 下拉式清單中，選取 [ **Microsoft iSCSI 啟動器**]。
    - 在 [**啟動器 IP**下拉式清單中，選取主應用程式的 IP 位址。
    - 在**目標入口網站的 IP**下拉式清單中，選取 [啟用裝置上的資料介面的 IP 位址。
    - 按一下**[確定]**返回 iSCSI 啟動器屬性] 對話方塊。

7. 按一下 [**屬性**]，然後在 [**屬性**] 對話方塊中，按一下 [**新增工作階段**。

8. 在 [**連線至目標**] 對話方塊中，選取 [**啟用多重路徑**] 核取方塊，然後再按一下 [**進階**。

9. 在 [**進階設定**] 對話方塊中︰
    1. 在 [**本機介面卡**] 下拉式清單中，選取 [ **Microsoft iSCSI 啟動器**]。
    2. 在 [**啟動器 IP**下拉式清單中，選取對應至主機上的第二個介面的 IP 位址。
    3. 在**目標入口網站的 IP**下拉式清單中，選取 [啟用裝置上的第二個資料介面的 IP 位址。
    4. 按一下 [若要返回 [ **iSCSI 啟動器屬性**] 對話方塊的**[確定]** 。 您現在已新增至目標的第二個工作階段。

10. 重複步驟 8-10 （路徑） 的額外工作階段新增至目標。 主機上的兩個介面及兩個裝置上，您可以新增合計的四個工作階段。

11. 新增後所要的工作階段 （路徑），在**iSCSI 啟動器屬性**] 對話方塊中，選取目標並按一下 [**內容**。 在 [**屬性**] 對話方塊的 [工作階段] 索引標籤中，注意的四個階段會對應到路徑排列方式的識別碼。 若要取消工作階段，選取工作階段識別碼] 旁的核取方塊，然後再按一下 [**中斷連線**。

12. 若要檢視工作階段中呈現的裝置，請選取 [**裝置**] 索引標籤。 若要設定所選的裝置的 MPIO 原則，按一下 [ **MPIO**]。 **裝置詳細資料**] 對話方塊會顯示。 在 [ **MPIO** ] 索引標籤上，您可以選取適當的**負載平衡原則**設定。 您也可以檢視的**使用中**] 或 [**待命**路徑類型。

## <a name="next-steps"></a>後續步驟

瞭解更多關於[使用 StorSimple 管理員服務修改 StorSimple 裝置設定](storsimple-modify-device-config.md)。
 
