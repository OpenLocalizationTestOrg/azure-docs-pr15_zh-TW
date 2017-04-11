<properties
   pageTitle="部署 StorSimple 虛擬陣列 1-入口網站的準備作業"
   description="若要部署 StorSimple 虛擬陣列的第一個教學課程包括入口網站"
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
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>部署 StorSimple 虛擬陣列-準備入口網站

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>概觀

本文適用於 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置） 執行 2016 年 3 月上市 (GA) 版本。 這是完全部署虛擬陣列，以在檔案伺服器或 iSCSI 伺服器所需的部署教學課程系列中第一份文件。 本文將說明建立及設定您的 StorSimple 管理員服務佈建的虛擬陣列之前所需的準備。 本文也會介紹出部署設定檢查清單，以及設定的先決條件。

您必須完成安裝和設定程序的系統管理員權限。 我們建議您在開始之前檢閱部署設定檢查清單。 入口網站準備會小於 10 分鐘的時間。

部署 StorSimple 虛擬陣列中 Azure 傳統入口網站，以及 Microsoft Azure 政府雲端適用於發佈本文中的資訊。

### <a name="get-started"></a>快速入門

部署工作流程包含準備入口網站與佈建在虛擬環境中，以虛擬陣列完成設定。 若要開始使用在檔案伺服器或 iSCSI 伺服器 StorSimple 虛擬陣列部署，您必須請參閱下列 tabulated 資源 （文章和視訊）。

#### <a name="deployment-articles"></a>部署文件

請參閱下列文章，並依指定順序部署 StorSimple 虛擬陣列。

| **#** | **在此步驟中**                          | **您將會執行動作**                                                         | **使用這些文件。**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1。   | **設定 Azure 傳統入口網站**       | 建立及設定您 StorSimple 管理員服務之前佈建 StorSimple 虛擬裝置。  |[準備入口網站](storsimple-ova-deploy1-portal-prep.md)|
|2。   | **佈建虛擬陣列**           | 如 HYPER-V，佈建與連線到 Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2 上執行 HYPER-V 主機系統上 StorSimple 虛擬裝置。 <br></br> <br></br> 如 VMware，佈建與連線至 StorSimple 內部部署虛擬裝置執行 VMware ESXi 5.5 主機系統上和上方。<br></br>| [佈建 HYPER-V 中的虛擬陣列](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [佈建中 VMware 的虛擬陣列](storsimple-ova-deploy2-provision-vmware.md)|
|3。    | **設定虛擬陣列**              | 為您的檔案伺服器執行初始設定，註冊您 StorSimple 檔案的伺服器，並完成裝置設定。 然後，您可以佈建 SMB 共用。 <br></br> <br></br> ISCSI 伺服器，執行初始設定，註冊 StorSimple iSCSI 伺服器，並完成裝置設定。 然後，您可以佈建 iSCSI 區。| [設定檔案伺服器的虛擬陣列](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[虛擬陣列 iSCSI 伺服器設定](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>部署影片

| **若要執行此步驟...** |  **觀看這段影片。**|
|----------------|-------------|
| 若要開始使用 StorSimple 虛擬陣列的逐步指示。 | [快速入門 StorSimple 虛擬陣列](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| 佈建 HYPER-V StorSimple 虛擬陣列的逐步指示。|[建立 StorSimple 虛擬陣列](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|若要設定和註冊 StorSimple 虛擬陣列的逐步指示|[設定以 StorSimple 虛擬陣列](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|若要建立共用的逐步指示備份共用，並還原 StorSimple 虛擬陣列設為在檔案伺服器上的資料|[使用 StorSimple 虛擬陣列](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|錯誤移轉及損毀復原 StorSimple 虛擬陣列的逐步指示|[StorSimple 虛擬陣列損毀修復](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

現在，您可以開始設定 Azure 傳統入口網站。

## <a name="configuration-checklist"></a>設定檢查清單

設定檢查清單將說明您需要 StorSimple 裝置上設定軟體之前收集的資訊。 準備提前這項資訊可協助簡化部署 StorSimple 裝置，您的環境中的程序。 是否將 StorSimple 虛擬裝置部署為在檔案伺服器或 iSCSI 伺服器而定，您將需要下列檢查清單的其中一個。

-   下載[StorSimple 虛擬陣列檔案伺服器設定檢查清單](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)。

-   下載[StorSimple 虛擬陣列 iSCSI 伺服器設定檢查清單](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)。

## <a name="prerequisites"></a>必要條件

這裡，您會發現您 StorSimple 管理員服務、 StorSimple 虛擬裝置和資料中心網路設定的先決條件。

### <a name="for-the-storsimple-manager-service"></a>如需 StorSimple 管理員服務

在開始之前，請確定︰

-   您有 Microsoft 帳戶使用 access 的認證。

-   您有使用 access 認證您 Microsoft Azure 儲存體帳戶。

-   您的 Microsoft Azure 訂閱應該啟用 StorSimple 管理員服務。

### <a name="for-the-storsimple-virtual-device"></a>StorSimple 虛擬裝置

部署虛擬裝置之前，請確定︰

-   您有權存取的主機系統執行 HYPER-V Windows Server 2008 R2 或更新版本或 VMware (ESXi 5.5 或更新版本) 可用於佈建裝置。

-   Host （主機） 系統是能夠所佈建虛擬裝置下列資源︰

    -   4 核心的最小值。

    -   至少 8 GB 的 RAM。

    -   網路介面。

    -   系統資料 500 GB 虛擬磁碟。

### <a name="for-the-datacenter-network"></a>為資料中心的網路

在開始之前，請確定︰

-   在您的資料中心的網路設定依照 StorSimple 裝置的網路需求。 如需詳細資訊，請參閱[StorSimple 虛擬陣列系統需求](storsimple-ova-system-requirements.md)。

-   您的 StorSimple 虛擬裝置有專用的 5 Mbps 的網際網路頻寬 （或更多） 隨時可用。 此頻寬不應該與其他應用程式共用。

## <a name="step-by-step-preparation"></a>逐步準備

使用下列的逐步指示來準備您的入口網站的 StorSimple 管理員服務。

## <a name="step-1-create-a-new-service"></a>步驟 1︰ 建立新的服務

單一服務的執行個體 StorSimple 管理員可以管理多個 StorSimple 1200 裝置。 執行下列步驟，以建立新的 StorSimple 管理員服務的執行個體。 如果您有現有的 StorSimple 管理員服務管理 1200年裝置，請略過此步驟，然後移到[遵循步驟 2︰ 取得服務登錄機碼](#step-2-get-the-service-registration-key)。

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> 如果您沒有啟用自動建立儲存帳戶的服務，您必須建立一個以上的儲存空間帳戶之後您已成功建立服務。
>

> - 如果您未自動建立儲存帳戶，請移至[設定服務的新儲存的帳戶](#optional-step-configure-a-new-storage-account-for-the-service)的詳細指示。
>

> - 如果您啟用自動建立的儲存空間帳戶，請移至[步驟 2︰ 取得服務登錄機碼](#step-2-get-the-service-registration-key)。


## <a name="step-2-get-the-service-registration-key"></a>步驟 2︰ 取得服務登錄機碼


StorSimple 管理員服務和使用之後，您必須取得服務登錄機碼。 這個索引鍵用來註冊，並使用服務連線到您 StorSimple 的裝置。

[Azure 傳統入口網站](https://manage.windowsazure.com/)中，執行下列步驟。


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> 服務登錄機碼用來註冊 StorSimple 管理員的所有裝置需要都註冊 StorSimple 管理員服務。

## <a name="step-3-download-the-virtual-device-image"></a>步驟 3︰ 下載虛擬裝置圖像

服務登錄機碼之後，您必須下載佈建虛擬裝置主機系統上的適當的虛擬裝置圖像。 虛擬裝置圖像特定的作業系統，而且您可以從 Azure 傳統入口網站中的 [快速入門] 頁面下載。

> [AZURE.IMPORTANT] 配合 Storsimple 管理員服務可能只能用於 StorSimple 虛擬陣列上執行軟體。


[Azure 傳統入口網站](https://manage.windowsazure.com/)中，執行下列步驟。

#### <a name="to-get-the-virtual-device-image"></a>若要取得虛擬裝置圖像

1.  在**StorSimple 管理員服務**頁面上，按一下您所建立的服務。 這會將您帶往 [**快速入門**] 頁面。 (您可以按一下 [快速入門] 圖示![](./media/storsimple-ova-deploy1-portal-prep/image8.png)隨時存取 [**快速入門**] 頁面。)

1.  按一下您想要從 Microsoft 下載中心下載的影像對應的連結。 圖像檔案是大約 4.8 GB。

    -   VHDX 的 HYPER-V Windows Server 2012 及更新版本

    -   VHD 的 HYPER-V Windows Server 2008 R2 及更新版本

    -   VMDK VMWare ESXi 5.5 或更新版本

2.  下載並解壓縮檔案到本機磁碟機，記下未解壓縮的檔案所在的位置。

![[視訊] 圖示](./media/storsimple-ova-deploy1-portal-prep/video_icon.png)**可用的視訊**

觀賞影片，如需瞭解如何開始使用 StorSimple 虛擬陣列的逐步指示。

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>選擇性步驟︰ 設定服務的新儲存的帳戶

這是您未啟用自動建立儲存帳戶的服務時，才執行是選擇性的步驟。

如果您需要在不同區域中建立 Azure 儲存體帳戶，請參閱[如何建立儲存的帳戶](storage-create-storage-account.md#create-a-storage-account)，如需逐步指示。

在[Azure 傳統入口網站](https://manage.windowsazure.com/)StorSimple 管理員的 [服務] 頁面上新增現有的 Microsoft Azure 儲存體帳戶，請執行下列步驟。

#### <a name="to-add-a-storage-account"></a>若要新增的儲存空間帳戶

1.  StorSimple 管理員服務登陸頁面，選取您的服務，並按兩下。 這會將您帶往 [**快速入門**] 頁面。 選取 [**設定**] 頁面。

2.  按一下 [**新增/編輯儲存帳戶**]。 在 [**新增/編輯儲存帳戶**] 對話方塊中，執行下列動作︰

    1.  按一下 [**新增**]。

    1.  提供您儲存帳戶的名稱。

    1.  提供您的 Microsoft Azure 儲存體帳戶主要**便捷鍵**。

    1.  選取 [**啟用 SSL 模式**]，建立您的裝置與雲端之間的網路通訊安全通道。 只有當您私人的雲端中操作，請清除 [**啟用 SSL 模式**] 核取方塊。

    1.  按一下核取圖示![](./media/storsimple-ova-deploy1-portal-prep/image7.png)。 儲存帳戶已成功建立之後，您將會收到通知。

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  新建立的儲存空間帳戶會顯示在**儲存**帳戶的 [**設定**] 頁面上。 按一下 [儲存新建立的儲存空間帳戶的 [**儲存**]。 按一下**[確定]**確認出現提示時。


## <a name="next-step"></a>下一步

下一步是佈建虛擬機器 StorSimple 虛擬裝置。 根據您主機的作業系統，請參閱中的詳細的指示︰

-   [佈建 HYPER-V StorSimple 虛擬陣列](storsimple-ova-deploy2-provision-hyperv.md)

-   [佈建 VMware StorSimple 虛擬陣列](storsimple-ova-deploy2-provision-vmware.md)
