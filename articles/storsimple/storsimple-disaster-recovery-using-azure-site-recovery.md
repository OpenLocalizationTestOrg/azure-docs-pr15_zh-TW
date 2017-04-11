<properties 
   pageTitle="自動化 DR StorSimple 使用 Azure 網站復原檔案共用區 |Microsoft Azure"
   description="說明的步驟和建立裝載於 StorSimple 儲存的檔案共用區損壞修復解決方案的最佳作法。"
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>使用 Azure 網站復原檔案共用區裝載於 StorSimple 自動化損毀修復解決方案

## <a name="overview"></a>概觀

Microsoft Azure StorSimple 是混合式雲端儲存空間方案的地址的複雜的非結構化資料經常相關聯的檔案共用。 StorSimple 會使用雲端儲存空間的內部部署解決方案並自動層資料延伸儲存內部部署和雲端儲存空間。 與本機整合資料保護雲端快照，就不需要正在擴張的儲存空間基礎結構。

[Azure 網站復原](../site-recovery/site-recovery-overview.md)是 Azure 基礎的服務，提供損毀修復 (DR) 功能來協調複寫、 錯誤移轉及復原的虛擬機器。 Azure 網站復原支援複寫技術一致的方式複製、 保護及完美容錯虛擬機器和私人/公用或託管雲朵應用程式的數字。

您可以使用 Azure 網站修復、 虛擬機器複寫和 StorSimple 雲端快照功能，保護完整的檔案伺服器環境。 時中斷，您可以使用 [按一下以將 Azure 中的線上的檔案共用，在幾分鐘。

如何建立損壞修復解決方案裝載於 StorSimple 儲存空間，您的檔案共用及執行計劃、 尚未計劃，並測試使用單鍵復原計劃的容錯移轉詳細說明這份文件。 實際上，它會顯示如何修改復原計劃中啟用 StorSimple 容錯移轉損毀狀況期間您 Azure 網站復原保存庫。 此外，它描述支援的設定和先決條件。 這份文件，假設您已熟悉 Azure 網站復原和 StorSimple 架構的基本概念。

## <a name="supported-azure-site-recovery-deployment-options"></a>支援的 Azure 網站復原部署選項

客戶可以部署檔案伺服器實體伺服器或 HYPER-V 或 VMware 上, 執行的虛擬機器 (Vm)，然後再建立從劃分登出 StorSimple 儲存空間的磁碟區的 [檔案共用。 Azure 網站復原可以保護實體和虛擬部署至次要網站或 Azure。 本文涵蓋 DR 解決方案 Azure VM 裝載於 HYPER-V 檔案伺服器的復原網站與 StorSimple 存放區上的檔案共用的詳細資料。 同樣可以實作 VMware VM 或實體的電腦上的檔案伺服器 VM 其他案例。

## <a name="prerequisites"></a>必要條件

實作單一損壞修復解決方案 Azure 網站復原用於裝載於 StorSimple 儲存的檔案共用具有下列先決條件︰

-   內部部署 VM 主控 HYPER-V 或 VMware 或實體的電腦的 Windows Server 2012 R2 檔案伺服器

-   使用 Azure StorSimple 管理員註冊 StorSimple 存放裝置內部部署

-   Azure StorSimple 管理員 （這可以保留關機狀態） 中所建立的 StorSimple 雲端應用裝置

-   裝載於 StorSimple 存放裝置上設定的媒體檔案共用

-   建立 Microsoft Azure 訂閱中的[azure 網站復原服務保存庫](../site-recovery/site-recovery-vmm-to-vmm.md)

此外，如果 Azure 復原網站，Vm，以確保這些相容於 Azure Vm 和 Azure 網站復原服務上執行[Azure 虛擬機器整備評估工具](http://azure.microsoft.com/downloads/vm-readiness-assessment/)。

若要避免的延遲 （這可能會產生更高的成本） 的問題，請確定您建立您的 StorSimple 雲端應用裝置、 自動化帳戶和儲存在相同地區帳戶。

## <a name="enable-dr-for-storsimple-file-shares"></a>啟用 DR StorSimple 檔案共用區  

內部部署環境的每個元件都必須啟用完整的複寫及修復會受到保護。 本節將說明如何︰

-   設定 Active Directory 和 DNS 複寫 （選用）

-   若要啟用檔案伺服器 VM 的保護使用 Azure 網站復原

-   啟用 StorSimple 區的保護

-   設定網路

### <a name="set-up-active-directory-and-dns-replication-optional"></a>設定 Active Directory 和 DNS 複寫 （選用）

如果您想要保護執行 Active Directory 和 DNS，讓他們可在 DR 網站上的電腦，您需要明確保護 （使檔案伺服器存取與驗證透過失敗之後）。 有兩個建議的選項，根據客戶的內部部署環境的複雜性。

#### <a name="option-1"></a>選項 1

如果客戶有少數幾個應用程式，整個的單一網域控制站內部網站，並會在整個網站、 失敗，我們建議您使用 Azure 網站復原複寫網域控制站電腦複製至次要網站 （這是適用於網站的網站和網站-Azure），然後。

#### <a name="option-2"></a>選項 2

如果客戶有大量的應用程式、 執行 Active Directory 樹系會在幾個應用程式失敗，一次，然後建議 DR 網站上的其他網域控制站的設定 (次要網站或 Azure 中)。

請參閱[Active Directory 和 DNS 使用 Azure 網站復原自動 DR 解決方案](../site-recovery/site-recovery-active-directory.md)的指示進行網域控制站的 DR 網站上的可用時。 本文件其餘部分，我們將假設網域控制站有 DR 網站上。

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>若要啟用檔案伺服器 VM 的保護使用 Azure 網站復原

此步驟中，您必須準備內部部署檔案伺服器環境、 建立及準備 Azure 網站復原保存庫和啟用的 VM 檔案保護。

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>準備內部部署檔案伺服器環境

1.  設定**使用者帳戶控制****永遠不會**通知。 讓您可以使用 Azure 自動化指令碼連線 iSCSI 目標 Azure 網站復原被失敗之後，這是必要。

    1.  按下 Windows 鍵 + Q 和**UAC**搜尋。

    2.  選取 [**變更使用者帳戶控制] 設定**。

    3.  **不要通知**向下拖曳的列。

    4.  按一下**[確定]** ，然後選取**[是]**出現提示時。

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  每個檔案伺服器 Vm 安裝 VM 代理程式。 讓您可以透過 Vm 上失敗執行 Azure 自動化指令碼，這是必要。

    1.  [下載代理程式](http://aka.ms/vmagentwin) `C:\\Users\\<username>\\Downloads`。

    2.  以系統管理員模式 （以管理員身分執行），開啟 Windows PowerShell，然後輸入下列命令以瀏覽至 [下載位置︰

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] 根據版本而定，可能會變更檔案名稱。

1.  按一下 [**下一步**]。

2.  接受**條款的合約**，然後按一下 [**下一步**。

3.  按一下 [**完成**]。


1.  建立使用劃分登出 StorSimple 儲存空間的磁碟的檔案共用。 如需詳細資訊，請參閱[使用 StorSimple 管理員服務管理區](storsimple-manage-volumes.md)。

    1.  在您的內部部署 Vm，按下 Windows 鍵 + Q 和**iSCSI**搜尋。

    2.  選取 [ **iSCSI 啟動器**]。

    3.  選取 [**設定**] 索引標籤，然後複製啟動器名稱。

    4.  [Azure 傳統入口網站](https://manage.windowsazure.com/)登入。

    5.  選取 [ **StorSimple** ] 索引標籤，然後選取 [包含的實體裝置 StorSimple 管理員服務。

    6.  建立大量容器，然後建立區。 （這些磁碟區是在檔案伺服器 Vm 上檔案共用）。 複製啟動器名稱，並授與適當的名稱，Access 控制項記錄，當您建立區。

    7.  選取 [**設定**] 索引標籤和向下裝置的 IP 位址的筆記。

    8.  在您的內部部署 Vm 再次移至**iSCSI 啟動器**並輸入 IP 快速連線] 區段中。 按一下 [**快速連線**（裝置現在應該已經連線）]。

    9.  開啟 Azure 管理入口網站，然後選取 [**磁碟區和裝置**] 索引標籤。 按一下 [**自動設定**]。 將您剛剛建立的音量應該會出現。

    10. 在入口網站中，選取 [**裝置**] 索引標籤，然後選取**建立新的虛擬裝置。** （此虛擬裝置會使用發生容錯移轉）。 此新的虛擬裝置保留在離線狀態以避免額外的成本。 離線虛擬裝置，移至**虛擬機器**區段入口網站上，將它關閉。

    11. 回到內部部署 Vm 並開啟 [磁碟管理 （按下 Windows 鍵 + X，然後選取**磁碟管理**）。

    12. 您會發現一些額外的磁碟 （根據您所建立的區的數量）。 以滑鼠右鍵按一下第一個、 選取**初始化磁碟**，然後選取**[確定]**。 以滑鼠右鍵按一下**未配置**區段中**新增簡單的大量**、 將其指派一個磁碟機字母，然後選取完成精靈。

    13. 重複步驟 l 所有磁碟。 現在，您就可以在 Windows 檔案總管中的**這台電腦**上看到所有的磁碟。

    14. 使用這些磁碟上建立的檔案共用的檔案與存放服務的角色。

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>建立並準備 Azure 網站復原保存庫

請參閱瞭解如何開始使用 Azure 網站復原之前保護檔案伺服器 VM [Azure 網站復原文件](../site-recovery/site-recovery-hyper-v-site-to-azure.md)。

#### <a name="to-enable-protection"></a>若要啟用保護

1.  中斷您想要透過 Azure 網站復原保護內部部署 Vm iSCSI 目標︰

    1.  按下 Windows 鍵 + Q 和**iSCSI**搜尋。

    2.  選取 [**設定 iSCSI 啟動器**]。

    3.  中斷連線您先前連線 StorSimple 裝置。 或者，您可以關閉檔案伺服器幾分鐘的時間時啟用保護。

    > [AZURE.NOTE] 這會導致要暫時無法使用的檔案共用

1.  檔案伺服器 VM 從 Azure 網站復原入口網站的 [[啟用虛擬機器保護](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication)。

2.  當初始同步處理開始時，您可以一次重新連線目標。 移至 iSCSI 啟動器、 選取 StorSimple 裝置，然後按一下 [**連線**]。

3.  當完成同步處理且 VM 的狀態是 [**受保護**時，請選取 VM [**設定**] 索引標籤，然後選取適當地更新 VM 的網路 （這是透過 VM(s) 失敗會部分網路）。 如果沒有顯示網路，這表示同步處理，仍即將。

### <a name="enable-protection-of-storsimple-volumes"></a>啟用 StorSimple 區的保護

如果您尚未選取 StorSimple 區的 [**啟用此大量的預設備份**] 選項，在 StorSimple 管理員服務中，移至 [**備份原則**，建立所有區適合備份原則。 我們建議您想要查看應用程式復原點目標 (RPO) 將備份的頻率。

### <a name="configure-the-network"></a>設定網路

檔案伺服器 VM，網路設定 Azure 網站修復，好讓 VM 網路後移轉後附加至正確的 DR 網路。

您可以選取 VM **VMM 雲端**或**保護] 群組**，若要設定網路的設定，，如下圖所示。

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>建立一個修復計劃

您可以建立一個修復計劃 ASR 自動容錯移轉程序的檔案共用中。 如果中斷發生，您就可以將檔案共用稍候幾分鐘，只要按一下。 若要啟用此自動化，您必須 Azure 自動化帳戶。

#### <a name="to-create-the-account"></a>若要建立帳戶

1.  移至 Azure 傳統入口網站，請移至 [**自動化**] 區段。

1.  建立新的自動化帳戶。 將它放在同一個地理/地區的 StorSimple 雲端應用裝置和儲存帳戶所建立。

2.  按一下 [**新增** &gt; **應用程式服務** &gt; **自動化** &gt; **Runbook** &gt;將自動化帳戶匯入所有必要的 runbooks**從圖庫**。

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  從圖庫中的 [**損毀修復**] 窗格中新增下列 runbooks:

    -   透過 StorSimple 大量容器失敗

    -   清理 StorSimple 磁碟區之後測試容錯移轉 (TFO)

    -   裝載區 StorSimple 後移轉後的裝置上

    -   開始 StorSimple 虛擬應用裝置

    -   解除安裝 Azure VM 中的 [自訂指令碼副檔名

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  發佈的所有指令碼選取 runbook 自動化帳戶中，前往 [**作者**] 索引標籤。 在此步驟中，[ **Runbooks** ] 索引標籤會顯示，如下所示︰

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  在自動化帳戶移至 [**資產**] 索引標籤，請按一下 [**新增設定** &gt; **新增認證**，並新增 Azure 認證 – 命名資產 AzureCredential。

    使用 Windows PowerShell 認證。 應該包含組織識別碼使用者名稱和密碼與存取此 Azure 訂閱，以及停用的多重因素驗證與認證。 這是必要期間容錯移轉代表使用者進行驗證，並顯示 DR 網站上的檔案伺服器磁碟。

1.  自動化帳戶] 中，選取 [**資產**] 索引標籤，然後按一下 [**新增設定** &gt; **新增變數**並新增下列變數。 您可以選擇加密這些資產。 這些變數是復原計劃-特定。 如果您修復計劃 （由您將建立在下一步） 名稱 TestPlan，就變數應該 TestPlan StorSimRegKey、 TestPlan AzureSubscriptionName，依此類推。

    -   *RecoveryPlanName***-StorSimRegKey**: StorSimple 管理員服務的登錄機碼。

    -   *RecoveryPlanName***-AzureSubscriptionName**: Azure 訂閱的名稱。

    -   *RecoveryPlanName***-ResourceName**: StorSimple 裝置有 StorSimple 資源的名稱。

    -   *RecoveryPlanName***-裝置名稱**︰ 的裝置，都無法透過會失敗。

    -   *RecoveryPlanName***-TargetDeviceName**: 在容器中有要移轉的 StorSimple 雲端應用裝置。

    -   *RecoveryPlanName***-VolumeContainers**︰ 逗點分隔的字串，需要失敗，; 在裝置上的大量容器例如，volcon1 volcon2、 volcon3。

    -   RecoveryPlanName**-TargetDeviceDnsName**︰ 目標裝置的服務名稱 (此可以在 [**虛擬機器**] 區段中找到︰ 服務名稱是 DNS 名稱相同)。

    -   *RecoveryPlanName***-StorageAccountName**: 指令碼 （已執行失敗透過 VM） 會儲存在其中儲存體帳戶名稱。 這可能是有一些空間暫時儲存指令碼的任何儲存帳戶。

    -   *RecoveryPlanName***-StorageAccountKey**︰ 上方的儲存空間帳戶的便捷鍵。

    -   *RecoveryPlanName***-ScriptContainer**︰ 容器的指令碼會儲存在雲端的名稱。 如果沒有容器，它將會建立。

    -   *RecoveryPlanName***-VMGUIDS**︰ 時保護 VM，Azure 網站復原指派每一個 VM 透過 VM 提供失敗的詳細資料的唯一識別碼。 若要取得 VMGUID，請選取 [**修復服務**] 索引標籤，然後按一下 [**受保護的項目** &gt; **保護群組** &gt; **機器** &gt; **屬性**。 如果您有多個 Vm，然後新增 Guid 以逗號分隔字串。

    -   *RecoveryPlanName***-AutomationAccountName** – 已新增 runbooks 和資產的自動化帳戶的名稱。

    例如，如果 fileServerpredayRP 復原計劃的名稱，然後您**資產**] 索引標籤應該會出現，如下所示後新增所有的資產。

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  移至 [**修復服務**] 區段，然後選取您先前建立的 Azure 網站復原保存庫。

2.  選取 [**復原方案**] 索引標籤，然後建立新的復原計劃，如下所示︰

    。  指定的名稱，然後選取適當的**保護] 群組**。

    b。  從您想要包含在復原計劃中的 [保護] 群組中，選取 Vm。

    c。  復原之後會建立計劃，請選取開啟復原規劃自訂檢視。

    d。  選取**所有群組關機**，按一下**指令碼**，然後選擇**[新增所有群組關機之前主端指令碼**。

    e。  選取自動化 （在其中您新增的帳戶 runbooks），然後選取 [**無法透過 StorSimple-大量容器**runbook。

    f。  按一下 [**群組 1︰ 啟動**，選擇**虛擬機器**，並新增 Vm 復原計劃中受保護的。

    g。  按一下 [**群組 1︰ 啟動**，選擇 [**指令碼**，然後新增下列的指令碼順序，**群組 1 後**步驟。

    - 開始-StorSimple-虛擬-應用裝置 runbook
    - 無法移轉 StorSimple-大量容器 runbook
    - 裝載區-之後-容錯 runbook
    - 解除安裝-自訂-指令碼-副檔名 runbook

1.  將手動動作後上述 4 的指令碼加入同一**群組 1︰ 後步驟**一節。 此動作是，您可以確認一切運作正常。 此動作需要加入只測試容錯移轉 （因此只選取 [**測試容錯移轉**核取方塊） 的一部分。

2.  手動的動作之後, 新增使用您用於其他 runbooks 的相同程序的清理指令碼。 儲存復原計劃。

    > [AZURE.NOTE] 執行中時測試容錯移轉，您應該確認手動動作步驟的所有項目，因為有目標裝置已複製的 StorSimple 區會刪除清理的一部分，完成手動動作後。

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>執行測試容錯移轉

在測試容錯移轉參照到 Active Directory 特定的考量的[Active Directory DR 解決方案](../site-recovery/site-recovery-active-directory.md)小幫手輔助線。 測試容錯移轉發生時，會完全影響內部部署安裝。 已連接到內部部署 VM StorSimple 區會複製到 StorSimple 雲端應用裝置上 Azure。 進行測試 VM 啟動 Azure 中，複製的區附加至 VM。

#### <a name="to-perform-the-test-failover"></a>若要執行測試容錯移轉

1.  在 Azure 傳統的入口網站中，選取您的網站復原保存庫。

1.  按一下 [建立的檔案伺服器 VM 的復原計劃]。

2.  按一下 [**測試容錯移轉**]。

3.  選取開始測試容錯移轉程序的虛擬網路。

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  設定次要環境時，您可以執行您驗證。

2.  驗證完成後，按一下 [**完成驗證**]。 將清除測試容錯移轉環境，並會完成 TFO 作業。

## <a name="perform-an-unplanned-failover"></a>執行意外的容錯移轉

在非預期的容錯移轉，StorSimple 區無法透過虛擬裝置、 複本 VM 會啟動 Azure 上，且區附加至 VM。

#### <a name="to-perform-an-unplanned-failover"></a>若要執行意外的容錯移轉

1.  在 Azure 傳統的入口網站中，選取您的網站復原保存庫。

1.  按一下 [建立的檔案伺服器 VM 復原計劃]。

2.  按一下 [**容錯移轉**，然後選取 [**意外的容錯移轉**。

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  選取目標網路，然後按一下 [檢查圖示 ✓ 開始容錯移轉程序。

## <a name="perform-a-planned-failover"></a>執行計劃的容錯移轉

在計劃的容錯移轉，內部部署的檔案的伺服器 VM 正常關機備份 StorSimple 裝置上區的快照將雲端。 StorSimple 區無法透過虛擬裝置複本 VM 啟動 Azure 上與區附加至 VM。

#### <a name="to-perform-a-planned-failover"></a>若要執行計劃的容錯移轉

1.  在 Azure 傳統的入口網站中，選取您的網站復原保存庫。

1.  按一下 [建立的檔案伺服器 VM 的復原計劃]。

2.  按一下 [**容錯移轉**，然後選取 [**計劃的容錯移轉**。

3.  選取目標網路，然後按一下 [檢查圖示 ✓ 開始容錯移轉程序。

## <a name="perform-a-failback"></a>執行回復

期間回復，StorSimple 大量容器會無法透過回到實體裝置之後在備份。

#### <a name="to-perform-a-failback"></a>若要執行回復

1.  在 Azure 傳統的入口網站中，選取您的網站復原保存庫。

1.  按一下 [建立的檔案伺服器 VM 復原計劃]。

2.  按一下 [**容錯移轉**，選取 [**預定容錯移轉**或**意外的容錯移轉**。

3.  按一下 [**變更方向**]。

4.  選取適當的資料同步處理及 VM 建立選項。

5.  按一下核取圖示 ✓ 開始回復程序。

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>最佳作法

### <a name="capacity-planning-and-readiness-assessment"></a>容量規劃及整備評估


#### <a name="hyper-v-site"></a>HYPER-V 網站

使用[使用者容量規劃工具](http://www.microsoft.com/download/details.aspx?id=39057)設計伺服器、 儲存及 HYPER-V 複本環境的網路基礎結構。

#### <a name="azure"></a>Azure

您可以執行[Azure 虛擬機器整備評估工具](http://azure.microsoft.com/downloads/vm-readiness-assessment/)，以確保這些相容於 Azure Vm 和 Azure 網站修復服務 Vm 上。 整備評估工具會檢查 VM 設定，並會與 Azure 不相容設定時的警告。 例如，它會發出警告如果 c 磁碟大於 127 GB。


容量計劃是組成的兩個以上的重要程序︰

-   對應的內部部署 HYPER-V Vm Azure VM 大小 （例如 A6、 A7、 A8 和 A9）。

-   決定所需的網際網路頻寬。

## <a name="limitations"></a>限制

- 目前，只有 1 StorSimple 裝置可以透過失敗，（以單一 StorSimple 雲端應用裝置中）。 不支援的檔案伺服器橫跨多個 StorSimple 裝置的案例。

- 如果您啟用保護 vm 時收到錯誤，請確定您已經中斷連線 iSCSI 目標。

- 有由於橫跨大量容器的備份原則一起組成群組的所有大量容器會都無法在一起。

- 在您所選擇的大量容器中的所有磁碟區會移轉。

- 無法透過失敗加總超過 64 TB 成的區，因為單一的 StorSimple 雲端應用裝置的最大容量 64 TB。

- 如果計劃/意外的容錯移轉失敗，而 Vm 建立 Azure，然後清除 Vm。 請改為執行回復。 如果您刪除 Vm 然後內部部署 Vm 無法開啟一次。

- 後移轉後，如果您不能看到區，移至 Vm、 開啟磁碟管理、 重新掃描磁碟，然後線上讓它們。

- 在某些情況下，可能會不同於字母內部 DR 網站中的磁碟機字母。 如果這種情況，您必須以手動方式容錯移轉完成後，修正問題。

- 多重因素驗證應該停用 Azure 中為資產的自動化帳戶輸入認證。 如果此驗證未停用，不會允許指令碼自動執行，並修復計劃會失敗。

- 容錯移轉工作逾時︰ StorSimple 指令碼會逾時，如果大量容器的容錯移轉耗費更多的時間超過 Azure 網站復原限制每指令碼 （目前 120 分鐘）。

- 備份工作逾時︰ StorSimple 指令碼時發生逾時，如果區備份需要更多的時間超過 Azure 網站復原限制每指令碼 （目前 120 分鐘）。
 
    > [AZURE.IMPORTANT] 手動執行備份，從 Azure 入口網站，然後再次執行復原計劃。

- 複製工作逾時︰ StorSimple 指令碼時發生逾時，如果複製區耗費更多的時間超過 Azure 網站復原限制每指令碼 （目前 120 分鐘）。

- 時間同步處理錯誤︰ StorSimple 指令碼錯誤出拒絕備份已失敗，即使備份已順利在入口網站。 可能的原因，這可能是 StorSimple 應用裝置的時間，可能是目前時區的時間與同步處理。
 
    > [AZURE.IMPORTANT] 同步處理應用裝置時間以目前時區的時間。

- 應用裝置容錯移轉錯誤︰ StorSimple 指令碼時，如果應用裝置容錯移轉執行復原計劃可能會失敗。
    
    > [AZURE.IMPORTANT] 應用裝置容錯移轉完成後，重新執行復原計劃。

## <a name="summary"></a>摘要

使用 Azure 網站復原，您可以建立在檔案伺服器 VM 完成自動化損毀修復計畫有裝載於 StorSimple 儲存的檔案共用。 您可以從任何地方數秒內啟動容錯移轉時中斷，以及如何取得應用程式啟動和執行稍候幾分鐘。
