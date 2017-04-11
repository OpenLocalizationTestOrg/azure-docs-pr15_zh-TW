<properties 
   pageTitle="部署政府版入口網站中的 StorSimple 裝置 |Microsoft Azure"
   description="將說明的步驟及部署 StorSimple 更新 1 裝置和 Azure 政府版入口網站中的服務的最佳作法。"
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
   ms.workload="NA"
   ms.date="06/17/2016"
   ms.author="v-sharos" />

# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>部署內部部署 StorSimple 裝置政府版入口網站中

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## <a name="overview"></a>概觀

歡迎使用 Microsoft Azure StorSimple 裝置部署。 這些部署教學課程套用 StorSimple 8000 數列 Azure 政府版入口網站中執行更新 1 軟體。 這一系列的教學課程說明如何設定 StorSimple 裝置，其中包含設定檢查清單、 設定先決條件和詳細的設定步驟。

這些教學課程中的資訊假設您已經檢閱安全預防措施和解壓縮、 racked，並妥 StorSimple 裝置。 如果您仍然需要執行這些工作，開始檢閱[安全預防措施](storsimple-safety.md)。 根據您的裝置型號，您可以再解壓縮，機架裝載及纜線，按照中的指示︰

- [解壓縮和建立機架連接，纜線您 8100](storsimple-8100-hardware-installation.md)
- [解壓縮和建立機架連接，纜線您從 8600](storsimple-8600-hardware-installation.md)

您必須完成安裝和設定程序的系統管理員權限。 我們建議您在開始之前檢閱設定檢查清單。 部署及設定程序可能需要一些時間才能完成。

> [AZURE.NOTE] StorSimple 8000 數列裝置只適用於發佈 Microsoft Azure 網站上的 StorSimple 部署資訊。 完成 7000 數列和裝置有關的詳細資訊，請移至︰ [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)。 7000 數列部署資訊，請參閱[StorSimple 系統快速入門指南](http://onlinehelp.storsimple.com/111_Appliance/)。

## <a name="deployment-steps"></a>部署步驟

執行所需的步驟設定您的 StorSimple 裝置，並將其連線至您 StorSimple 管理員服務。 除了所需的步驟，還有選用步驟，您可能需要在部署期間的程序。 您應該執行這些選用步驟時，就會指出部署逐步指示。


| 步驟                                                                                   | 描述                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **必要條件**                                                                      | 這些需要準備進行即將來臨的部署完成。                                                                                        |
| 部署設定檢查清單。                                                     | 用於收集和記錄之前，以及在部署期間資訊這份檢查清單。                                                                       |
| 部署的先決條件。                                                               | 這些驗證環境準備好進行部署。                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **逐步部署**                                                                   | 這些步驟，才能部署生產 StorSimple 裝置。                                                                                      |
| 步驟 1︰ 建立新的服務。                                                         | 設定雲端管理及 StorSimple 裝置的儲存空間。 如果您有其他 StorSimple 裝置現有的服務，請略過此步驟。                |
| 步驟 2︰ 取得服務登錄機碼。                                               | 使用此鍵可註冊及管理服務連線 StorSimple 裝置。                                                                         |
| 步驟 3︰ 設定，並透過 Windows PowerShell 的裝置註冊 StorSimple。    | 將裝置連接到您的網路和註冊 Azure 以完成使用管理服務的設定。                                            |
| 步驟 4︰ 完成的最小值的裝置設定</br>可省略︰ 更新您的 StorSimple 裝置。      | 您可以使用 [管理服務來完成裝置設定，並讓它提供儲存空間。                                                                      |
| 步驟 5︰ 建立大量容器。                                                      | 建立佈建區容器。 大量容器具有儲存帳戶、 頻寬和加密設定中所包含的所有磁碟區。    |
| 步驟 6︰ 建立區。                                                                | 佈建 StorSimple 裝置伺服器上的存放區。                                                                                        |
| 步驟 7︰ 裝載、 初始化，以及設定格式的音量。</br>可省略︰ 設定 MPIO。            | 您的伺服器連線到裝置所提供的 iSCSI 儲存空間。 您也可以選擇設定 MPIO，以確保您的伺服器的可容許連結、 網路和介面失敗。                                                                                                                                                              |
| 步驟 8︰ 需要備份。                                                                  | 將您的備份原則設定為保護您的資料                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **其他程序**                                                                   | 您可能需要將您的解決方案部署參考這些程序。                                                                                        |
| 設定服務的新儲存的帳戶。                                      |                                                                                                                                                               |
| 使用 PuTTY 連線至裝置循序主控台。                                    |                                                                                                                                                               |
| 掃描並套用更新。                                                   |                                                                                                                                                               |
| 取得 IQN 的 Windows Server host （主機）。                                                   |                                                                                                                                                               |
| 建立手動備份。                                                                 | 
| 設定 MPIO。                                                                          |


## <a name="deployment-configuration-checklist"></a>部署設定檢查清單

下列部署設定檢查清單將說明您需要先與 StorSimple 裝置上設定軟體收集的資訊。 準備部分提前這項資訊可協助簡化部署 StorSimple 裝置，您的環境中的程序。 您可以使用這份檢查清單，也記下設定的詳細資訊將部署您的裝置。

| 階段                                  | 參數                                         | 詳細資料                                                                                                                                                                | 值 |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **纜線您的裝置**                      | 序列存取                                     | 初始的裝置設定                                                                  | 是/否 |
|   |   |  |  |
| **設定和註冊裝置**          | 資料 0 網路設定                           | 資料 0 IP 位址︰</br>子網路遮罩︰</br>閘道器︰</br>主要 DNS 伺服器︰</br>主要 NTP 伺服器︰</br>Web proxy 伺服器 IP/FQDN （選用）︰</br>Web proxy 連接埠︰|        |
|                                        | 裝置系統管理員密碼                     | 密碼必須包含小寫、 大寫、 數字和特殊字元的 8 及 15 個字元之間。 |        |
|                                        | StorSimple 快照管理員密碼              | 密碼必須包含小寫、 大寫、 數字和特殊字元 14 或 15 個字元。|        |
|                                        | 服務登錄機碼                          | 從 Azure 入口網站，就會產生此按鍵。    |        |
|                                        | 服務資料加密金鑰                       | 裝置已透過 Windows PowerShell 管理服務註冊 StorSimple 時，會建立此金鑰。 複製此鍵，並將其儲存在安全的位置。|  |
|   |   |  |  |
| **完成的最小裝置設定**          | 您的裝置易記的名稱                     | 這是裝置的描述性名稱。 |        |
|                                        | 時區                                          | 您的裝置會使用這個時區所有排程作業。  |        |
|                                        | 次要 DNS 伺服器                              | 這是必要的設定。                                  |        |
|                                        | 網路介面︰ 資料 0 控制器固定 IPs                                     | 這些 IP 應該可路由傳送至網際網路。</br>控制站 0 固定 IP 位址︰</br>控制站 1 固定 IP 位址︰|
|   |   |  |  |
| **其他網路介面設定**  | 網路介面︰ 1 的資料</br>如果 iSCSI 啟用，不要設定閘道器。      | 目的︰ 雲端/iSCSI 無法使用</br>IP 位址︰</br>子網路遮罩︰</br>閘道器︰|
|                                        | 網路介面︰ 2 的資料</br>如果 iSCSI 啟用，不要設定閘道器。      | 目的︰ 雲端/iSCSI 無法使用</br>IP 位址︰</br>子網路遮罩︰</br>閘道器︰|
|                                        | 網路介面︰ 3 的資料</br>如果 iSCSI 啟用，不要設定閘道器。      | 目的︰ 雲端/iSCSI 無法使用</br>IP 位址︰</br>子網路遮罩︰</br>閘道器︰|
|                                        | 網路介面︰ 資料 4</br>如果 iSCSI 啟用，不要設定閘道器。      | 目的︰ 雲端/iSCSI 無法使用</br>IP 位址︰</br>子網路遮罩︰</br>閘道器︰|
|                                        | 網路介面︰ 5 的資料</br>如果 iSCSI 啟用，不要設定閘道器。      | 目的︰ 雲端/iSCSI 無法使用</br>IP 位址︰</br>子網路遮罩︰</br>閘道器︰|
|   |   |  |  |
| **建立大量容器**                      | 大量容器名稱︰                            | 容器的名稱                                                                                                                                                 |        |
|                                        | Azure 儲存體帳戶︰                            | 儲存帳戶名稱與 access 鍵與此大量容器建立關聯                                                                                              |        |
|                                        | 雲端儲存空間加密金鑰︰                     | 每個容器中的儲存空間的加密金鑰                                                                                                                           |        |
|   |   |  |  |
| **建立區**                        | 每個大量的詳細資料                           | 大量名稱︰                                                                                                                                                           |        |
|                                        |                                                   | 大小︰                                                                                                                                                                  |        |
|                                        |                                                   | 使用類型︰                                                                                                                                                            |        |
|                                        |                                                   | ACR 名稱︰                                                                                                                                                              |        |
|                                        |                                                   | 預設備份原則︰                                                                                                                                                 |        |
|   |   |  |  |
| **裝載、 初始化以及設定格式的音量** | 每個主機伺服器連線到儲存空間的詳細資料 | Windows 伺服器名稱︰                                                                                                                                                   |        |
|                                        |                                                   | Windows Server IQN:                                                                                                                                                    |        |
|                                        |                                                   | Windows Server 大量名稱︰                                                                                                                                                   |        |
|                                        |                                                   | NTFS 連接點/磁碟機代號︰                                                                                                                                      |        |


## <a name="deployment-prerequisites"></a>部署先決條件

下列各節說明您 StorSimple 管理員服務及 StorSimple 裝置設定的先決條件。

### <a name="for-the-storsimple-manager-service"></a>如需 StorSimple 管理員服務

在開始之前，請確定︰

- 您有 Microsoft 帳戶使用 access 的認證。

- 您有使用 access 的認證您 Microsoft Azure 儲存體帳戶。

- 您的 Microsoft Azure 訂閱已啟用的 StorSimple 管理員服務。 訂閱應該購買[Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/)。

- 您可以存取，例如 PuTTY 終端機模擬軟體。

### <a name="for-the-device-in-the-datacenter"></a>在資料中心的裝置

設定之前的裝置，請確認︰

- 完全解壓縮、 裝載機架並中所述，完全妥 power、 網路和序列存取您的裝置︰

    -  [解壓縮和建立機架連接，纜線 8100 裝置](storsimple-8100-hardware-installation.md)
    -  [解壓縮和建立機架連接，纜線從 8600 裝置](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>在資料中心的網路

在開始之前，請確定︰

- 在您的資料中心的防火牆連接埠會開啟至允許 iSCSI 與雲端流量[網路需求 StorSimple 裝置](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)所述。

## <a name="step-by-step-deployment"></a>逐步部署

您可以使用下列的逐步指示部署 StorSimple 裝置中的資料中心。

## <a name="step-1-create-a-new-service"></a>步驟 1︰ 建立新的服務

StorSimple 管理員服務可以管理多個 StorSimple 裝置。 執行下列步驟，以建立新的 StorSimple 管理員服務的執行個體。

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT] 如果您沒有啟用自動建立儲存帳戶的服務，您必須建立一個以上的儲存空間帳戶之後您已成功建立服務。 當您建立大量容器，則使用此儲存帳戶。 
>
> * 如果您未自動建立儲存帳戶，請移至[設定服務的新儲存的帳戶](#configure-a-new-storage-account-for-the-service)的詳細指示。 
> * 如果您啟用自動建立的儲存空間帳戶，請移至[步驟 2︰ 取得服務登錄機碼](#step-2-get-the-service-registration-key)。

## <a name="step-2-get-the-service-registration-key"></a>步驟 2︰ 取得服務登錄機碼

StorSimple 管理員服務和使用之後，您必須取得服務登錄機碼。 此按鍵用於註冊和 StorSimple 裝置連線至服務。

政府版入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>步驟 3︰ 設定，並透過 Windows PowerShell 的裝置註冊 StorSimple

使用 Windows PowerShell 的 StorSimple 完成初始 StorSimple 裝置的設定，如下列程序所述。 您必須使用終端機模擬軟體來完成這個步驟。 如需詳細資訊，請參閱[連線至裝置循序主控台使用 PuTTY](#use-putty-to-connect-to-the-device-serial-console)。

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## <a name="step-4-complete-minimum-device-setup"></a>步驟 4︰ 完成的最小值的裝置設定

StorSimple 裝置的最小值的裝置設定，您會需要︰ 

- 設定次要 DNS 伺服器。
- 啟用 iSCSI 至少有一個網路介面上。
- 指定固定的控制站的 IP 位址。

請執行下列步驟以完成的最小值的裝置設定政府版入口網站中。

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>步驟 5︰ 建立大量容器

大量容器具有儲存帳戶、 頻寬和加密設定中所包含的所有磁碟區。 您必須建立大量容器，才能開始佈建 StorSimple 裝置上的區。 

若要建立大量容器政府版入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>步驟 6︰ 建立區

建立大量容器之後，您就可以針對您的伺服器提供 StorSimple 裝置上的存放區。 若要建立大量政府版入口網站中，執行下列步驟。

> [AZURE.IMPORTANT] Azure StorSimple 可以建立唯一的狗熊能夠的區。  您無法建立能夠完全或部分能夠 Azure StorSimple 系統上的區。 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>步驟 7︰ 裝載、 初始化以及設定格式的音量

在您的 Windows Server 主機上執行這些步驟。

> [AZURE.IMPORTANT]

> - 為 StorSimple 解決方案的可用性，我們建議您之前設定 iSCSI （選用） 您 host （主機） 伺服器上設定 MPIO。 Host （主機） 伺服器上的 MPIO 設定可確保伺服器的可容許的連結、 網路或介面失敗。

> - MPIO 及 iSCSI 安裝和設定指示 Windows Server host （主機），請移至 [[設定 MPIO StorSimple 裝置](storsimple-configure-mpio-windows-server.md)。 這些也會包含裝載、 初始化及格式化 StorSimple 區的步驟。

> - 如需 MPIO 及 iSCSI 安裝和設定指示 Linux 主機上，前往 [[設定 MPIO StorSimple Linux 主機的](storsimple-configure-mpio-on-linux.md)

如果您決定不要設定 MPIO，請執行下列步驟來裝載、 初始化以及格式化 StorSimple 區上的 Windows Server 主機。

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>步驟 8︰ 採取備份

備份提供時間點保護的區，並改善修復能力時最小化還原時間。 您可以採取兩種類型的備份 StorSimple 裝置上︰ 本機快照和雲端快照集。 每個備份類型可以**排程**或**手動**。 

若要建立排程的備份政府版入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

您可以執行手動備份隨時。 程序，請移至[建立手動備份](#create-a-manual-backup)。 

## <a name="configure-a-new-storage-account-for-the-service"></a>設定新的儲存空間帳戶的服務

這是選擇性的步驟，您需要執行只有當您未啟用自動建立儲存帳戶的服務。 Microsoft Azure 儲存體帳戶，才能建立 StorSimple 大量容器。

如果您需要在不同區域中建立 Azure 儲存體帳戶，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)，如需逐步指示。

在政府版入口網站**StorSimple 管理員服務**] 頁面上，執行下列步驟。

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>使用 PuTTY 連線至裝置循序主控台

若要連線到 Windows PowerShell 用 StorSimple，您需要使用終端機模擬軟體，例如 PuTTY。 當您存取裝置，透過序列主控台或直接從遠端電腦開啟 telnet 工作階段時，您可以使用 PuTTY。

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>掃描並套用更新

更新您的裝置，可能需要幾個小時。 執行下列步驟，以掃描，並將更新套用至您的裝置。

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>更新您的裝置

1.  在 [裝置**快速入門**] 頁面上按一下 [**裝置**]。 選取的實體裝置，按一下**進行的維修作業**，然後按一下 [**掃描的更新**。  
2.  建立工作掃描可用的更新。 如果有可用的更新，請**瀏覽更新**改成**安裝更新**。 按一下 [**安裝更新**]。 
3.  將會建立更新工作。 瀏覽至**工作**監控您的更新的狀態。

    > [AZURE.NOTE] 更新工作啟動時，會立即顯示狀態為 50%。 只有在更新工作已經完成後，狀態會變更為 100%。 有更新程序沒有即時狀態。

4.  裝置已成功更新後，啟用已停用這些資料 2 和 3 的資料的網路介面。

## <a name="get-the-iqn-of-a-windows-server-host"></a>取得 IQN 的 Windows Server host （主機）

執行下列步驟，以取得 iSCSI 完整名稱 (IQN) 的 Windows 主機正在執行 Windows Server® 2012年。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>建立手動備份

若要建立單一區指定手動備份 StorSimple 裝置上政府版入口網站中，執行下列步驟。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## <a name="configure-mpio"></a>設定 MPIO

多重路徑 I/O (MPIO) 是選擇性功能，並不會安裝在 Windows Server 上預設值。 您應該安裝做為功能透過伺服器管理員。 MPIO 安裝指示，請移至 [[設定 MPIO StorSimple 裝置](storsimple-configure-mpio-windows-server.md)。

MPIO StorSimple 裝置連接到 Linux 主機的安裝指示，請移至 [[設定 MPIO Linux 主機的](storsimple-configure-mpio-on-linux.md)。

> [AZURE.NOTE] StorSimple 虛擬裝置上不支援 MPIO。 

## <a name="next-steps"></a>後續步驟

- 設定[虛擬裝置](storsimple-virtual-device-u2.md)。

- 若要管理您的 StorSimple 裝置使用[StorSimple 管理員服務](https://msdn.microsoft.com/library/azure/dn772396.aspx)。
 
