<properties 
   pageTitle="部署 StorSimple 裝置 (更新 2) |Microsoft Azure"
   description="將說明的步驟及部署 StorSimple 更新 2 裝置和服務的最佳作法。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="deploy-your-on-premises-storsimple-device-update-2"></a>部署您的內部部署 StorSimple 裝置 (更新 2)

> [AZURE.SELECTOR]
- [更新 2 及更新版本](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [更新 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA 發行](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>概觀

歡迎使用 Microsoft Azure StorSimple 裝置部署。 這些部署教學課程套用至 StorSimple 8000 數列更新 2。 這一系列的教學課程包含設定檢查清單、 設定先決條件和 StorSimple 裝置適用的詳細的設定步驟。

這些教學課程中的資訊假設您已經檢閱安全預防措施和解壓縮、 racked，並妥 StorSimple 裝置。 如果您仍然需要執行這些工作，開始檢閱[安全預防措施](storsimple-safety.md)。 請遵循裝置特定指示解壓縮和建立機架連接，纜線您的裝置。

- [解壓縮和建立機架連接，纜線您 8100](storsimple-8100-hardware-installation.md)
- [解壓縮和建立機架連接，纜線您從 8600](storsimple-8600-hardware-installation.md)

您必須完成安裝和設定程序的系統管理員權限。 我們建議您在開始之前檢閱設定檢查清單。 部署及設定程序可能需要一些時間才能完成。

> [AZURE.NOTE] StorSimple 8000 數列裝置只適用於發佈 Microsoft Azure 網站上的 StorSimple 部署資訊。 完成 7000 數列和裝置有關的詳細資訊，請移至︰ [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)。 7000 數列部署資訊，請參閱[StorSimple 系統快速入門指南](http://onlinehelp.storsimple.com/111_Appliance/)。

## <a name="deployment-steps"></a>部署步驟

執行所需的步驟設定您的 StorSimple 裝置，並將其連線至您 StorSimple 管理員服務。 除了所需的步驟，還有選用步驟，您可能需要在部署期間的程序。 您應該執行這些選用步驟時，就會指出部署逐步指示。


| 步驟                                                                                   | 描述                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **必要條件**                                                                      | 這些需要準備進行即將來臨的部署完成。                                                                                        |
| [部署設定檢查清單](#deployment-configuration-checklist)                                                     | 用於收集和記錄之前，以及在部署期間資訊這份檢查清單。                                                                       |
| [部署先決條件](#deployment-prerequisites)                                                               | 這些驗證環境準備好進行部署。                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **逐步部署**                                                                   | 這些步驟，才能部署生產 StorSimple 裝置。                                                                                      |
| [步驟 1︰ 建立新的服務](#step-1-create-a-new-service)                                                         | 設定雲端管理及 StorSimple 裝置的儲存空間。 *略過此步驟，如果您有其他 StorSimple 裝置現有的服務*。                |
| [步驟 2︰ 取得服務登錄機碼](#step-2-get-the-service-registration-key)                                               | 使用此鍵可註冊及管理服務連線 StorSimple 裝置。                                                                         |
| [步驟 3︰ 設定，並透過 Windows PowerShell 的裝置註冊 StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)    | 將裝置連接到您的網路和註冊 Azure 以完成使用管理服務的設定。                                            |
| [步驟 4︰ 完成的最小值的裝置設定](#step-4-complete-minimum-device-setupd)</br>[可省略︰ 更新您的 StorSimple 裝置](#scan-for-and-apply-updates)      | 您可以使用 [管理服務來完成裝置設定，並讓它提供儲存空間。                                                                      |
| [步驟 5︰ 建立大量容器](#step-5-create-a-volume-container)                                                      | 建立佈建區容器。 大量容器具有儲存帳戶、 頻寬和加密設定中所包含的所有磁碟區。    |
| [步驟 6︰ 建立區](#step-6-create-a-volume)                                                                | 佈建 StorSimple 裝置伺服器上的存放區。                                                                                        |
| [步驟 7︰ 裝載、 初始化以及設定格式的音量](#step-7-mount-initialize-and-format-a-volume)</br>[可省略︰ 設定 MPIO](storsimple-configure-mpio-windows-server.md)            | 您的伺服器連線到裝置所提供的 iSCSI 儲存空間。 您也可以選擇設定 MPIO，以確保您的伺服器可容許連結]、 [網路和介面失敗。                                                                                                                                                              |
| [步驟 8︰ 採取備份](#step-8-take-a-backup)                                                                  | 將您的備份原則設定為保護您的資料                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **其他程序**                                                                   | 您可能需要將您的解決方案部署參考這些程序。                                                                                        |
| [設定新的儲存空間帳戶的服務](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [使用 PuTTY 連線至裝置循序主控台](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [取得 IQN 的 Windows Server host （主機）](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [建立手動備份](#create-a-manual-backup)                                                                 | 


## <a name="deployment-configuration-checklist"></a>部署設定檢查清單

在部署您的裝置之前，您必須收集 StorSimple 裝置上設定軟體的資訊。 準備部分提前這項資訊可協助簡化部署 StorSimple 裝置，您的環境中的程序。 下載並使用這份檢查清單記下設定的詳細資訊，將部署您的裝置。

- [下載 StorSimple 部署設定檢查清單](http://www.microsoft.com/download/details.aspx?id=49159)


## <a name="deployment-prerequisites"></a>部署先決條件

下列各節說明您 StorSimple 管理員服務及 StorSimple 裝置設定的先決條件。

### <a name="for-the-storsimple-manager-service"></a>如需 StorSimple 管理員服務

在開始之前，請確定︰

- 您有 Microsoft 帳戶使用 access 的認證。

- 您有使用 access 的認證您 Microsoft Azure 儲存體帳戶。

- 您的 Microsoft Azure 訂閱已啟用的 StorSimple 管理員服務。 訂閱應該購買[Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/)。

- 您可以存取，例如 PuTTY 終端機模擬軟體。

### <a name="for-the-device-in-the-datacenter"></a>在資料中心的裝置

之前設定裝置，請先確認您的裝置是完整解壓縮、 裝載機架和完整妥 power、 網路和序列存取所述︰

-  [解壓縮和建立機架連接，纜線 8100 裝置](storsimple-8100-hardware-installation.md)
-  [解壓縮和建立機架連接，纜線從 8600 裝置](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>在資料中心的網路

在開始之前，請確定︰

- 在您的資料中心的防火牆連接埠會開啟至允許 iSCSI 與雲端流量[網路需求 StorSimple 裝置](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)所述。


## <a name="step-by-step-deployment"></a>逐步部署

您可以使用下列的逐步指示部署 StorSimple 裝置中的資料中心。

## <a name="step-1-create-a-new-service"></a>步驟 1︰ 建立新的服務

StorSimple 管理員服務可以管理多個 StorSimple 裝置。 執行下列步驟，以建立新的 StorSimple 管理員服務的執行個體。

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] 如果您沒有啟用自動建立儲存帳戶的服務，您必須建立一個以上的儲存空間帳戶之後您已成功建立服務。 當您建立大量容器，則使用此儲存帳戶。 
>
> * 如果您未自動建立儲存帳戶，請移至[設定服務的新儲存的帳戶](#configure-a-new-storage-account-for-the-service)的詳細指示。 
> * 如果您啟用自動建立的儲存空間帳戶，請移至[步驟 2︰ 取得服務登錄機碼](#step-2-get-the-service-registration-key)。

## <a name="step-2-get-the-service-registration-key"></a>步驟 2︰ 取得服務登錄機碼

StorSimple 管理員服務和使用之後，您必須取得服務登錄機碼。 這個索引鍵用來註冊，並使用服務連線到您 StorSimple 的裝置。

在 [管理] 入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>步驟 3︰ 設定，並透過 Windows PowerShell 的裝置註冊 StorSimple

使用 Windows PowerShell 的 StorSimple 完成初始 StorSimple 裝置的設定，如下列程序所述。 您必須使用終端機模擬軟體來完成這個步驟。 如需詳細資訊，請參閱[連線至裝置循序主控台使用 PuTTY](#use-putty-to-connect-to-the-device-serial-console)。

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>步驟 4︰ 完成的最小值的裝置設定

StorSimple 裝置的最小值的裝置設定，您會需要︰ 

- 設定次要 DNS 伺服器。
- 啟用 iSCSI 至少有一個網路介面上。
- 指定固定的控制站的 IP 位址。

在 [管理] 入口網站，以完成的最小值的裝置設定，請執行下列步驟。

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>步驟 5︰ 建立大量容器

大量容器具有儲存帳戶、 頻寬和加密設定中所包含的所有磁碟區。 您必須建立大量容器，才能開始佈建 StorSimple 裝置上的區。 

若要建立大量容器管理入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>步驟 6︰ 建立區

建立大量容器之後，您就可以針對您的伺服器提供 StorSimple 裝置上的存放區。 若要建立大量管理入口網站中，執行下列步驟。

> [AZURE.IMPORTANT] StorSimple 管理員可以建立兩型和完整能夠的區。 不過，您無法建立部分能夠的區。 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>步驟 7︰ 裝載、 初始化以及設定格式的音量

在您的 Windows Server 主機上執行下列步驟。 


> [AZURE.IMPORTANT]

> - 為 StorSimple 解決方案的可用性，我們建議您之前設定 iSCSI （選用） 您 host （主機） 伺服器上設定 MPIO。 Host （主機） 伺服器上的 MPIO 設定可確保伺服器的可容許的連結、 網路或介面失敗。

> - MPIO 及 iSCSI 安裝和設定指示 Windows Server host （主機），請移至 [[設定 MPIO StorSimple 裝置](storsimple-configure-mpio-windows-server.md)。 這些也會包含裝載、 初始化及格式化 StorSimple 區的步驟。

> - 如需 MPIO 及 iSCSI 安裝和設定指示 Linux 主機上，前往 [[設定 MPIO StorSimple Linux 主機的](storsimple-configure-mpio-on-linux.md)

如果您決定不要設定 MPIO，請執行下列步驟來裝載、 初始化以及格式化 StorSimple 區上的 Windows Server 主機。

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>步驟 8︰ 採取備份

備份提供時間點保護的區，並改善修復能力時最小化還原時間。 您可以採取兩種類型的備份 StorSimple 裝置上︰ 本機快照和雲端快照集。 每個備份類型可以**排程**或**手動**。 

若要建立排程的備份管理入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

您可以執行手動備份隨時。 程序，請移至[建立手動備份](#create-a-manual-backup)。 

## <a name="configure-a-new-storage-account-for-the-service"></a>設定新的儲存空間帳戶的服務

這是選擇性的步驟，您需要執行只有當您未啟用自動建立儲存帳戶的服務。 Microsoft Azure 儲存體帳戶，才能建立 StorSimple 大量容器。

如果您需要在不同區域中建立 Azure 儲存體帳戶，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)，如需逐步指示。

在 [ **StorSimple 管理員服務**] 頁面上的 [管理] 入口網站，請執行下列步驟。

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>使用 PuTTY 連線至裝置循序主控台

若要連線到 Windows PowerShell 用 StorSimple，您需要使用終端機模擬軟體，例如 PuTTY。 當您存取裝置，透過序列主控台或直接從遠端電腦開啟 telnet 工作階段時，您可以使用 PuTTY。

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## <a name="scan-for-and-apply-updates"></a>掃描並套用更新

更新您的裝置，可能需要幾個小時。 執行下列步驟，以掃描，並將更新套用至您的裝置。
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>更新您的裝置

1.  在 [裝置**快速入門**] 頁面上按一下 [**裝置**]。 選取的實體裝置，按一下**進行的維修作業**，然後按一下 [**掃描的更新**。  

2.  建立工作掃描可用的更新。 如果有可用的更新，請**瀏覽更新**改成**安裝更新**。 按一下 [**安裝更新**]。 

3.  將會建立更新工作。 瀏覽至**工作**監控您的更新的狀態。

    > [AZURE.NOTE] 更新工作啟動時，會立即顯示狀態為 50%。 只有在更新工作已經完成後，狀態會變更為 100%。 有更新程序沒有即時狀態。

4.  裝置已成功更新後，啟用已停用這些資料 2 和 3 的資料的網路介面。

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>取得 IQN 的 Windows Server host （主機）

執行下列步驟，以取得 iSCSI 完整名稱 (IQN) 的 Windows 主機正在執行 Windows Server® 2012年。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>建立手動備份

若要建立單一區指定手動備份 StorSimple 裝置上管理入口網站中，執行下列步驟。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## <a name="next-steps"></a>後續步驟

- 設定[虛擬裝置](storsimple-virtual-device-u2.md)。

- 若要管理您的 StorSimple 裝置使用[StorSimple 管理員服務](storsimple-manager-service-administration.md)。
 
