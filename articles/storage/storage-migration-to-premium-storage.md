<properties
    pageTitle="升級至 Azure 進階版儲存 |Microsoft Azure"
    description="將您現有的虛擬機器移轉至 Azure 進階版儲存體。 進階版儲存提供高效能、 低延遲磁碟支援 O-大量負載執行 Azure 虛擬機器上的商務連絡人。"
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>升級至 Azure 進階版儲存空間

## <a name="overview"></a>概觀

Azure 進階版儲存提供執行 O-大量工作負載的虛擬機器高效能、 低延遲磁碟支援。 您可以採取利用速度和效能的這些磁碟移轉應用程式的 VM 磁碟至 Azure 進階版儲存體。

本指南的目的，是以協助 Azure 進階版儲存體更好的新使用者準備進行從其目前系統平滑切換到進階版儲存空間。 本指南地址三種此程序中的主要元件︰ 

  - [移轉到進階版儲存空間規劃](#plan-the-migration-to-premium-storage)
  - [準備並複製到進階版存放的虛擬硬碟 (Vhd)](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [建立使用進階版儲存 Azure 虛擬機器](#create-azure-virtual-machine-using-premium-storage)

您可以將其他平台 Vm 移轉到 Azure 進階版儲存空間，或將現有的 Azure Vm 移轉到進階版儲存標準的儲存空間。 本指南說明這兩種兩個案例的步驟。 請遵循步驟根據您的狀況相關的一節中指定。

>[AZURE.NOTE] 您可以找到功能概觀和價格的進階版儲存區中的進階版儲存空間︰ [Azure 虛擬機器負載高效能儲存空間](storage-premium-storage.md)。 我們建議您升級為您的應用程式的最佳效能要求高 IOPS Azure 進階版儲存至任何虛擬機器磁碟。 如果您的磁碟不需要高 IOPS，您可以限制成本維護其在標準的儲存空間，可儲存在硬碟機 (HDDs)，而不是 SSDs 的虛擬機器磁碟資料。

完成整個移轉程序可能會需要的其他動作之前, 和之後本指南中提供的步驟。 範例包括設定虛擬網路或結束點，或變更應用程式本身的可能會要求您的應用程式中的某些停機時間的程式碼。 這些動作唯一至每個應用程式，而您完成這些以及對進階版儲存為盡可能緊密地進行完整的轉場效果本指南中提供的步驟。


## <a name="plan-the-migration-to-premium-storage"></a>移轉到進階版儲存空間規劃

本節可確保您準備好要遵循本文中的移轉步驟，並可協助您決定最佳 VM 和磁碟類型。

### <a name="prerequisites"></a>必要條件
- 您必須 Azure 的訂閱。 如果您沒有帳戶，您可以建立一個月[免費試用版](https://azure.microsoft.com/pricing/free-trial/)訂閱，或造訪取得更多選項的 [ [Azure 價格](https://azure.microsoft.com/pricing/)。
- 若要執行 PowerShell cmdlet，您將需要 Microsoft Azure PowerShell 模組。 如需安裝點和安裝指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。
- 當您打算使用 Azure Vm 執行進階版儲存空間時，您需要使用進階版儲存簡訊 Vm。 您可以使用 [一般] 及 [進階版儲存空間的磁碟與進階版儲存簡訊 Vm。 進階版儲存空間的磁碟會在未來提供更多 VM 類型。 如需有關所有可用的 Azure VM 磁碟類型與大小的詳細資訊，請參閱[虛擬機器的大小](../virtual-machines/virtual-machines-windows-sizes.md)和[雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)。

### <a name="considerations"></a>考量

Azure VM 支援，讓您的應用程式可以有多達 64 TB 的儲存空間，每個 VM 附加數個進階版儲存空間的磁碟。 使用進階版儲存空間，您的應用程式可以每第二個磁碟處理能力每 VM 的讀取作業太低延遲達到 80,000 IOPS （輸入輸出作業秒），每個 VM 和 2000 MB。 您可以在各種 Vm 和磁碟的選項。 本節是可協助您尋找最適合您的工作量的選項。

#### <a name="vm-sizes"></a>VM 大小
Azure VM 大小規格會列在[虛擬機器的大小](../virtual-machines/virtual-machines-windows-sizes.md)。 檢閱效能特性的虛擬機器使用進階版儲存空間，然後選擇 [最適合的虛擬記憶體大小適合您的工作量。 請確定有足夠的頻寬可用的磁碟機磁碟流量您 VM 上。


#### <a name="disk-sizes"></a>磁碟大小
有三種類型的可供您 VM 的磁碟，各有特定的 IOPs 與處理限制。 請考量這些限制時選擇您 VM 磁碟類型根據容量、 效能、 延展性龐大的應用程式的需求，最大使用量載入。

|進階版儲存空間的磁碟類型|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|磁碟大小|128 GB|512 GB|1024 GB (1 TB)|
|每個磁碟 IOPS|500|2300|5000|
|每個磁碟處理量|每秒 100 MB|每秒 150 MB|每秒 200 MB|

根據您的工作量，決定是否需要您 VM 其他資料磁碟。 您可以將多個常設資料磁碟附加至您 VM。 如有需要您可以等量磁碟區增加大量的效能與容量到磁碟。 (請參閱什麼是具[這裡](storage-premium-storage-performance.md#disk-striping)。)如果您等量磁碟區使用[儲存空間]的進階版存放資料磁碟[4]，您應與每個磁碟所使用的一欄加以設定。 否則，條紋大量的整體效能可能會比預期狀況不平均分佈流量到磁碟。 針對 Linux Vm 您可以使用*mdadm*公用達成相同。 請參閱文章[設定軟體襲擊 Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) ︰ 如需詳細資訊。

#### <a name="storage-account-scalability-targets"></a>儲存帳戶延展性目標
進階版儲存的帳戶有以下延展性目標除了[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。 如果應用程式的需求超出延展性目標的單一儲存帳戶，建立您的應用程式使用多個儲存帳戶，與您的資料分割到儲存的帳戶。

|總帳戶容量|在本機上多餘的儲存帳戶的總頻寬|
|:--|:---|
|磁碟容量︰ 35 TB<br />快照容量︰ 10 TB|輸入 + 輸出秒超過 50 個 gb|

進階版儲存規格詳細資訊，請參閱[延展性與使用進階版儲存時的效能目標](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)。

#### <a name="disk-caching-policy"></a>磁碟快取原則
根據預設，磁碟快取原則是*唯讀*的進階版的所有資料磁碟和*讀寫*的進階版作業系統磁碟附加至 VM。 此設定的設定建議以獲得最佳效能，為您的應用程式在 IOs。 為寫入大量或唯資料磁碟 （例如 SQL Server 記錄檔），停用磁碟快取，以便您可以獲得更好的應用程式效能。 可以使用[Azure 入口網站](https://portal.azure.com)或*-HostCaching*參數*設定 AzureDataDisk*指令程式的更新現有的資料磁碟的快取設定。

#### <a name="location"></a>位置
挑選 Azure 進階版儲存體可用的位置。 在 [可用的位置上的更新資訊，請參閱[Azure 服務，依地區](https://azure.microsoft.com/regions/#services)。 Vm 位於相同的區域，儲存 vm 磁碟會提供多較佳的效能比如果他們是在另一個區域內的儲存空間帳戶。

#### <a name="other-azure-vm-configuration-settings"></a>其他 Azure VM 設定的設定
在建立 Azure VM 時，系統會要求您設定特定 VM 設定。 請記住，在您可以修改或稍後新增其他人時 VM 的存留固定幾項設定。 檢閱這些 Azure VM 設定的設定，並確定這些已正確設定以符合您的工作量需求。

### <a name="optimization"></a>最佳化

[Azure 進階版儲存空間︰ 設計高效能](storage-premium-storage-performance.md)提供的指導方針建置高效能應用程式使用 Azure 進階版儲存空間。 您可以遵循結合與效能最佳作法適用於應用程式所使用的技術的指導方針。


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>準備並複製到進階版存放的虛擬硬碟 (Vhd)

下一節會提供您 VM 從準備 Vhd 和 Vhd 複製到 Azure 儲存體的準則。

- [情況 1: 「 我正在移轉現有 Azure Vm 至 Azure 進階版儲存空間。 」](#scenario1)
- [案例 2: 「 我正在移轉 Vm 從其他平台至 Azure 進階版儲存空間。 」](#scenario2)

### <a name="prerequisites"></a>必要條件

若要準備移轉 Vhd，您必須︰

- Azure 訂閱的儲存空間帳戶，與儲存帳戶，您可以複製您 VHD 中的容器。 請注意的目的地儲存帳戶，可能會根據您的需求的標準或進階版儲存的帳戶。
- 若要從其建立多個 VM 執行個體一般化 VHD 工具。 例如，Windows 或 virt-sysprep 的 Ubuntu sysprep。
- VHD 檔案上傳至儲存帳戶工具。 請參閱[將資料以 AzCopy 命令列公用程式傳送](storage-use-azcopy.md)，或使用[Azure 儲存檔案總管]](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)。 本文將說明複製您 VHD 使用 AzCopy 工具。

> [AZURE.NOTE] 如果您選擇使用 AzCopy 的同步複本選項的最佳效能，複製您 VHD 從 Azure VM 為目的地儲存帳戶的同一個區域中，執行下列一項工具。 如果您從不同區域 Azure VM 複製 VHD，效能可能會變慢。
>
> 如需有限的頻寬上進行複製大量的資料，請考慮[使用 Azure 匯入/匯出服務傳送資料至 Blob 儲存體](storage-import-export-service.md)。這個選項可讓您依出貨硬碟機的將資料傳送至 Azure 資料中心。 若要將資料複製到標準儲存帳戶，您可以使用匯入/匯出 Azure 服務。 標準儲存帳戶中的資料後，您可以使用 [[複製 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) 」 或 「 AzCopy 來傳送資料至您的進階版儲存帳戶。
>
> 請注意，Microsoft Azure 僅支援固定的大小 VHD 檔案。 不支援 VHDX 檔案或動態 Vhd。 如果您有動態 VHD 時，您可以將它轉換為固定大小使用[轉換 VHD](http://technet.microsoft.com/library/hh848454.aspx)指令程式。

### <a name="scenario1"></a>情況 1: 「 我正在移轉現有 Azure Vm 至 Azure 進階版儲存空間。 」

如果您要移轉現有 Azure Vm，停止 VM、 準備 Vhd 每個您想，VHD 的類型，然後複製 AzCopy 或 PowerShell VHD。

VM 必須完全移轉初始狀態向下。 移轉完成之前，將會當機。

#### <a name="step-1-prepare-vhds-for-migration"></a>步驟 1。 Vhd 準備移轉

如果您將現有的 Azure Vm 移轉至進階版儲存空間，可能是您 VHD:

- 一般化的作業系統圖像
- 唯一的作業系統磁碟
- 資料磁碟

以下我們逐步準備您 VHD 這些 3 案例。

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>使用通用的作業系統 VHD 建立多個 VM 執行個體

如果您要上傳 VHD 會用來建立多個一般 Azure VM 執行個體，您必須先一般化 VHD 使用 sysprep 公用程式。 這會套用至 VHD 的內部部署或雲端中。 Sysprep VHD 中移除任何電腦特定的資訊。

>[AZURE.IMPORTANT] 快照，或備份您的 VM 一般化它之前。 執行 sysprep 會停止，並解除 VM 執行個體。 Sysprep Windows OS VHD 遵循下列步驟。 請注意，執行 Sysprep 命令會要求您關閉虛擬機器。 如需有關 Sysprep 的詳細資訊，請參閱[Sysprep 概觀](http://technet.microsoft.com/library/hh825209.aspx)或[Sysprep 技術參照](http://technet.microsoft.com/library/cc766049.aspx)。

1. 以管理員身分開啟命令提示字元視窗。
2. 輸入下列命令以開啟 Sysprep:

        %windir%\system32\sysprep\sysprep.exe

3. 在系統準備工具中，選取的輸入系統的全新體驗 (OOBE)，選取一般化核取方塊**關機**]，然後選取下圖所示，然後按一下**[確定**]。 Sysprep 將一般化作業系統，並關閉系統。

    ![][1]

針對 Ubuntu VM，使用 virt sysprep 達成相同。 請參閱[virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html)如需詳細資訊。 另請參閱其他 Linux 作業系統的開啟來源[Linux 伺服器佈建軟體](http://www.cyberciti.biz/tips/server-provisioning-software.html)部分。

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>使用唯一的作業系統 VHD 建立單一 VM 執行個體

如果您有 VM 需要機器特定資料上執行的應用程式時，不會一般化 VHD。 非一般化 VHD 可用來建立唯一的 Azure VM 執行個體。 例如，如果您擁有網域控制站您 VHD 上時，執行 sysprep 它將網域控制站無效。 檢閱您 VM 並在其中執行 sysprep 一般化 VHD 之前的影響上的應用程式。

##### <a name="register-data-disk-vhd"></a>註冊資料磁碟 VHD

如果您要移轉的 Azure 中資料磁碟，您必須確定使用這些資料磁碟 Vm 會關閉。

請依照將 VHD 複製到 Azure 進階版儲存空間，並為能夠的資料磁碟註冊如下所述的步驟。

#### <a name="step-2-create-the-destination-for-your-vhd"></a>步驟 2。 建立您 VHD 的目的地

建立維護您 Vhd 的儲存空間帳戶。 規劃存放您 Vhd 位置時，請考慮下列幾點︰

- 目標進階版儲存帳戶。
- 儲存帳戶位置必須與進階版儲存您將建立在最後階段簡訊 Azure Vm 相同。 您無法複製至新的儲存空間帳戶或計劃以使用相同的儲存空間帳戶根據您的需求。
- 複製並儲存儲存帳戶金鑰的目的地儲存帳戶的下一個階段。

為資料磁碟，您可以選擇將部分資料磁碟標準儲存帳戶 （例如，磁碟有製冷器的儲存空間的），但我們強烈建議您將使用進階版儲存生產工作負載的所有資料。

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>步驟 3。 複製 VHD AzCopy 或 PowerShell

您必須尋找容器路徑和儲存帳戶金鑰處理其中一項這兩個選項。 容器路徑和儲存空間的 [帳戶金鑰，請參閱**Azure 入口網站** > **儲存空間**。 容器 URL 會類似 「 https://myaccount.blob.core.windows.net/mycontainer/ 」。

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>選項 1︰ 複製與 AzCopy （非同步複本） VHD

使用 AzCopy，您可以輕鬆地上傳 VHD 透過網際網路。 根據 Vhd 的大小，這可能會花時間。 請記得要使用這個選項時，請核取儲存帳戶輸入/出口限制。 如需詳細資訊，請參閱[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

1. 下載並安裝 AzCopy 從這裡開始︰[最新版本的 AzCopy](http://aka.ms/downloadazcopy)
2. 開啟 Azure PowerShell，並移至 AzCopy 安裝所在的資料夾。
3. 您可以使用下列命令來複製 「 目的地 」 的 「 來源 」 VHD 檔案。

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    範例︰

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    以下是使用 AzCopy 命令中的參數的說明︰

 - * */來源︰ *&lt;來源&gt;:** * 資料夾或包含 VHD 存放容器 URL 的位置。
 - * */SourceKey: *&lt;來源帳戶金鑰&gt;:** * 儲存空間的來源儲存帳戶的 [帳戶金鑰。
 - * */Dest: *&lt;目的地&gt;:** * 存放容器 URL 複製到 VHD。
 - * */DestKey: *&lt;目的地帳戶金鑰&gt;:** * 儲存空間的目的地儲存帳戶的 [帳戶金鑰。
 - * */模式︰ *&lt;檔案名稱&gt;:** * 指定複製 VHD 的檔案名稱。

如需使用 AzCopy 工具的詳細資訊，請參閱[傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)。

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>選項 2︰ 複製 VHD 使用 PowerShell （同步處理複本）

您也可以複製使用 PowerShell 指令程式開始 AzureStorageBlobCopy VHD 檔案。 使用 PowerShell 的 Azure 中的下列命令，複製 VHD。 從來源與目的地儲存帳戶的對應值取代 <> 中的值。 若要使用這個命令，您必須在您的目的地儲存帳戶稱為 vhd 的容器。 如果沒有容器，建立一個之前執行命令。

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

範例︰

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>案例 2: 「 我正在移轉 Vm 從其他平台至 Azure 進階版儲存空間。 」

如果您以 Azure 遷移 VHD 從非-Azure 雲端儲存空間，您必須先匯出 VHD 本機的目錄。 有本機 VHD 儲存的目錄方便，完成的來源路徑，然後使用 AzCopy 上, 傳至 Azure 儲存體。

#### <a name="step-1-export-vhd-to-a-local-directory"></a>步驟 1。 匯出 VHD 本機目錄

##### <a name="copy-a-vhd-from-aws"></a>複製 AWS VHD

1. 如果您使用的 AWS，請將中 Amazon S3 連結 VHD 匯出 EC2 執行個體。 請遵循匯出 Amazon EC2 執行個體安裝 Amazon EC2 命令列介面 (CLI) 工具及執行 VHD 檔案匯出 EC2 執行個體的 [建立執行個體-匯出工作] 命令的 Amazon 文件中所述的步驟。 請務必使用**VHD**磁碟與 #95; 圖像與 #95。執行 [**建立執行個體-匯出-工作**] 命令時，出現格式變數。 匯出的 VHD 檔案會儲存在您指定的程序期間 Amazon S3 連結。

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. 從 S3 填色下載 VHD 檔案。 選取 VHD 檔案，然後**動作** > **下載**。

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>複製其他非 Azure 雲端 VHD

如果您以 Azure 遷移 VHD 從非-Azure 雲端儲存空間，您必須先匯出 VHD 本機的目錄。 複製儲存 VHD 本機目錄的完整的來源路徑。

##### <a name="copy-a-vhd-from-on-premise"></a>複製 VHD 從內部部署

如果您從內部部署環境移轉 VHD，您必須完成的來源路徑 VHD 儲存的位置。 來源路徑可能伺服器位置或檔案共用。

#### <a name="step-2-create-the-destination-for-your-vhd"></a>步驟 2。 建立您 VHD 的目的地

建立維護您 Vhd 的儲存空間帳戶。 規劃存放您 Vhd 位置時，請考慮下列幾點︰

- 目標儲存帳戶可能會根據您的應用程式需求標準或付費的儲存空間。
- 儲存帳戶地區必須與進階版儲存您將建立在最後階段簡訊 Azure Vm 相同。 您無法複製至新的儲存空間帳戶或計劃以使用相同的儲存空間帳戶根據您的需求。
- 複製並儲存儲存帳戶金鑰的目的地儲存帳戶的下一個階段。

我們強烈建議您將使用進階版儲存生產工作負載的所有資料。

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>步驟 3。 將 VHD 上傳至 Azure 儲存體

現在，您的本機目錄中有您 VHD，您可以使用 [AzCopy 或 AzurePowerShell.vhd 檔案上傳至 Azure 儲存體。 以下提供這兩個選項︰

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>選項 1︰ 使用 Azure PowerShell 新增 AzureVhd.vhd 檔案上傳

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

範例<Uri>可能是因為**_「 https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd 」_**。 範例<FileInfo>可能是因為**_「 C:\path\to\upload.vhd 」_**。

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>選項 2︰ 使用 AzCopy.vhd 檔案上傳

使用 AzCopy，您可以輕鬆地上傳 VHD 透過網際網路。 根據 Vhd 的大小，這可能會花時間。 請記得要使用這個選項時，請核取儲存帳戶輸入/出口限制。 如需詳細資訊，請參閱[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

1. 下載並安裝 AzCopy 從這裡開始︰[最新版本的 AzCopy](http://aka.ms/downloadazcopy)
2. 開啟 Azure PowerShell，並移至 AzCopy 安裝所在的資料夾。
3. 您可以使用下列命令來複製 「 目的地 」 的 「 來源 」 VHD 檔案。

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    範例︰

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    以下是使用 AzCopy 命令中的參數的說明︰

 - * */來源︰ *&lt;來源&gt;:** * 資料夾或包含 VHD 存放容器 URL 的位置。
 - * */SourceKey: *&lt;來源帳戶金鑰&gt;:** * 儲存空間的來源儲存帳戶的 [帳戶金鑰。
 - * */Dest: *&lt;目的地&gt;:** * 存放容器 URL 複製到 VHD。
 - * */DestKey: *&lt;目的地帳戶金鑰&gt;:** * 儲存空間的目的地儲存帳戶的 [帳戶金鑰。
 - **/BlobType︰ 頁面︰**指定目的地頁面 blob。
 - * */模式︰ *&lt;檔案名稱&gt;:** * 指定複製 VHD 的檔案名稱。

如需使用 AzCopy 工具的詳細資訊，請參閱[傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)。

##### <a name="other-options-for-uploading-a-vhd"></a>上傳 VHD 的其他選項

您也可以上載 VHD 使用其中一個動作所代表的意義您儲存帳戶︰

- [Azure 儲存複製 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure 儲存檔案總管上傳二進位大型物件](https://azurestorageexplorer.codeplex.com/)
- [儲存匯入/匯出服務 REST API 參考](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] 我們建議使用匯入/匯出服務，如果估計上傳的時間長度超過 7 天。 您可以使用[DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html)來評估從資料大小和傳輸單位的時間。
>
> 複製到標準儲存帳戶可匯入/匯出]。 您必須使用的工具，例如 AzCopy 進階版儲存帳戶複製標準的儲存空間。


## <a name="create-azure-virtual-machine-using-premium-storage"></a>建立 Azure Vm 使用進階版儲存空間

VHD 是上傳或複製到您要的儲存帳戶之後，請遵循此節] 註冊 VHD OS 圖像或根據您的狀況 OS 磁碟，然後在 [從其建立 [VM 執行個體中的指示進行。 已建立資料磁碟 VHD 可附加至 VM。 在此節結尾提供的範例移轉指令碼。 這個簡單的指令碼不符合所有案例。 您可能需要更新以符合您特定案例的指令碼。 如果這個指令碼會套用至您的狀況，請參閱下方[範例移轉指令碼](#a-sample-migration-script)。

### <a name="checklist"></a>檢查清單

1.  完成等到所有複製的 VHD 磁碟。
2.  請確定進階版儲存有您要移轉至區域中。
3.  決定您要使用的新 VM 數列。 應該簡訊，進階版儲存空間並大小應該根據區域中的可用性根據您的需求。
4.  決定您會使用完全虛擬記憶體大小。 虛擬記憶體大小必須夠大，以支援您的資料磁碟數目。 例如︰ 如果您有 4 資料磁碟，VM 必須 2 或更多核心。 此外，請考慮處理能力，記憶體和網路頻寬需求。
5.  目標區域中建立進階版儲存的帳戶。 這是您可以使用的新 VM 的帳戶。
6.  具有目前 VM 詳細資料方便，包括磁碟和對應 VHD blob 的清單。

準備停機時間的應用程式。 若要執行 clean 移轉，必須停止目前系統中的所有的處理。 只有取得一致的狀態，您可以移轉到新的平台的。 停機時間工期取決於要移轉的磁碟中的資料量。

>[AZURE.NOTE] 如果您從特殊 VHD 磁碟建立 Azure 資源管理員 VM，請參閱[此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)部署資源管理員 VM 使用現有的磁碟。

### <a name="register-your-vhd"></a>註冊您 VHD

若要從 OS VHD 建立 VM，或將資料磁碟附加至新的 VM，您必須先進行註冊。 請遵循下列步驟，根據您 VHD 的狀況。

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>一般化作業系統 VHD，以建立多個 Azure VM 執行個體

一般化的 OS 映像 VHD 上傳到儲存帳戶之後，它登錄為**Azure VM 圖像**，讓您可以從其建立一或多個 VM 執行個體。 使用下列 PowerShell cmdlet 來註冊您 VHD 為 Azure VM OS 圖像。 提供完整的容器 URL VHD 已複製到的位置。

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

複製並儲存此新 Azure VM 圖像的名稱。 在上述範例中，則*OSImageName*。

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>若要建立單一 Azure VM 執行個體的唯一作業系統 VHD

唯一 OS VHD 上傳到儲存帳戶之後，它登錄為**Azure OS 磁碟**，讓您可以從其建立 VM 執行個體。 使用下列 PowerShell cmdlet 來註冊您 VHD 為 Azure OS 磁碟。 提供完整的容器 URL VHD 已複製到的位置。

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

複製並儲存此新 Azure OS 磁碟的名稱。 在上述範例中，則*OSDisk*。

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>資料磁碟 VHD 来附加至新的 Azure VM 執行個體

資料磁碟 VHD 上傳到儲存帳戶之後，它登錄為 Azure 資料磁碟使其可附加至新 DS 數列，DSv2 數列或 GS 數列 Azure VM 執行個體。

使用下列 PowerShell cmdlet 來註冊您 VHD 為 Azure 資料磁碟。 提供完整的容器 URL VHD 已複製到的位置。

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

複製並儲存此新 Azure 資料磁碟的名稱。 在上述範例中，則*DataDisk*。

### <a name="create-a-premium-storage-capable-vm"></a>建立進階版儲存簡訊 VM

一次 OS 圖像或註冊 OS 磁碟，建立新的 DS 數列、 DSv2 數列或 GS 數列 VM。 您將使用的作業系統圖像或註冊您的電腦系統磁碟名稱。 從進階版儲存層選取 VM 類型。 在下面的範例中，我們使用*Standard_DS2*虛擬記憶體大小。

>[AZURE.NOTE] 更新磁碟大小，以確保其符合您的容量效能需求和 Azure 的可用磁碟大小。

請遵循下列建立新的 VM 逐步 PowerShell 指令程式。 首先，設定常見的參數︰

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

首先，建立，您將主控您的新 Vm 雲端服務。

    New-AzureService -ServiceName $serviceName -Location $location

接下來，根據您的狀況，請從 OS 圖像或註冊您的作業系統磁碟建立 Azure VM 執行個體。

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>一般化作業系統 VHD，以建立多個 Azure VM 執行個體

建立一或多個新 DS 數列 Azure VM 執行個體使用您註冊**Azure OS 圖像**。 建立新的 VM，如下所示時，請在 [VM 設定中指定此 OS 圖像的名稱。

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>若要建立單一 Azure VM 執行個體的唯一作業系統 VHD

建立新 DS 數列 Azure VM 執行個體使用您註冊**Azure OS 磁碟**。 建立新的 VM，如下所示時，請在 [VM 設定中指定此 OS 磁碟名稱。

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

指定其他 Azure VM 資訊，例如雲端服務、 地區、 儲存帳戶、 可用性設定和快取原則。 請注意 VM 執行個體必須與相關聯的作業系統或資料磁碟放在一起，因此所選的雲端服務、 地區和儲存帳戶必須使用那些磁碟為基礎的 Vhd 的同一個位置。

### <a name="attach-data-disk"></a>附加資料磁碟

最後，如果您已註冊資料磁碟 Vhd，請將它們附加到新進階版儲存簡訊 Azure VM。

使用下列 PowerShell 指令程式來將資料磁碟附加至新的 VM 並指定的快取原則。 在下列範例中的快取原則會設定為*唯讀*。

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] 可能會有額外的步驟所需支援的應用程式未包含本指南。

### <a name="checking-and-plan-backup"></a>檢查及計劃備份

一旦新 VM 執行，存取並使用相同的登入 id 並密碼原始 VM，做為驗證的所有項目會如預期般運作。 所有的設定，包括等量的區，就會出現在新的 VM。

最後一個步驟是計劃備份，並進行的維修作業排程新 vm 根據應用程式的需求。

### <a name="a-sample-migration-script"></a>範例移轉指令碼

如果您有多個 Vm 移轉，透過 PowerShell 指令碼來自動化會很有幫助。 以下是範例指令碼的自動化 VM 移轉。 附註的指令碼] 下方的範例，並有幾個假設對目前 VM 磁碟。 您可能需要更新以符合您特定案例的指令碼。

假設是︰

- 您會建立傳統 Azure Vm。
- 您來源 OS 磁碟和來源資料是在同一個儲存帳戶和相同的容器。 如果您 OS 及資料磁碟不是在相同位置，您可以使用 [AzCopy 或 PowerShell 的 Azure 儲存體帳戶和容器複製 Vhd。 請參考上一個步驟︰[複製 VHD AzCopy 或 PowerShell](#copy-vhd-with-azcopy-or-powershell)。 編輯以符合您的狀況這個指令碼是另一個選項，但建議使用 AzCopy 或 PowerShell，因為它是更容易且更快。

自動化指令碼如下所示。 使用您的資訊取代文字，並更新以符合您特定案例的指令碼。

>[AZURE.NOTE] 使用現有的指令碼不會保留來源 VM 的網路設定。 您必須重新設定網路設定您的移轉 Vm 上。

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>最佳化

您可以自訂您目前的 VM 設定以便於也會使用標準磁碟。 例如，若要增加效能條紋區中使用多個磁碟。 例如，而不是使用 4 磁碟分別進階版儲存空間，您或許可以由單一磁碟最佳化成本。 最佳化，例如此需要處理案例為基礎，並在移轉之後需要自訂的步驟。 此外，請注意，此程序可能也無法運作的資料庫及應用程式的安裝程式中定義的磁碟版面配置而定。

##### <a name="preparation"></a>準備

1.  前一節所述，請完成簡單移轉。 在移轉之後，將新 VM 上執行最佳化。
2.  定義新最佳化設定所需的磁碟大小。
3.  決定目前新的磁碟規格磁碟/區的對應。

##### <a name="execution-steps"></a>執行步驟

1.  建立新的磁碟進階版儲存 VM 上正確的大小。
2.  登入 VM 並複製資料對應至該大量的新磁碟目前音量。 執行此動作必須對應到新的磁碟的所有目前區。
3.  接下來，變更的應用程式設定，以切換到新的磁碟，然後卸離舊的區。

調整更好的磁碟效能的應用程式，請參閱[最佳化應用程式的效能](storage-premium-storage-performance.md#optimizing-application-performance)。

### <a name="application-migrations"></a>移轉應用程式

資料庫及其他複雜的應用程式所定義的移轉應用程式提供者可能會要求特殊的步驟。 請參閱個別的應用程式的文件。 例如︰ 通常您可以將資料庫移轉到備份與還原。

## <a name="next-steps"></a>後續步驟

請參閱下列資源的移轉虛擬機器特定案例︰

- [移轉儲存帳戶之間的 Azure 虛擬機器](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [建立並上傳至 Azure 的 Windows Server VHD。](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [建立及上傳虛擬硬碟包含 Linux 作業系統](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [從 Amazon AWS 移轉的虛擬機器，以 Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

此外，請參閱下列資源，進一步瞭解 Azure 儲存體和 Azure 虛擬機器︰

- [Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)
- [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Azure 虛擬機器工作負載的進階版儲存空間︰ 高效能儲存空間](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
