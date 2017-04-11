<properties
    pageTitle="適用於開發人員 azure 批次功能概觀 |Microsoft Azure"
    description="瞭解批次服務及從開發的觀點來看它 Api 的功能。"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>適用於開發人員批次功能概觀

在此概觀 Azure 批次服務的核心元件，我們討論的主要服務功能和資源，批次開發人員可以用來建立大型平行計算解決方案。

無論您開發分散運算應用程式或服務的問題直接[REST API] [batch_rest_api]來電或您正在使用其中一個[批次 Sdk](batch-technical-overview.md#batch-development-apis)，您會使用許多的資源與本文所述的功能。

> [AZURE.TIP] 批次服務的高層級簡介，請參閱[Azure 批的基本概念](batch-technical-overview.md)。

## <a name="batch-service-workflow"></a>批次服務工作流程

下列高層級的工作流程是一般幾乎所有應用程式與服務的使用批次服務處理平行工作負載的各項︰

1. 上傳您想要處理[Azure 儲存體]**資料檔**[azure_storage]帳戶。 批次包含內建的支援存取 Azure Blob 儲存體，和您的任務可以下載這些檔案來[計算節點](#compute-node)執行工作時。

2. 上傳您的工作會執行**應用程式的檔案**。 這些檔案可二進位檔案或指令碼和相依性，並執行的工作中的工作。 您的任務可以下載這些檔案從您儲存的帳戶，或您可以使用[應用程式套件](#application-packages)功能批次的應用程式管理及部署。

3. 建立[資料庫](#pool)的運算節點。 當您建立資料庫時，您指定的資料庫，其大小和作業系統運算節點數目。 每個任務，在您工作中的執行時，已指派執行在其中一個程式集區中的節點。

4. 建立[工作](#job)。 工作管理工作的集合。 您建立關聯到特定的資料庫，該工作的工作位置會執行每項工作。

5. 將[任務](#task)新增至工作。 應用程式或您上傳到處理資料檔案，其會從您儲存的帳戶下載的指令碼，則會執行每項工作。 完成每項工作，它可以將輸出上傳至 Azure 儲存體。

6. 監視工作進度，並從 Azure 儲存體擷取工作成果。

下列各節討論這些及其他資源批次，可讓您分散式計算案例。

> [AZURE.NOTE] 您需要使用批次服務以[批次帳戶](batch-account-create-portal.md)。 此外，幾乎所有解決方案都使用[Azure 儲存體][azure_storage]檔案儲存與擷取帳戶。 批次目前支援**一般用途**儲存帳戶類型，[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)[的相關 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的步驟 5 中所述。

## <a name="batch-service-resources"></a>批次服務資源

部分下列資源，帳戶，來計算節點、 資料庫、 工作和工作，所需的所有使用批次服務的解決方案。 其他人]，工作排程等應用程式套件，是很有幫助，但選擇性功能。

- [帳戶](#account)
- [計算節點](#compute-node)
- [集區](#pool)
- [工作](#job)

  - [工作排程](#scheduled-jobs)

- [任務](#task)

  - [啟動工作](#start-task)
  - [工作管理員工作](#job-manager-task)
  - [準備和發行的工作](#job-preparation-and-release-tasks)
  - [多個執行個體任務 (MPI)](#multi-instance-tasks)
  - [任務相依性](#task-dependencies)

- [應用程式套件](#application-packages)

## <a name="account"></a>帳戶

批次帳戶是唯一識別的實體批次服務中。 所有的處理是以批次帳戶相關聯。 當您執行作業批次服務時，您會需要帳戶名稱，以及其中一個帳戶金鑰。 您可以[建立 Azure 批次帳戶使用 Azure 入口網站](batch-account-create-portal.md)。

## <a name="compute-node"></a>計算節點

運算節點是 Azure 虛擬機器 (VM) 專門用來處理您的應用程式工作負載的一部分。 節點的大小會決定數 CPU 核心、 記憶體容量和配置節點的本機檔案系統大小。 您可以使用 Azure 雲端服務或虛擬機器服務商場的圖像，以建立 Windows 或 Linux 節點的資料庫。 這些選項，請參閱下的[資料庫](#pool)一節，如需詳細資訊。

節點可以執行任何可執行檔或支援的作業系統環境節點的指令碼。 這包含\*.exe \*.cmd \*.bat 和 Windows-和二進位檔案，PowerShell 指令碼骨架與 Python 指令碼的 Linux。

所有計算節點批次中的也包含︰

- 標準[資料夾結構](#files-and-directories)及相關聯的[環境變數](#environment-settings-for-tasks)所使用的工作的參照。
- 設定來控制存取權的**防火牆**設定。
- [遠端存取](#connecting-to-compute-nodes)Windows (遠端桌面通訊協定 (RDP)) 和 Linux (安全命令介面 (SSH)) 節點。

## <a name="pool"></a>集區

集區是在執行應用程式的節點的集合。 集區可以手動建立，或自動批次服務當您指定要完成的工作。 您可以建立及管理資源符合您的應用程式集區。 集區只可供建立的批次帳戶。 批次帳戶可以有一個以上的資料庫。

Azure 批次資料庫建立核心 Azure 計算平台的上方。 他們提供大規模配置、 應用程式安裝、 資料通訊、 狀況監控和集區 （[縮放](#scaling-compute-resources)） 內的計算節點的數字的彈性調整。

會新增至資料庫的每個節點會被指派，唯一的名稱及 IP 位址。 從資料庫移除節點，作業系統或檔案所做的任何變更會遺失，其名稱及 IP 位址發行以供日後使用。 當節點離開資料庫時，其為單位的週期是透過。

當您建立資料庫時，您可以指定下列屬性︰

- 計算節點**的作業系統**和**版本**

    選取 [程式集區中的 [節點作業系統時，有兩個選項︰**虛擬機器設定**和**雲端服務設定**。

    **虛擬機器設定**提供 Linux 和 Windows 圖像計算節點從[Azure 虛擬機器 Marketplace][vm_marketplace]。
    當您建立包含虛擬機器設定節點的資料庫時，您必須指定不僅節點，但也**虛擬機器影像參照**和批次**節點代理程式 SKU**安裝在節點的大小。 如需有關如何指定這些資料庫屬性的詳細資訊，請參閱[佈建 Linux 運算 Azure 批次集區中的節點](batch-linux-nodes.md)。

    **雲端服務設定**提供 Windows 節點*只*計算。 可用的作業系統雲端服務設定集區會列於[Azure 來賓作業系統版本和 SDK 相容性矩陣圖](../cloud-services/cloud-services-guestos-update-matrix.md)。 當您建立包含雲端服務節點的資料庫時，您需要指定節點大小和其*OS 系列*]。 當您建立資料庫的 Windows 運算節點時，您最常使用雲端服務。

    - *OS 系列*也會決定哪些版本的.NET 已安裝作業系統。
    - 為工作者雲端服務中的角色，您可以指定*作業系統版本*（如需有關工作者角色的詳細資訊，請參閱 [[告訴我雲端服務](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services)] 區段中[Cloud Services 概觀](../cloud-services/cloud-services-choose-me.md)）。
    - 利用工作者角色，我們建議您指定為`*`的*作業系統版本*，以便節點就會自動升級，並不需要跟著新的執行工作發行版本。 選取特定的作業系統版本的主要的使用案例是為了確保應用程式的相容性，讓測試，以允許可更新的版本之前所要執行的回溯相容性。 驗證後，您可以更新集區*作業系統版本*和新的 OS 圖像可安裝-任何執行中的工作會中斷，重新佇列。

- **節點的大小**

    **雲端服務設定**計算節點大小會列在[雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)。 批次支援以外的所有雲端服務大小`ExtraSmall`。

    **虛擬機器設定**計算的節點[中 Azure 虛擬機器大小](../virtual-machines/virtual-machines-linux-sizes.md)(Linux) 和[中 Azure 虛擬機器大小](../virtual-machines/virtual-machines-windows-sizes.md)(Windows) 中所列的大小。 批次支援以外的所有 Azure VM 大小`STANDARD_A0`，以及使用進階版儲存空間 (`STANDARD_GS`， `STANDARD_DS`，及`STANDARD_DSV2`數列)。

    選取時計算節點大小，請考慮特性，您會節點上執行應用程式的需求。 方面是否應用程式的多執行緒等耗用的記憶體有助於判斷最適合且經濟節點大小。 若要選取節點大小，假設一項任務將節點上執行一次通常會。 然而，有可能是有多個任務 （，因此多個應用程式執行個體） 上的 [[同時執行](batch-parallel-node-tasks.md)作業執行期間運算節點。 在此情況下，是常見選擇較大的節點大小，以納入其間的平行工作執行增加的要求。 如需詳細資訊，請參閱[任務的排程原則](#task-scheduling-policy)。

    集區中的節點都相同的大小。 如果想要執行的應用程式使用不同的系統需求及/或載入層級，我們建議您使用不同的集區。

- **目標節點數目**

    這是您想要部署集區中的計算節點數目。 這被指*目標*因為在某些情況下，您的資料庫可能達到所要的節點數目。 集區可能達到所要的節點數目，如果達到[核心配額](batch-quota-limit.md#batch-account-quotas)批次帳戶，或是您已套用至資料庫的限制 （請參閱下的 [縮放比例原則 」 一節） 的節點數目上限為自動調整大小的公式。

- **縮放比例原則**

    除了指定靜態節點的數字，您可以改為撰寫或套用至集區的 [[自動調整大小的公式](#scaling-compute-resources)。 批次服務定期評估公式，並調整根據不同的資料庫、 工作和工作參數，您可以指定在集區中的節點數目。

- **任務排程的原則**

    [每個節點的最大工作](batch-parallel-node-tasks.md)設定選項會決定您可以執行平行集區中每個運算節點的任務的最大數目。

    預設的設定是一項工作，一次執行節點，但會很有幫助有一個以上的工作，同時執行節點的情況下。 請參閱[同時節點工作](batch-parallel-node-tasks.md)文章，以瞭解您可以助益每個節點的多個任務中的[範例案例](batch-parallel-node-tasks.md#example-scenario)。

    您也可以指定*填滿類型*決定是否批次的資料庫中的所有節點平均散佈工作或之前指派工作給另一個節點套件每個任務的最大的數字的節點。

- 運算節點**通訊的狀態**

    在大多數情況下，任務獨立運作，而不需要進行通訊的另一個。 然而，有部分的工作必須進行通訊，例如[MPI 案例](batch-mpi.md)的應用程式。

    您可以設定允許內 it-**internode 通訊**節點之間的通訊的資料庫。 啟用 internode 通訊時，在雲端服務設定資料庫中的節點可以彼此連接埠大於 1100，並虛擬機器設定集區不會限制連接埠。

    請注意，啟用 internode 通訊也會影響內叢集節點的位置可能會因部署限制而限制集區中的節點數目上限。 如果您的應用程式時，不需要節點之間的通訊，批次服務可以從許多不同的叢集集區中配置可能很大節點的數字，並啟用資料中心增加平行處理 power。

- 運算節點的 [**開始工作**

    *啟動工作*選用的每個節點上執行，以及該節點加入集區中，每次節點必須重新啟動或重新建立影像。 開始工作功能的準備工作，例如安裝運算節點執行的工作的應用程式的執行運算節點尤其有用。

- **應用程式套件**

    您可以指定要部署至此運算節點集區中的[應用程式套件](#application-packages)。 應用程式套件提供簡化的部署及執行工作的應用程式的版本設定。 您指定的集區的應用程式套件已安裝在加入該資料庫，每個節點並節點的每次重新啟動或重新建立影像。 應用程式套件上 Linux 運算節點是目前某些不受支援。

- **網路設定**

    您可以指定應該建立資料庫的計算節點的 Azure[虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md)的識別碼。 需求的程式集區，請參閱[新增集區帳戶]指定 VNet[vnet]批次 REST API 參照中。

> [AZURE.IMPORTANT] 批次的所有帳戶都有限制的**核心**（以及，運算節點） 數批次帳戶預設**配額**。 您可以在[配額和 Azure 批次服務限制](batch-quota-limit.md)找到的預設配額和指示[增加配額](batch-quota-limit.md#increase-a-quota)（例如核心批次帳戶中的最大的數字）。 如果您發現要求 「 為何無法我資料庫達到多個 X 節點？ 」 此核心配額可能的原因。

## <a name="job"></a>工作

工作是工作的集合。 管理其工作運算節點集區中執行計算的方式。

- 工作指定的工作可執行的**資料庫**。 您可以建立新的資料庫，每個工作，或使用集區許多工作。 您可以建立資料庫的相關聯的工作排程，每項工作或工作排程與相關聯的所有工作。

- 您可以指定選擇性的**工作優先順序**。 當工作會送出，以高於目前正在進行中的工作的優先順序時，為高優先順序工作的工作會插入在較低優先順序工作的工作佇列。 不會佔用中較低優先順序工作已在執行中的工作。

- 您可以使用工作**的限制式**，指定工作的特定限制︰

    您可以設定**最大 wallclock 時間**，超過指定的最大 wallclock 時間執行工作時，如果終止的工作及所有子任務。

    批次，可以偵測並再試一次失敗的工作。 您可以指定**最大次數任務**限制式，包括*自動*或*永遠不會*重試任務是否為。 重試任務，表示工作重新佇列再執行一次。

- 您的用戶端應用程式可以將任務新增至工作，或您可以指定[工作管理員工作](#job-manager-task)。 工作管理員工作包含有建立與執行運算節點集區中的其中一個工作管理員工作的必要的工作的工作，所需的資訊。 處理工作管理員工作，特別是以批次-佇列一經作業會建立，並失敗時，必須重新啟動。 工作管理員工作是*必要*的工作，因為它是唯一的方式定義工作之前產生工作時，所建立的[工作排程](#scheduled-jobs)。

- 根據預設，工作會保留在作用中狀態時的工作中的所有工作都的完成。 您可以變更這個行為，讓工作中的所有工作完成時，會自動終止工作。 設定工作的**onAllTasksComplete**屬性 ([OnAllTasksComplete] [net_onalltaskscomplete]批次.net) 至*terminatejob*自動結束工作，所有任務時已完成的狀態。

    請注意批次服務會考慮*所有的任務完成工作*的工作。 因此，此選項是最常用的 [[工作管理員工作](#job-manager-task)。 如果您想要使用工作管理員不自動工作取消，您應該一開始將新的工作**onAllTasksComplete**屬性設定為*noaction*，然後將它設定為*terminatejob* ，只有在您完成新增工作至工作後。

### <a name="job-priority"></a>工作優先順序

您可以指定在批次中所建立的工作的優先順序。 批次服務會使用工作的優先順序] 的值來判斷工作排程的帳戶 （這並不會與[排程的工作](#scheduled-jobs)，後者） 內的順序。 優先順序是從-1000年到 1000，-1000年的最低的優先順序和 1000年是最高。 您可以使用[更新的工作屬性]更新工作的優先順序[rest_update_job]作業 （批次剩餘） 或修改[CloudJob.Priority] [net_cloudjob_priority]屬性 (批次.NET)。

在同一個帳戶，較高優先順序工作會有排程於較低優先順序工作的優先順序。 一個帳戶為高優先順序值工作沒有排程移到不同的帳戶為較低優先順序值的另一個工作的優先順序。

工作排程跨集區不受影響。 不同的集區之間不保證其相關聯的資料庫時就閒置節點較高優先順序工作第一次排程。 在相同的資料庫中，在相同的優先順序層級的工作會有相等的機會的排程。

### <a name="scheduled-jobs"></a>排程的工作

[工作排程][rest_job_schedules]可讓您建立週期性工作批次服務中的。 工作排程指定何時要執行的工作，並包含要執行的工作的規格。 您可以指定期間的排程，多久和時排程生效，且多久的時間週期應該建立工作。

## <a name="task"></a>任務

任務是計算的與工作相關聯的單位。 在 [節點上執行。 指派給執行，節點的工作，或直到節點變成免費佇列。 簡言之，任務上執行，一或多個程式或指令碼運算節點執行必須完成的成果。

當您建立任務時，您可以指定︰

- **命令列**的工作。 這是在運算節點執行應用程式或指令碼命令列。

    請務必注意命令列實際不執行下命令介面。 因此，它不能原生利用等[環境變數](#environment-settings-for-tasks)擴充功能，命令介面 (包括`PATH`)。 若要利用這類功能，您必須叫用的命令列-命令介面，例如啟動`cmd.exe`Windows 節點或`/bin/sh`Linux 上︰

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    如果工作需要執行應用程式或指令碼的節點的不是`PATH`或參照環境變數，呼叫明確工作命令列中的命令介面。

- **資源檔案**包含處理資料。 這些檔案都會自動複製到節點從**一般用途**Azure 儲存體帳戶 Blob 儲存體之前執行工作的命令列。 如需詳細資訊，請參閱[啟動任務](#start-task)，然後[檔案與目錄](#files-and-directories)的章節。

- **環境變數**所需的應用程式。 如需詳細資訊，請參閱[工作環境設定](#environment-settings-for-tasks)] 區段。

- 要執行任務**限制式**。 保留的範例中，若要執行，應該重試失敗的工作，最大次數允許工作的最大時間和最大時間該任務的工作目錄中的檔案。

- 要部署至此的任務排程執行運算節點的**應用程式套件**。 [應用程式套件](#application-packages)提供簡化的部署及執行工作的應用程式的版本設定。 任務層級應用程式套件是在共用資料庫環境中，在一個資料庫，來執行不同工作及工作完成時，不會刪除資料庫位置尤其有用。 如果您的工作集區中有較少的工作，比節點，任務應用程式套件可以最小化的資料傳輸之後，才能執行工作的節點部署應用程式。

除了您所定義的工作可執行計算節點，下列特殊工作另外也提供批次服務︰

- [啟動工作](#start-task)
- [工作管理員工作](#job-manager-task)
- [準備和發行的工作](#job-preparation-and-release-tasks)
- [多個執行個體的工作 (MPI)](#multi-instance-tasks)
- [任務相依性](#task-dependencies)

### <a name="start-task"></a>啟動工作

建立關聯，以**開始工作**集區，您就可以準備作業環境的節點。 例如，您可以執行安裝的應用程式，執行您的工作，並啟動背景處理程序之類的動作。 開始任務每次執行節點啟動，它仍會保留在資料庫中，為包括節點時新增至資料庫及時，重新啟動或建立映像的。

開始工作的主要優點是資訊的它可以包含的所有設定運算節點並安裝所需執行工作的應用程式所需。 因此，增加的資料庫中的節點數目是非常簡單，指定新目標節點的次數，這批次已經有設定新的節點，並取得這些準備好進行接受工作所需的資訊。

使用任何 Azure 批次工作，您可以指定**資源檔案**的清單中[Azure 儲存體][azure_storage]，除了**命令列**執行。 批次先將資源檔案從 Azure 儲存體中，複製到節點，，然後執行命令。 集區開始任務的檔案清單通常包含任務應用程式及其相依性。

不過，它也可能包括運算節點執行的所有工作所使用的參考資料。 開始工作的命令列可執行，例如`robocopy`操作複製的[共用的資料夾](#files-and-directories)中，從開始工作的[工作目錄](#files-and-directories)應用程式的檔案 （已指定為資源檔案，及下載的節點），並執行 MSI 或`setup.exe`。

> [AZURE.IMPORTANT] 批次目前支援*只***一般用途**儲存的帳戶類型，[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)[的相關 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的步驟 5 中所述。 批次工作 （包括標準工作、 開始工作、 工作準備工作，以及版本的工作） 必須指定資源檔案*只*在**一般用途**儲存帳戶。

批次服務才能開始工作考慮準備好指派工作的節點之前，先完成時，請等候通常是最好，但您可以在此設定。

如果在運算節點，開始工作失敗，請再節點的狀態會更新以反映失敗，和節點不會指派有任何工作。 如果沒有複製投影片儲存空間，其資源檔案有問題，或由它的命令列執行的程序傳回的零的結束程式碼，可能會失敗開始工作。

如果您新增或更新*現有*的集區的 [開始] 任務時，您必須重新啟動任務，可套用至節點其運算節點。

### <a name="job-manager-task"></a>工作管理員工作

通常是使用**工作管理員工作**來控制及/或監視工作執行，例如，若要建立並提交工作的工作，判斷其他工作，而工作已完成時，決定。 不過，不會套用至這些活動工作管理員工作。 是的完整完備的工作，可以執行任何動作所需的工作。 比方說，工作管理員工作可能會下載參數所指定的檔案、 分析該檔案的內容並提交根據這些內容的其他工作。

所有其他工作之前開始工作管理員工作。 它會提供下列功能︰

- 它會自動提交工作批次服務時建立工作。

- 排定工作中的其他工作之前執行。

- 集區會被縮小時移除資料庫中的最後一個是其相關聯的節點。

- 在工作中的所有工作取消可以結合其終止。

- 工作管理員工作需要重新啟動時，會獲得最高優先順序。 如果閒置節點無法使用，可能會終止批次服務其中一項以騰出工作管理員工作，若要執行的資料庫中其他正在執行的工作。

- 工作管理員工作中的一個工作沒有在其他工作的工作的優先順序。 跨工作，被觀察只工作層級的優先順序。

### <a name="job-preparation-and-release-tasks"></a>準備和發行的工作

批次測試工作執行安裝程式提供工作的準備工作。 發行的工作是維護後的工作] 或 [清理]。

- **作業準備工作**︰ 執行工作，才能執行任何其他工作任務的排程的所有運算節點上執行的作業準備工作。 您可以使用共用的所有的任務，但卻是唯一的工作，例如的複製資料的作業準備工作。
- **工作釋出工作**︰ 執行至少有一個任務的集區中每個節點上完成工作後，執行工作釋出工作。 若要刪除的工作準備工作，也會複製資料，或將壓縮上, 傳診斷記錄資料，例如，您可以使用工作釋出工作。

同時工作準備和發行工作可讓您指定要執行叫用任務時的命令列。 提供檔案下載、 執行提高權限、 自訂環境變數，最大執行工期、 重試計數及檔案保留時間等功能。

準備和發行的工作的詳細資訊，請參閱[執行準備及完成的工作 Azure 批次運算節點](batch-job-prep-release.md)。

### <a name="multi-instance-task"></a>多個執行個體的工作

[多個執行個體的工作](batch-mpi.md)是設定為同時在多個運算節點上執行的工作。 多個執行個體工作後，您可以啟用高效能運算需要配置在一起處理 (例如郵件傳遞介面 (MPI)) 的單一工作負載的運算節點的群組。

在上執行 MPI 工作批次，使用批次.NET 文件庫的詳細說明，請參閱[使用多個執行個體執行的工作 Azure 批次中的郵件傳遞介面 (MPI) 應用程式](batch-mpi.md)。

### <a name="task-dependencies"></a>任務相依性

[任務相依性](batch-task-dependencies.md)，顧名思義，可讓您指定的工作取決於之前執行其他工作的完成。 此功能的 「 下游 」 任務會耗費輸出的 「 上游 」 任務，或 「 上游工作執行某些初始化所需的下游任務的情況下提供支援。 若要使用此功能，您必須先啟用任務相依性在您批次的工作。 然後，取決於另一個之每項工作 （或其他），指定工作的工作而定。

與任務相依性，您可以設定案例如下所示︰

* *taskB*取決於*taskA* （*taskB*執行才開始*taskA*已完成）。
* *taskC*取決於*taskA*和*taskB*。
* *taskD*而定範圍的工作，例如工作*1*到*10*，才能執行。

請參閱[Azure 批次中的任務相依性](batch-task-dependencies.md)和[TaskDependencies] [ github_sample_taskdeps] [azure-批次的範例]中的程式碼範例[github_samples]GitHub 存放庫，如需在您採取進階的詳細資訊這項功能。

## <a name="environment-settings-for-tasks"></a>任務的環境設定

批次服務所執行的每個任務具有存取環境變數，設定運算節點。 這包含批次服務來定義的環境變數 ([服務定義][msdn_env_vars]) 和自訂環境變數，您可以定義為任務。 應用程式和指令碼執行您的工作可存取這些環境變數執行期間。

您可以設定任務或工作層級的自訂環境變數來填入*環境設定*屬性的這些項目。 例如，請參閱[新增工作的工作][rest_add_task]作業 (批次 REST API) 或[CloudTask.EnvironmentSettings] [net_cloudtask_env]和[CloudJob.CommonEnvironmentSettings] [net_job_env]批次.NET 中的屬性。

用戶端應用程式或服務可以任務的環境變數，服務定義與自訂，請使用取得[取得工作的相關資訊][rest_get_task_info]作業 （批次剩餘） 或存取[CloudTask.EnvironmentSettings] [net_cloudtask_env]屬性 (批次.NET)。 在運算節點上執行的程序可以存取這些和節點，其他環境變數，例如使用熟悉的`%VARIABLE_NAME%`(Windows) 或`$VARIABLE_NAME`(Linux) 語法。

您可以找到所有服務定義環境變數的完整清單在[計算節點環境變數][msdn_env_vars]。

## <a name="files-and-directories"></a>檔案和目錄

每個任務的*工作目錄*要建立零或更多檔案和目錄。 這個工作目錄可用來儲存工作、 資料的處理，並執行處理的輸出執行的程式。 所有檔案和任務的目錄會使用者都擁有的工作。

批次服務的*根目錄*公開節點的檔案系統的一部分。 工作可以存取的根目錄參照`AZ_BATCH_NODE_ROOT_DIR`環境變數。 如需有關使用環境變數的詳細資訊，請參閱[環境設定為任務](#environment-settings-for-tasks)。

根目錄包含下列目錄結構︰

![計算節點目錄結構][1]

- **共用**︰ 此目錄提供節點上執行的*所有*任務的讀/寫權。 節點執行任何工作可以建立、 讀取、 更新及刪除此目錄中的檔案。 工作可以存取此目錄參照`AZ_BATCH_NODE_SHARED_DIR`環境變數。

- **啟動**︰ 此目錄會開始工作用來作為其工作目錄。 所有會下載至節點開始工作的檔案會儲存在此處。 開始工作可以建立、 讀取、 更新及刪除此目錄下的檔案。 工作可以參照來存取此目錄`AZ_BATCH_NODE_STARTUP_DIR`環境變數。

- **工作**︰ 建立目錄的節點上執行之每項工作。 透過參照`AZ_BATCH_TASK_DIR`環境變數。

    在每個任務的目錄，批次服務會建立工作目錄 (`wd`) 來指定唯一路徑的`AZ_BATCH_TASK_WORKING_DIR`環境變數。 此目錄提供給任務的讀/寫權。 工作可以建立、 讀取、 更新及刪除此目錄下的檔案。 此目錄，保留根據*RetentionTime*限制式所指定的工作。

    `stdout.txt`與`stderr.txt`︰ 這些檔案寫入期間執行工作的工作資料夾。

>[AZURE.IMPORTANT] 從資料庫移除節點，將會移除*所有*節點上儲存的檔案。

## <a name="application-packages"></a>應用程式套件

[應用程式套件](batch-application-packages.md)」 功能會提供輕鬆管理及部署的應用程式，以在您的集區運算節點。 您可以上傳並管理您的工作，包括其二進位檔案和支援的檔案，以執行應用程式的多個版本。 然後您可以自動部署一或多個運算節點這些應用程式在您的資料庫。

您可以指定資料庫與任務層級的應用程式套件。 當您指定資料庫應用程式套件時，應用程式集區中的每個節點部署。 當您指定工作應用程式套件時，只是要排程要執行的節點部署應用至少一個工作的工作，該任務的命令列執行之前。

批次控點來儲存您的應用程式套件及部署這些計算節點，因此可以簡化您的程式碼 」 和 「 管理負擔 Azure 儲存體所使用詳細的資料。

若要深入瞭解應用程式套件功能，請參閱[Azure 批次應用程式套件的應用程式部署](batch-application-packages.md)。

>[AZURE.NOTE] 如果您新增至*現有*的資料庫的資料庫應用程式套件，您必須重新啟動應用程式套件部署至節點其運算節點。

## <a name="pool-and-compute-node-lifetime"></a>集區和計算節點為單位的週期

當您設計 Azure 批次方案時，您必須決定設計與相關時集區在建立和多久計算這些集區中的節點會保留可用。

上一個結尾的範圍的詳細資訊，您可以建立您送出，每項工作的集區，並為其工作完成執行刪除資料庫。 這會最大化使用，因為有需要並關閉，只要在閒置時，只配置節點。 雖然這表示工作必須等候配置節點，請務必請注意，一旦節點個別可用配置，以及開始任務已完成的任務的排程執行。 批次並*不*等到所有資料庫中的節點可之前指派工作給節點。 這可確保所有可用的節點的最大使用量。

另一端的範圍，如果無法立即開始的工作是最高優先順序，您可以建立提前資料庫並提供其節點的工作會送出前。 在此案例中，可以立即開始工作，但節點可能坐閒置等待加以指定。

合併的方法通常用於處理變數，但持續進行載入。 您可以有很多個工作會送出，但可以不按比例縮放的節點向上或向下根據工作數目載入 （請參閱下節中的 [[縮放比例計算資源](#scaling-compute-resources)）。 您可以執行此動作疑問，根據目前負載或主動，如果載入可以預測。

## <a name="scaling-compute-resources"></a>縮放比例計算資源

使用[自動縮放比例](batch-automatic-scaling.md)，您可以讓批次服務動態調整運算根據目前的工作量和資源使用狀況，計算案例的資料庫中的節點數目。 這個選項可讓您降低藉由使用只在需要時，資源以及放開需要執行應用程式的整體成本。

您啟用撰寫[自動縮放比例的公式](batch-automatic-scaling.md#automatic-scaling-formulas)，該公式聯集區自動縮放比例。 批次服務會使用公式來決定目標的節點數目集區中的下一個縮放比例間隔 （您可以設定的時間間隔）。 當您建立，或啟用 [縮放比例，集區之後，您可以指定集區的自動縮放比例設定。 您也可以更新的縮放功能集區上的縮放比例設定。

例如，可能是工作需要送出大量要執行的工作。 您可以指定縮放比例公式調整為目前的數字的佇列工作及工作中的任務的完成率集區中的節點數目的集區。 批次服務定期評估公式，並調整大小的資料庫，根據工作負載 （加入節點許多佇列工作，並移除節點沒有佇列或執行任務） 與其他公式設定。

縮放比例公式可以依據下列指標︰

- **時間指標**會根據每 5 分鐘中指定時數統計資料。

- **資源單位**根據 CPU 使用狀況、 頻寬使用量、 記憶體使用量和節點的數字。

- **任務指標**會根據任務的狀態，例如 [*作用中*（佇列），以*執行*，或*已完成]*。

時自動縮放比例，將會減少計算資料庫中的節點數目，您必須考慮如何處理減少作業時執行的工作。 若要容納這種情況，批次會提供您可以在公式中包含*節點解除選項*。 例如，您可以指定的執行的工作會立即停止、 立即停止然後重新執行另一個節點，佇列或允許節點會從資料庫移除先完成。

如需有關自動調整應用程式的詳細資訊，請參閱[自動小數位數計算 Azure 批次資料庫中的節點](batch-automatic-scaling.md)。

> [AZURE.TIP] 若要最大化運算資源使用狀況，設定目標的節點數目零結尾的工作，但允許執行完成的工作。

## <a name="security-with-certificates"></a>安全性憑證

通常，您需要加密或解密機密資訊的工作，例如[Azure 儲存體帳戶]按鍵時使用的憑證[azure_storage]。 支援此功能，才能安裝節點的憑證。 加密的密碼會透過命令列參數傳遞給任務或內嵌在其中一個任務的資源，並可用於已安裝的憑證解密這些檔案。

您使用[的憑證新增][rest_add_cert]作業 （批次剩餘） 或[CertificateOperations.CreateCertificate] [net_create_cert]方法 (批次.NET) 將憑證新增至批次帳戶。 然後，您可以建立關聯到新的或現有資料庫的憑證。 聯集區憑證時，批次服務會集區中每個節點上安裝的憑證。 批次服務會安裝適當的憑證，節點啟動時，才會啟動任何任務 （包括開始工作與工作管理員任務）。

如果您將憑證新增至*現有*的資料庫時，您必須重新計算節點，可套用至節點的憑證。

## <a name="error-handling"></a>錯誤處理

您可能會發現必要處理您批次的方案中的工作和應用程式錯誤。

### <a name="task-failure-handling"></a>任務錯誤處理
工作失敗分成這些類別︰

- **排程失敗**

    如果工作指定的檔案傳輸失敗基於任何原因，請為工作設定 「 排程錯誤 」。

    排程錯誤可能是如果任務的資源檔案已移動，儲存帳戶不再提供使用，或時發生其他問題，導致無法成功複製檔案到節點。

- **應用程式失敗**

    指定工作的命令列的程序也會失敗。 程序會視為失敗時所執行的工作程序傳回零的結束代碼 （請參閱下一節中的*任務結束代碼*）。

    取得應用程式錯誤，您可以設定批次，自動重試任務截至指定的次數。

- **限制式失敗**

    您可以設定指定工作的工作， *maxWallClockTime*的最大執行期間的限制式。 這可能是很適合用於終止 「 無反應 」 工作。

    當已超過最大時間量時，工作標示為*完成*，但結束程式碼] 設定為`0xC000013A` *schedulingError*欄位，並且標示為`{ category:"ServerError", code="TaskEnded"}`。

### <a name="debugging-application-failures"></a>偵錯時，應用程式失敗

- `stderr`與`stdout`

    在執行時，應用程式可能會產生診斷輸出的可用來疑難排解問題。 批次服務[檔案和目錄](#files-and-directories)前一節所述，將標準輸出和標準誤差輸出`stdout.txt`和`stderr.txt`運算節點工作目錄中的檔案。 您可以使用 Azure 入口網站，或其中一個批次 Sdk，若要下載這些檔案。 例如，您可以在其中擷取這些和其他檔案以進行疑難排解使用[ComputeNode.GetNodeFile] [net_getfile_node]和[CloudTask.GetNodeFile] [net_getfile_task]批次.NET 文件庫中。

- **任務結束代碼**

    如先前所述，工作標示為批次服務失敗，如果處理程序所執行的工作會傳回的零的結束程式碼。 當任務執行的程序時，批次中填入任務的結束程式碼] 屬性與*傳回程序的程式碼*。 請務必請注意，任務的結束碼**不**由批次服務，它由本身的程序或作業系統執行的程序。

### <a name="accounting-for-task-failures-or-interruptions"></a>會計專用的工作失敗或中斷

有時候可能失敗的工作，或受到打擾。 任務應用程式本身可能會失敗，執行工作的節點可能會重新開機，或者節點可能會被移除資料庫中調整大小的作業期間如果集區解除原則設定若要立即移除節點，而不需等待完成的工作。 在所有的情況下，任務可以會自動重新佇列批次來執行另一個節點。

您也可針對間歇性發生問題導致停止回應或採取太多時間執行工作。 您可以設定任務的最大的執行時間。 如果超過，批次便會中斷工作應用程式。

### <a name="connecting-to-compute-nodes"></a>連線到運算節點

您可以執行其他的偵錯和疑難排解遠端登入運算節點。 若要下載 Windows 節點的遠端桌面通訊協定 (RDP) 檔案，並取得 Linux 節點的安全命令介面 (SSH) 連線資訊，您可以使用 [Azure 入口網站。 您也可以執行此批次 Api，例如，使用[批次.NET] [net_rdpfile]或[批次 Python](batch-linux-nodes.md#connect-to-linux-nodes)。

>[AZURE.IMPORTANT] 若要連線至透過 RDP 或 SSH 節點，您必須先建立使用者的節點。 若要這麼做，您可以使用 Azure 入口網站，[新增使用者帳戶以節點][rest_create_user]藉由使用批次 REST API，來電[ComputeNode.CreateComputeNodeUser] [net_create_user]方法批次.NET 或通話[add_user] [py_add_user]批次 Python 模組中的方法。

### <a name="troubleshooting-bad-compute-nodes"></a>疑難排解 」 錯誤 」 運算節點

在您的工作的一些失敗的位置的情況下，批次用戶端應用程式或服務可以檢查失敗的工作，以識別異常行為的節點的中繼資料。 集區中的每個節點會獲得唯一識別碼，並執行工作的節點所包含的任務的中繼資料。 當您識別出問題節點後，您可以採取幾個的動作︰

- **重新啟動節點**([REST][rest_reboot] | [.NET][net_reboot])

    重新啟動節點有時可以清除設定潛在問題，例如停留或損毀的程序。 請注意，是否您的資料庫使用開始任務或工作使用作業準備工作，他們會執行節點重新啟動時。

- **重新節點**([REST][rest_reimage] | [.NET][net_reimage])

    節點上重新安裝作業系統。 使用重新啟動節點，開始工作，工作準備工作會重新執行之後已建立映像的節點。

- **移除資料庫中的節點**([REST][rest_remove] | [.NET][net_remove])

    有時候就必須完全移除資料庫中的 [節點。

- **停用任務排程的節點**([REST][rest_offline] | [.NET][net_offline])

    此有效節點 [離線]，讓沒有進一步的工作指派給，但可讓維持節點執行和集區中。 這可讓您執行的失敗次數進一步調查原因而不遺失失敗的工作資料，並不節點造成其他的工作失敗。 例如，您可以停用任務排程的節點上，然後[從遠端登入](#connecting-to-compute-nodes)檢查節點的事件記錄檔，或執行其他疑難排解。 調查完畢之後，您就可以啟用任務的排程，然後將節點上線 ([其餘][rest_online] | [.NET][net_online])，或執行上述的動作。

> [AZURE.IMPORTANT] 與每個動作所述此區段中，重新啟動、 reimage、 移除和停用任務的排程，您就可以指定目前的節點上執行的工作時執行的動作的處理方式。 例如，當您停用任務的排程節點上使用批次.NET 用戶端文件庫，您可以指定[DisableComputeNodeSchedulingOption] [net_offline_option]列舉值，以指定是否要**終止**執行的工作， **Requeue**他們的其他節點上的排程或允許執行的工作完成，才能執行的動作 (**TaskCompletion**)。

## <a name="next-steps"></a>後續步驟

- 逐步執行逐步[開始使用.NET Azure 批次文件庫](batch-dotnet-get-started.md)中的範例批次應用程式。 此外，還有 Linux 運算節點上執行的工作負載的教學課程[Python 的版本](batch-python-tutorial.md)。

- 下載並建立[批次檔案總管][github_batchexplorer]用於開發批次方案時的範例專案。 使用 [批次總管] 中，您可以執行下列步驟及其他內容︰
  - 監控和管理資料庫、 工作和批次帳戶中的任務
  - 下載`stdout.txt`， `stderr.txt`，和其他檔案的節點
  - 建立使用者可在節點並下載 RDP 遠端登入的檔案

- 瞭解如何[建立資料庫的 Linux 運算節點](batch-linux-nodes.md)。

- 請造訪[Azure 批次論壇][ batch_forum] MSDN 上。 論壇是很好地方提出問題，您只要學習，或使用批次中是專家。

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
