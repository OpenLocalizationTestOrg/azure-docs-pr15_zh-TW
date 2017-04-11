<properties 
   pageTitle="StorSimple 虛擬裝置更新 2 |Microsoft Azure"
   description="瞭解如何建立、 部署及管理 StorSimple 虛擬裝置的 Microsoft Azure 虛擬網路。 （適用於 StorSimple 更新 2）。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>部署及管理 StorSimple 虛擬裝置 Azure 中


##<a name="overview"></a>概觀
StorSimple 8000 數列虛擬裝置是您的 Microsoft Azure StorSimple 解決方案提供其他功能。 StorSimple 虛擬裝置執行虛擬機器中的 Microsoft Azure 虛擬網路、，您可以使用它來備份及從您的主機複製資料。 本教學課程說明如何部署及管理中 Azure 虛擬裝置，而是用於執行軟體版本更新 2 的虛擬裝置和下限。


#### <a name="virtual-device-model-comparison"></a>虛擬裝置模型比較

使用兩種模型、 （先前稱為 1100年） 標準 8010 和進階版 （中更新 2 推出） 8020 StorSimple 虛擬裝置。 比較兩個模型是即下方形成表格。


| 裝置模型          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **最大容量**      | 30 TB                                                                     | 64 TB                                                                                                                                |
| **Azure VM**              | Standard_A3 （4 個核心、 7 GB 的記憶體）                                                                      | Standard_DS3 （4 個核心、 14 GB 的記憶體）                                                                                                                          |
| **版本相容性** | 執行的版本前更新 2 或更新版本                                             | 執行更新 2 或更新版本                                                                                                  |
| **地區可用性**   | 所有的 Azure 區域                                                         | Azure 支援進階版儲存空間的區域<br></br>如區域的清單，請參閱[支援的 8020 區域](#supported-regions-for-8020) |
| **儲存類型**          | 使用本機磁碟 Azure 標準儲存體<br></br> 瞭解如何[建立標準儲存的帳戶]() | 使用本機磁碟<sup>2</sup> Azure 進階版儲存體 <br></br>瞭解如何[建立進階版儲存的帳戶](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **工作負載的指導方針**     | 從備份檔案的項目層級擷取                                              | 雲端開發和測試案例、 低延遲較高的效能負載 <br></br>修復損毀的次要裝置                                                                                            |
 
*稱為 1100年* <sup>1</sup> 。

<sup>2</sup> *8010 和 8020 都使用雲端層 Azure 標準的儲存空間。差異只存在於裝置中的本機層*。

#### <a name="supported-regions-for-8020"></a>受支援的 8020 的區域

8020 目前支援的進階版儲存空間區域是即下方形成表格。 為進階版儲存在多個區域內就可以使用這份清單，就會持續更新。 

| S 鍵。 沒有。                                                  | 在區域內目前支援 |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | 美國中部                     |
| 2                                                       |  設定適用於美國                       |
| 3                                                       |  設定適用於美國 2                     |
| 4                                                       | 西美制]。                        |
| 5                                                       | 北美歐洲                   |
| 6                                                       | 西歐                    |
| 7                                                       | 東南亞                 |
| 8                                                       | 日本東部                     |
| 9                                                       | 日本西部                     |
| 10                                                      | 澳大利亞東亞                 |
| 11                                                      | 澳大利亞 Southeast *           |
| 12                                                      | 東亞 *                     |
| 13                                                      | 南美洲的中央美國 *              |

* 這些 geos 是最近啟動進階版儲存空間。

本文將說明部署 StorSimple 虛擬裝置 Azure 中的逐步程序。 閱讀本文之後, 您將會︰

- 瞭解如何虛擬裝置與實際的裝置。

- 能夠建立及設定虛擬裝置。

- 連線至虛擬裝置。

- 瞭解如何使用虛擬裝置。

本教學課程適用於所有 StorSimple 虛擬裝置執行更新 2 及更新版本。 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>實體裝置如何與不同的虛擬裝置

StorSimple 虛擬裝置是 StorSimple 執行的 Microsoft Azure 虛擬機器中的單一節點的專用軟體版本。 虛擬裝置支援您的實體裝置無法使用，並適用於從備份、 內部部署損毀修復和雲端開發和測試案例的項目層級擷取損毀修復狀況。

#### <a name="differences-from-the-physical-device"></a>從實體裝置的差異

下表顯示一些主要 StorSimple 虛擬裝置和 StorSimple 實體裝置之間的差異。

|                             | 實體裝置                                          | 虛擬裝置                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **位置**                    | 位於資料中心。                               | Azure 中執行。                                                                            |
| **網路介面**          | 具有六個網路介面︰ 資料 0 至資料 5。                  | 只有一個網路介面︰ 資料 0。                                        |
| **註冊**                | 註冊期間設定步驟。                | 註冊是個別的任務。                                                          |
| **服務資料加密金鑰** | 重新產生實體裝置上，然後以新的金鑰更新的虛擬裝置。           | 無法重新產生從虛擬裝置。 |


## <a name="prerequisites-for-the-virtual-device"></a>虛擬裝置的先決條件

下列各節說明您 StorSimple 虛擬裝置設定的先決條件。 之前部署虛擬裝置，請檢閱[使用虛擬裝置的安全性考量](storsimple-security.md#storsimple-virtual-device-security)。

#### <a name="azure-requirements"></a>Azure 需求

您佈建虛擬裝置之前，您需要進行下列準備 Azure 環境中︰

- [設定上 Azure 虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-portal.md)的虛擬裝置。 如果使用進階版儲存空間，您必須建立虛擬網路 Azure 支援進階版儲存空間的區域中。 [區域 8020 目前支援的](#supported-regions-for-8020)詳細資訊。
- 建議使用預設的 DNS 伺服器 Azure 提供，而不是指定您自己的 DNS 伺服器名稱。 如果您的 DNS 伺服器名稱不正確，或建立虛擬裝置的 DNS 伺服器無法正確解析 IP 位址，如果將會失敗。
- 點-網站和網站的網站是選擇性的但並非必要。 如果您想要您可以設定這些選項的 [更多進階的案例。 
- 可以使用公開虛擬裝置的區虛擬網路中，您可以建立[Azure 虛擬機器](../virtual-machines/virtual-machines-linux-about.md)（主機伺服器）。 這些伺服器必須符合下列需求︰                            
    - 是 iSCSI 啟動器軟體安裝 Windows 或 Linux Vm。
    - 執行相同的虛擬網路虛擬裝置。
    - 無法連線至 iSCSI 目標的虛擬裝置的內部 IP 位址透過虛擬裝置。

- 請確定您已設定支援 iSCSI 和雲端流量相同的虛擬網路上。


#### <a name="storsimple-requirements"></a>StorSimple 需求

建立虛擬裝置前，請進行下列更新 Azure StorSimple 服務︰


- 新增要成為虛擬裝置伺服器 Vm[存取控制記錄](storsimple-manage-acrs.md)。

- 在相同的虛擬裝置地區使用[儲存的帳戶](storsimple-manage-storage-accounts.md#add-a-storage-account)。 在不同區域內的儲存空間帳戶可能會導致效能不佳。 您可以使用 [標準] 或 [進階版儲存帳戶與虛擬裝置。 如何建立[標準儲存帳戶]()或[進階版儲存帳戶](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)的詳細資訊

- 為您的資料時所用的虛擬裝置建立使用不同的儲存的帳戶。 使用相同的儲存空間帳戶，可能會導致效能不佳。

請確定您在開始之前有下列資訊︰

- 您 Azure 傳統入口網站的帳戶以存取認證。

- 從您的實體裝置服務資料加密金鑰複本。


## <a name="create-and-configure-the-virtual-device"></a>建立及設定虛擬裝置

之前執行這些程序，請確定您有符合[虛擬裝置的必要條件](#prerequisites-for-the-virtual-device)。 

建立虛擬網路、 設定 StorSimple 管理員服務，並向您的實體 StorSimple 裝置註冊服務之後，您可以使用下列步驟來建立和設定 StorSimple 虛擬裝置。 

### <a name="step-1-create-a-virtual-device"></a>步驟 1︰ 建立虛擬裝置

執行下列步驟，以建立 StorSimple 虛擬裝置。

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

如果建立的虛擬裝置無法在此步驟中，您可能沒有連線至網際網路。 如需詳細資訊，請移至[疑難排解網際網路連線失敗](#troubleshoot-internet-connectivity-errors)時 creatig 虛擬裝置。


### <a name="step-2-configure-and-register-the-virtual-device"></a>步驟 2︰ 設定和註冊虛擬裝置

之前開始此程序，請確定您有一份服務資料加密金鑰。 當您設定您的第一個 StorSimple 裝置，且您被指示要將其儲存在安全的位置建立服務資料加密金鑰。 如果您沒有服務資料加密金鑰複本，您必須連絡 Microsoft 支援服務取得協助。

執行下列步驟，以設定和註冊 StorSimple 虛擬裝置。
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>步驟 3: （選擇性） 修改裝置設定的設定

下一節說明裝置設定的設定所需的 StorSimple 虛擬裝置，如果您想要使用 CHAP，StorSimple 快照管理員或變更裝置系統管理員的密碼。

#### <a name="configure-the-chap-initiator"></a>設定 CHAP 啟動器

這個參數會包含從啟動器 （伺服器） 嘗試存取區預期的虛擬裝置 （目標） 的認證。 CHAP 使用者名稱和 CHAP 密碼確認其身分識別期間此驗證您的裝置，其中提供的啟動器。 如需詳細步驟，移至[您的裝置設定 CHAP](storsimple-configure-chap.md#unidirectional-or-one-way-authentication)。

#### <a name="configure-the-chap-target"></a>設定 CHAP 目標

這個參數會包含時 CHAP 啟用的啟動器要求相互或雙向驗證您的虛擬裝置所使用的認證。 虛擬裝置會使用反向 CHAP 使用者名稱和反向 CHAP 密碼給啟動器辨識此驗證程序期間。 請注意，CHAP 目標設定通用設定。 這些會套用，連線到儲存虛擬裝置的所有磁碟區會使用 CHAP 驗證。 如需詳細步驟，移至[您的裝置設定 CHAP](storsimple-configure-chap.md#bidirectional-or-mutual-authentication)。

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>設定 StorSimple 快照管理員密碼

StorSimple 快照管理員軟體位於您的 Windows 主機，並可讓系統管理員來管理 StorSimple 裝置在表單中的本機的備份與雲端快照集。

>[AZURE.NOTE] 虛擬裝置，您的 Windows 主機是 Azure 虛擬機器。

在設定裝置 StorSimple 快照管理員中，將會提示您提供 StorSimple 裝置的 IP 位址與密碼以驗證您的儲存裝置。 如需詳細步驟，前往 [[設定 StorSimple 快照管理員密碼](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)。

#### <a name="change-the-device-administrator-password"></a>變更裝置系統管理員的密碼 

當您使用 Windows PowerShell 介面存取虛擬裝置時，您都必須輸入裝置系統管理員的密碼。 如需資料的安全性，您所需的虛擬裝置可供使用前變更密碼。 如需詳細步驟，前往 [[設定裝置系統管理員的密碼](storsimple-change-passwords.md#change-the-device-administrator-password)。

## <a name="connect-remotely-to-the-virtual-device"></a>遠端連接至虛擬裝置
根據預設，不會啟用遠端存取您的虛擬裝置，透過 Windows PowerShell 介面。 您需要啟用遠端管理虛擬裝置上的第一次，並啟用它將會用來存取您的虛擬裝置在用戶端上。

下面會從遠端連接的兩個步驟程序。

### <a name="step-1-configure-remote-management"></a>步驟 1︰ 將遠端管理

執行下列步驟，以設定 [遠端管理 StorSimple 虛擬裝置。

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>步驟 2︰ 從遠端存取的虛擬裝置

您已啟用遠端管理 StorSimple 裝置設定] 頁面上的之後，您可以使用 Windows PowerShell 遠端從另一個虛擬機器內相同的虛擬網路; 連線至虛擬裝置例如，您可以從主應用程式的設定，且用來連線 iSCSI VM 連線。 在大部分的部署中，您將已開啟公用的結束點，以存取您的主機 VM 您可以用來存取虛擬裝置。

>[AZURE.WARNING] **增強的安全性，我們強烈建議您使用 HTTPS 的端點連線時，並完成之後，您遠端 PowerShell 工作階段，然後刪除端點。**

您應該遵循設定遠端虛擬裝置[連線至您的 StorSimple 裝置遠端](storsimple-remote-connect.md)程序。

## <a name="connect-directly-to-the-virtual-device"></a>直接連接到虛擬裝置

您也可以直接對虛擬裝置連線。 如果您想要直接連接到虛擬裝置從外部虛擬網路或外部的 Microsoft Azure 環境的另一部電腦，您需要建立其他端點，下列程序中所述。 

執行下列步驟，以建立虛擬裝置上的 [公用結束點。

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

我們建議您連線從另一個虛擬機器內相同的虛擬網路，因為此作法最小化公用虛擬網路上的結束點的數目。 當您使用此方法時，您只要連接至虛擬機器透過遠端桌面工作階段，並一樣在本機的網路上其他 Windows 用戶端，然後設定使用該虛擬機器。 您不需要附加的公用連接埠號碼，因為已經為已知的連接埠。

## <a name="work-with-the-storsimple-virtual-device"></a>使用 StorSimple 虛擬裝置

現在，您建立並設定 StorSimple 虛擬裝置，您準備好開始使用它。 您可以使用大量容器、 磁碟區及備份原則上的虛擬裝置在實體 StorSimple 裝置; 一樣唯一的差異，是您需要請確定從您的裝置清單中選取虛擬裝置。 請參閱[使用 StorSimple 管理員服務管理虛擬裝置](storsimple-manager-service-administration.md)的各種管理工作的虛擬裝置的逐步程序。

下列各節討論使用虛擬裝置時，會遇到的差異。

### <a name="maintain-a-storsimple-virtual-device"></a>維護 StorSimple 虛擬裝置

因為它是軟體專用的裝置，虛擬裝置進行的維修作業是最小的相較於實體裝置進行的維修作業。 您有下列選項︰

- **軟體更新**– 您可以檢視軟體上次更新的日期，以及任何更新的狀態訊息。 執行手動掃描，如果您想要檢查新的更新，您可以在頁面底部的使用**掃描更新**] 按鈕。 如果有可用的更新，請按一下 [安裝的**安裝更新**]。 因為虛擬裝置上只有一個介面，這表示會有稍微服務中斷時就會套用更新。 虛擬裝置會關閉，重新啟動 （如有需要）] 以套用任何已發行的更新。 逐步程序，請移至[更新您的裝置](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)。
- **支援套件**-您可以建立並上傳支援套件，以協助疑難排解問題與虛擬裝置的 Microsoft 支援服務。 逐步程序，請移至[建立及管理支援套件](storsimple-create-manage-support-package.md)。

### <a name="storage-accounts-for-a-virtual-device"></a>儲存帳戶的虛擬裝置

儲存帳戶建立用於 StorSimple 管理員服務、 虛擬裝置，和實際的裝置。 建立您儲存的帳戶後，我們建議的易記的名稱中使用地區識別項，以確保區域是一致的所有系統元件。 虛擬裝置，很重要的所有元件位於相同的區域，以防止效能問題。

逐步程序，請移至[[新增儲存空間帳戶]](storsimple-manage-storage-accounts.md#add-a-storage-account)。

### <a name="deactivate-a-storsimple-virtual-device"></a>停用 StorSimple 虛擬裝置

停用虛擬裝置刪除 VM 時已佈建後所建立的資源。 虛擬的裝置已停用 」 之後，就無法還原成先前的狀態中。 當您停用虛擬裝置之前，務必以停止或刪除用戶端和相依於該主機。

停用虛擬裝置會造成下列動作︰

- 虛擬裝置會移除。

- 移除 OS 磁碟與建立虛擬裝置的相關資料磁碟。

- 保留裝載的服務，提供時所建立的虛擬網路。 如果您不使用它們，您必須手動刪除。

- 建立虛擬裝置的雲端快照會保留。

如的逐步程序，請移至[停用，並刪除 StorSimple 裝置](storsimple-deactivate-and-delete-device.md)。

一旦虛擬裝置會顯示已停用 StorSimple 管理員的 [服務] 頁面上，您可以刪除虛擬裝置從裝置清單，在 [**裝置**] 頁面上。


### <a name="start-stop-and-restart-a-virtual-device"></a>啟動、 停止然後再重新虛擬裝置
不同於 StorSimple 實體裝置，沒有 power 開啟或關閉推入 StorSimple 虛擬裝置上的按鈕 power。 不過，可能會有時您要停止然後再重新虛擬裝置。 例如，某些更新可能會要求 VM 會重新啟動完成更新程序。 您可以開始的最簡單方法，停駐點，並重新啟動虛擬裝置是使用虛擬機器管理主控台。

當您檢視管理主控台時，虛擬裝置狀態是**執行**因為建立後，依預設開始使用。 您可以啟動、 停止，並在任何時間重新啟動虛擬機器。

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>重設為原廠預設值

如果您決定您只想要與虛擬裝置從頭開始，只要停用它刪除，然後建立一個新。 就像時重設您的實體裝置，您新增的虛擬裝置並不會安裝; 任何更新因此，請務必使用前更新。


## <a name="fail-over-to-the-virtual-device"></a>無法透過虛擬裝置

損毀修復 (DR) 是一種 StorSimple 虛擬裝置專為重要案例。 在此案例的實體 StorSimple 裝置或整個資料中心可能無法使用。 所幸，您可以使用虛擬裝置還原另一個位置中的作業。 期間 DR，從來源裝置音量容器變更擁有權，並會傳送到虛擬的裝置。 DR 的必要條件是的虛擬的裝置已建立及設定、 大量容器內的所有磁碟區已被離線大量容器具有相關聯雲端快照。

>[AZURE.NOTE] 
>
> - 使用時虛擬裝置做為次要裝置的 DR，請記住，8010 有 30 TB 的標準的儲存空間，而 8020 具有 64 TB 進階版儲存空間。 較高的容量 8020 虛擬裝置可能更適合 DR 案例。
> - 您無法容錯移轉或從裝置執行更新 2 到裝置執行更新前 1 軟體複本。 您可以不過容錯裝置執行更新 2 到裝置執行更新 1 （1.1 （英文） 或 1.2）

逐步程序，請移至[容錯移轉至虛擬裝置](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device)。
 

## <a name="shut-down-or-delete-the-virtual-device"></a>關閉或刪除虛擬裝置

如果您先前設定，並使用的 StorSimple，但現在想要停止累算其用於計算費用的虛擬裝置，您可以關閉虛擬裝置。 關閉虛擬裝置並不會刪除其作業系統或資料的儲存空間的磁碟。 它會停止在您的訂閱累算的費用，但會繼續儲存費用 OS 和資料磁碟。

如果您刪除，或關閉虛擬裝置，請在 StorSimple 管理員服務的 [裝置] 頁面會顯示為**離線**。 您可以選擇要停用或刪除裝置，如果您也想要刪除的虛擬裝置所建立的備份。 如需詳細資訊，請參閱[停用和刪除 StorSimple 裝置](storsimple-deactivate-and-delete-device.md)。

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>疑難排解網際網路連線的錯誤 

在虛擬裝置建立時，如果沒有連線至網際網路，建立步驟會失敗。 若要疑難排解如果失敗是因為網際網路連線，請在 Azure 傳統入口網站執行下列步驟︰

1. 建立 Windows server 2012 虛擬機器中 Azure。 此虛擬機器應該使用相同的儲存空間帳戶、 VNet 和子網路時使用的虛擬裝置。 如果您已使用相同的儲存空間帳戶、 Vnet 和子網路 Azure 中現有的 Windows Server 主機，您也可以使用它來疑難排解網際網路連線。
2. 遠端到虛擬機器在上述步驟中建立的記錄。 
3. 開啟命令視窗內虛擬機器 (Win + R，然後輸入`cmd`)。
4. 出現提示時，執行下列命令。

    `nslookup windows.net`

5. 如果`nslookup`失敗，然後網際網路連線失敗禁止虛擬裝置註冊 StorSimple 管理員服務。 
6. 進行必要的變更，以確保虛擬裝置存取 Azure 的網站，例如 「 windows.net 」 虛擬網路。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 StorSimple 管理員服務管理虛擬裝置](storsimple-manager-service-administration.md)。
 
- 瞭解如何[還原備份從 StorSimple 音量](storsimple-restore-from-backup-set.md)。 

