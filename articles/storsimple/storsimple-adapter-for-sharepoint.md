<properties 
   pageTitle="Sharepoint StorSimple 介面卡 |Microsoft Azure"
   description="說明如何安裝和設定或移除 StorSimple 介面卡的 SharePoint 伺服器陣列中的 SharePoint。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/11/2016"
   ms.author="v-sharos" />

# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>安裝和設定 StorSimple 介面卡的 SharePoint

## <a name="overview"></a>概觀

StorSimple 介面卡的 SharePoint 是元件，可讓您提供 Microsoft Azure StorSimple 彈性儲存與 SharePoint 伺服器陣列的資料保護功能。 若要從 SQL Server 內容資料庫的二進位大型物件 (BLOB) 內容移到 Microsoft Azure StorSimple 混合式雲端儲存裝置，您可以使用介面卡。

StorSimple 介面卡的 SharePoint 函數做為遠端 BLOB 儲存體 (RBS) 提供者，並使用 [SQL Server 遠端 BLOB 儲存體] 功能在檔案伺服器備份 StorSimple 裝置非結構化的 SharePoint 內容 （形式儲存的二進位大型物件）。

>[AZURE.NOTE] StorSimple 介面卡的 SharePoint 支援 SharePoint Server 2010 遠端 BLOB 儲存體 (RBS)。 不支援 SharePoint Server 2010 外部 BLOB 儲存體 (EBS)。

- 若要下載 StorSimple 介面卡的 SharePoint，請移至[Sharepoint 的 StorSimple 介面卡][1]在 Microsoft 下載中心。

- 有關規劃 RBS 和 RBS 限制，請移至[使用 SharePoint 2013 中的 RBS 制定][ 2] [規劃 RBS (SharePoint Server 2010)]或[3]。

這個概觀，瞭解其他簡要描述 StorSimple 介面卡的角色適用的 SharePoint 和 SharePoint 容量和效能限制，您應該知道的安裝及設定介面卡之前。 檢閱這項資訊之後，請前往[StorSimple 介面卡的 SharePoint 安裝](#storsimple-adapter-for-sharepoint-installation)，若要開始設定介面卡。

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>SharePoint 優點 StorSimple 介面卡

在 SharePoint 網站中的內容會儲存為一或多個內容資料庫中的非結構化 BLOB 資料。 根據預設，這些資料庫被裝載在執行中的 SQL Server 位於 SharePoint 伺服器陣列的電腦上。 二進位大型物件快速可以增加大小，使用大量的內部部署的儲存空間。 因此，您可能會想要尋找另一個，較高的儲存空間方案。 SQL Server 提供稱為遠端 Blob 儲存體 (RBS)，可讓您將 BLOB 內容儲存在檔案系統、 外部 SQL Server 資料庫的技術。 RBS，Blob 可以存放在 SQL server 的電腦上的檔案系統中或另一個伺服器電腦上的檔案系統中可以儲存。

RBS 會要求您啟用 RBS 在 SharePoint 中的使用 RBS 提供者，例如 SharePoint StorSimple 介面卡。 StorSimple 介面卡的 SharePoint 搭配使用 RBS，可讓您將二進位大型物件移至 Microsoft Azure StorSimple 系統備份伺服器。 Microsoft Azure StorSimple 然後 BLOB 資料儲存在本機或在雲端，根據使用方式。 是非常作用中 （通常是指第 1 層或資料） 的二進位大型物件在本機上。 降低作用中的資料與保存的資料存放在雲端上。 內容資料庫上啟用 RBS 之後，在 SharePoint 中建立的任何新 BLOB 內容會儲存在 StorSimple 裝置並不是內容資料庫中。

Microsoft Azure StorSimple 實作 RBS 有下列好處︰

- 移動到另一個伺服器的 BLOB 內容，可減少可以改善 SQL Server 回應的 SQL Server 上的查詢載入。 

- Azure StorSimple 使用 deduplication 和壓縮縮小資料。

- Azure StorSimple 提供資料保護表單的本機和雲端快照集。 此外，如果您將資料庫本身 StorSimple 裝置上時，您可以備份設定內容資料庫及 Blob 一起損毀一致的方式。 （移動至裝置的內容資料庫只支援 StorSimple 8000 數列裝置。 此功能不支援 5000 或 7000 系列。）

- Azure StorSimple 包含損毀修復功能，包括容錯移轉、 檔案和大量修復 （包括測試復原），以及快速還原資料。

- 您可以使用資料修復軟體，例如 Kroll Ontrack PowerControls，和 StorSimple 的執行 SharePoint 內容的項目層級復原 BLOB 資料的快照。 （此資料修復軟體是個別的購買）。

- Sharepoint StorSimple 介面卡插入 [SharePoint 管理中心] 入口網站，讓您可以從中央位置管理整個 SharePoint 解決方案。

BLOB 內容移動至 [檔案系統可以提供其他節省成本和優點。 例如 [使用 RBS 可減少需要寶貴的第 1 層儲存和其縮小內容的資料庫，因為 RBS 降低 SharePoint 伺服器陣列中所需的資料庫的數目。 不過，例如資料庫大小限制的其他因素和非 RBS 內容量也會影響儲存需求。 如需有關使用 RBS 的優點與成本的詳細資訊，請參閱[規劃 RBS (SharePoint Foundation 2010)] [4]及[使用 SharePoint 2013 中的 RBS 制定][5]。

### <a name="capacity-and-performance-limits"></a>容量和效能的限制

您考慮使用 RBS 中的 SharePoint 解決方案之前，您應該知道的測試的效能與容量限制 SharePoint Server 2010 與 SharePoint Server 2013，以及這些限制整體可接受的效能。 如需詳細資訊，請參閱[SharePoint 2013 的軟體界限與限制](https://technet.microsoft.com/library/cc262787.aspx)。

設定 RBS 之前，請檢閱下列動作︰

- 請確定內容 （的內容資料庫大小） 加上任何相關聯的允二進位大型物件的大小的總大小不超過支援 SharePoint RBS 大小限制。 這項限制為 200 GB。 

    **量值內容資料庫及 BLOB 大小**

     1. 在管理中心管理 WFE 執行這個查詢。 啟動 SharePoint 管理命令介面，然後輸入下列 Windows PowerShell 命令，以取得的內容資料庫大小︰

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         此步驟中取得磁碟上的內容資料庫的大小。

     2. 執行下列 SQL 查詢的 SQL Management Studio 中 SQL server 上方塊上每個內容的資料庫，並新增結果的數字的中取得步驟 1。

        在 SharePoint 2013 內容資料庫，請輸入︰

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        在 SharePoint 2010 內容的資料庫，請輸入︰

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        此步驟會有已允的二進位大型物件的大小。

- 我們建議您儲存所有 BLOB 與資料庫內容本機 StorSimple 裝置上。 StorSimple 裝置是兩個節點叢集高可用性。 將內容資料庫及 Blob StorSimple 裝置上，提供高可用性。

    若要移動的內容資料庫至 StorSimple 裝置使用傳統的 SQL Server 移轉最佳作法。 只有在透過 RBS 資料庫中的所有 BLOB 內容都移至檔案共用後，請將資料庫。 如果您選擇要移動的內容資料庫至 StorSimple 裝置，我們建議您為主要的大量裝置上設定內容資料庫儲存空間。

- 在 Microsoft Azure StorSimple，如果使用分層的區，有不保證儲存在本機裝置不會層到 Microsoft Azure 雲端儲存空間 StorSimple 上的內容。 因此，建議使用搭配 SharePoint RBS StorSimple 已釘選至本機磁碟。 如此可確保所有 BLOB 內容仍會保留本機 StorSimple 在裝置上，並不會移至 Microsoft Azure。

- 如果您沒有儲存內容的資料庫 StorSimple 裝置上，使用傳統 SQL Server 可用性最佳作法支援 RBS。 SQL Server 叢集支援 RBS、 SQL Server 時鏡像並不會。 

>[AZURE.WARNING] 如果您有沒有啟用 RBS，我們不建議將內容資料庫移至 StorSimple 裝置。 這是未經測試的設定。
 
## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple 介面卡的 SharePoint 安裝

您的 SharePoint 安裝 StorSimple 介面卡前，您必須設定 StorSimple 裝置，並確定 SharePoint 伺服器陣列和 SQL Server [實例符合所有的先決條件。 本教學課程說明設定需求，以及您在安裝及升級 StorSimple 介面卡的 SharePoint 程序。 

## <a name="configure-prerequisites"></a>設定的先決條件

您可以安裝 StorSimple 介面卡的 SharePoint 之前，務必 StorSimple 裝置 SharePoint 伺服器陣列，與 SQL Server [實例符合下列條件。

### <a name="system-requirements"></a>系統需求

Sharepoint StorSimple 介面卡適用於下列硬體與軟體︰

- 支援的作業系統 – Windows Server 2008 R2 SP1、 Windows Server 2012 或 Windows Server 2012 R2 

- 支援的 SharePoint 版本 – SharePoint Server 2010 或 SharePoint Server 2013

- 支援的 SQL Server 版本 – SQL Server 2008 企業版、 SQL Server 2008 R2 企業版或 SQL Server 2012 企業版

- 支援 StorSimple 裝置 – StorSimple 8000 數列、 StorSimple 7000 系列] 或 StorSimple 5000 數列。

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple 裝置設定先決條件

StorSimple 裝置是區塊的裝置，因此需要使用資料可以在其裝載於檔案伺服器。 我們建議您從 SharePoint 伺服器陣列使用不同的伺服器，而不是現有的伺服器。 這個檔案伺服器裝載的內容資料庫的 SQL Server 電腦必須在相同的區域網路 (LAN)。 

>[AZURE.TIP] 
>
>- 如果您設定高可用性 SharePoint 伺服器陣列，您應該也部署高可用性檔案伺服器。
>
>- 如果您沒有儲存內容的資料庫 StorSimple 裝置上，使用傳統的可用性支援 RBS 的最佳作法。 SQL Server 叢集支援 RBS、 SQL Server 時鏡像並不會。 

請確定您 StorSimple 的裝置已正確，並且設定適當的區，以支援 SharePoint 部署且可從您 SQL Server 的電腦存取。 如果您還沒有部署並設定您的 StorSimple 裝置，請前往[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。 請注意 StorSimple 裝置; 的 IP 位址您必須將其期間 StorSimple 介面卡的 SharePoint 安裝。 

此外，請確定用於 BLOB externalization 大量符合下列需求︰

- 音量必須格式化 64 KB 分派單位大小。

- 您的 web 前端 (WFE) 和應用程式伺服器必須能夠存取透過通用命名慣例 (UNC) 路徑音量。 

- SharePoint server 伺服器陣列必須設定寫入到音量。

>[AZURE.NOTE] 安裝後，當您設定的介面卡時，所有 BLOB externalization 必須都通過 StorSimple 裝置 （裝置會顯示區 SQL Server，並管理儲存空間層）。 您無法使用任何其他目標的 BLOB externalization。
 
如果您打算使用 StorSimple 快照管理員 BLOB 與資料庫資料的快照，請務必在資料庫伺服器上安裝 StorSimple 快照管理員，讓它可以使用 SQL 寫入器服務，來實作 Windows 大量陰影複製服務 (VSS)。 

>[AZURE.IMPORTANT] StorSimple 快照管理員不支援 SharePoint VSS 寫入，而且不能使用 SharePoint 資料的應用程式一致的快照。 在 SharePoint 的案例中，StorSimple 快照管理員會提供當機一致的備份。 
 
## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint 伺服器陣列設定先決條件

請確定您的 SharePoint 伺服器陣列已正確設定，，如下所示︰

- 請確認您的 SharePoint 伺服器陣列的健全的狀態，並檢查下列項目︰ 

- 所有 SharePoint WFE 註冊伺服器陣列中的應用程式伺服器執行，並可以 ping 從您將會安裝 StorSimple 介面卡的 SharePoint 伺服器。

- SharePoint 計時器服務 （SPTimerV3 或 SPTimerV4） 執行每個 WFE 伺服器] 及 [應用程式伺服器。

- SharePoint 計時器服務與 SharePoint 管理中心網站執行的 IIS 應用程式集區具有系統管理權限。 

- 請確定已停用 [Internet Explorer 增強安全性內容 （IE 按 esc 鍵）。 請遵循下列步驟，停用 IE ESC:

    1. 關閉所有 Internet Explorer 的執行個體。

    2. 啟動 [伺服器管理員。

    3. 在左窗格中，按一下 [**本機的伺服器**。

    4. **在右窗格中， **IE 增強的安全性設定**] 旁按一下。**

    5. 在**系統管理員**，按一下 [**關閉**]。

    6. 按一下**[確定]**。

## <a name="remote-blob-storage-rbs-prerequisites"></a>遠端 BLOB 儲存體 (RBS) 先決條件

請確認您使用的受支援的版本的 SQL Server。 下列的版本是受支援，並且可以使用 [RBS:

- SQL Server 2008 企業版

- SQL Server 2008 R2 企業版

- SQL Server 2012 企業版

可以 externalized Blob，只在 SQL Server 呈現 StorSimple 裝置的區。 不支援 BLOB externalization 的任何其他的目標。

當您完成所有必要的設定步驟時，移至[安裝 sharepoint StorSimple 介面卡](#install-the-storsimple-adapter-for-sharepoint)。

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Sharepoint 中安裝 StorSimple 介面卡

使用下列步驟來安裝 StorSimple 介面卡的 SharePoint。 如果您要重新安裝軟體，請參閱[升級或重新安裝 SharePoint StorSimple 介面卡](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)。 安裝所需的時間，取決於您的 SharePoint 伺服器陣列中的 SharePoint 資料庫的總數。

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## <a name="configure-rbs"></a>設定 RBS

您的 SharePoint 安裝 StorSimple 介面卡之後，請在下列程序中所述設定 RBS。

>[AZURE.TIP] Sharepoint StorSimple 介面卡插入 SharePoint 管理中心] 頁面上，允許 RBS 啟用或停用 SharePoint 伺服器陣列中每個內容資料庫。 不過，啟用或停用內容資料庫的 RBS 會使 IIS 重設]，哪些，根據您的伺服器陣列設定，可以暫時會中斷 SharePoint web 前端 (WFE) 的可用性。 （使用前端負載平衡器、 目前的伺服器工作量等各種因素可以限制或排除此中斷）。若要防止使用者從干擾，建議您啟用或停用 RBS，只在 [預定進行的維修作業] 視窗。

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## <a name="configure-garbage-collection"></a>設定回收

從 SharePoint 網站刪除的物件，這些是不會自動從刪除 RBS 存放區。 不過，非同步，背景維護程式刪除孤立的二進位大型物件檔案存放區。 系統管理員可以排程定期執行此程序或他們可以啟動它必要時。

此進行的維修作業程式 (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) 自動上已安裝所有 SharePoint WFE 伺服器和應用程式伺服器當您啟用 RBS。 在下列位置安裝程式︰*啟動磁碟機*︰ \Program Files\Microsoft SQL 遠端 Blob 儲存體 10.50\Maintainer\

關於設定和使用該進行的維修作業程式，請參閱[SharePoint Server 2013 中的維護 RBS]資訊[8]。

>[AZURE.IMPORTANT] RBS maintainer 程式是大量的資源。 您應該排程它在 SharePoint 伺服器陣列中執行 [僅限精簡活動期間。

### <a name="delete-orphaned-blobs-immediately"></a>請立即刪除孤立的二進位大型物件

如果您需要立即刪除孤立的二進位大型物件時，您可以使用下列的指示。 請注意，這些指示範例如何完成這在 SharePoint 2013 部署中的下列元件︰

- 內容的資料庫名稱是 WSS_Content。
- SQL Server 名稱是 SHRPT13 SQL12\SHRPT13。
- Web 應用程式的名稱是 SharePoint – 80。

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>升級或重新安裝 SharePoint StorSimple 介面卡

使用下列程序升級 SharePoint server 和 sharepoint 重新安裝 StorSimple 介面卡，或只是升級或重新安裝在現有的 SharePoint 伺服器陣列中的介面卡。 

>[AZURE.IMPORTANT] 在您嘗試升級您的 SharePoint 軟體及/或升級或重新安裝 SharePoint StorSimple 介面卡，請檢閱下列資訊︰
>
>- 已透過 RBS 先前移至外部儲存任何檔案將無法使用，直到完成後再重新安裝並重新啟用 [RBS] 功能。 若要限制使用者的影響，請執行任何升級或重新安裝期間預定進行的維護視窗。
>
>- 根據 SharePoint 資料庫中的 SharePoint 伺服器陣列的總數，有所不同升級/重新安裝所需的時間。
>
>- 升級/重新安裝完畢後，您必須啟用 RBS 內容資料庫。 如需詳細資訊，請參閱[設定 RBS](#configure-rbs) 。
>
>- 如果您有大量的資料庫 （超過 200） 的 SharePoint 伺服器陣列設定 RBS， **SharePoint 管理中心**] 頁面可能逾時。 如果發生這種情形，重新整理頁面。 這並不會影響的設定程序。

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
## <a name="storsimple-adapter-for-sharepoint-removal"></a>SharePoint 移除 StorSimple 介面卡

下列程序說明如何將二進位大型物件移回 SQL Server 內容資料庫，然後解除 sharepoint 的安裝 StorSimple 介面卡。 

>[AZURE.IMPORTANT] 您必須將二進位大型物件移回內容資料庫前您解除安裝介面卡軟體。 

### <a name="before-you-begin"></a>開始之前 

將資料移回 SQL Server 內容資料庫並開始介面卡移除程序之前，請收集下列資訊︰

- RBS 已啟用的所有資料庫的名稱
- 設定 BLOB 儲存 UNC 路徑

### <a name="move-the-blobs-back-to-the-content-databases"></a>將二進位大型物件移回內容資料庫

解除安裝 SharePoint 軟體 StorSimple 介面卡之前，您必須回到 SQL Server 內容資料庫移轉已允的二進位大型物件的所有。 如果您嘗試解除 StorSimple 介面卡安裝 sharepoint，您將所有 Blob 都移回內容資料庫之前，您會看到下列警告訊息。

![警告訊息](./media/storsimple-adapter-for-sharepoint/sasp1.png)
 
####<a name="to-move-the-blobs-back-to-the-content-databases"></a>將二進位大型物件移回內容資料庫 

1. 下載每個允物件。

2. 開啟 [ **SharePoint 管理中心**] 頁面中，瀏覽至 [**系統設定**。 

3. **Azure StorSimple**，底下按一下 [**設定 StorSimple 介面卡**]。

4. **設定 StorSimple 介面卡**在頁面上，按一下 [**停用**] 按鈕下方每個您想要移除外部 BLOB 儲存體內容資料庫。 

5. 從 SharePoint 中，刪除的物件，然後再次以上傳這些檔案。

或者，您可以使用 Microsoft` RBS Migrate()` SharePoint 隨附的 PowerShell 指令程式。 如需詳細資訊，請參閱[移轉內容進或移出 RBS](https://technet.microsoft.com/library/ff628255.aspx)。

將二進位大型物件移回內容資料庫後，請移至下一步︰[解除安裝介面卡](#uninstall-the-adapter)。

### <a name="uninstall-the-adapter"></a>解除安裝介面卡

將二進位大型物件移回 SQL Server 內容資料庫之後，請使用下列選項的其中一個解除 StorSimple 介面卡安裝 sharepoint。

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>若要解除安裝介面卡使用安裝程式 

1. 使用系統管理員權限的帳戶登入網頁前端 (WFE) 伺服器。
2. 按兩下 StorSimple 介面卡的 SharePoint 安裝程式。 設定精靈會啟動。

    ![設定精靈](./media/storsimple-adapter-for-sharepoint/sasp2.png)

3. 按一下 [**下一步**]。 下列頁面隨即出現。

    ![設定精靈移除頁面](./media/storsimple-adapter-for-sharepoint/sasp3.png)

4. 按一下以選取 [移除程序的 [**移除**]。 下列頁面隨即出現。

    ![設定精靈確認頁面](./media/storsimple-adapter-for-sharepoint/sasp4.png)

5. 按一下 [**移除**]，以確認移除。 下列 [進度] 頁面隨即出現。

    ![設定精靈進度頁面](./media/storsimple-adapter-for-sharepoint/sasp5.png)

6. 移除完成後，會出現 [完成] 頁面。 按一下 [**完成**] 以關閉 [安裝精靈。

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>若要使用 [控制台] 解除安裝介面卡 

1. 開啟 [控制台]，然後按一下**[程式和功能**。

2. 選取**StorSimple 介面卡的 SharePoint**，然後按一下 [**解除安裝**]。 

## <a name="next-steps"></a>後續步驟

[深入瞭解 StorSimple](storsimple-overview.md)。

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx
