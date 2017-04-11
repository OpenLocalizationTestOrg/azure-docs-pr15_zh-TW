<properties
    pageTitle="Azure DPM 備份簡介 |Microsoft Azure"
    description="若要備份 DPM 伺服器使用 Azure 備份服務簡介"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="系統管理中心資料保護管理員，資料保護管理員，dpm 備份"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>準備備份 Azure DPM 與負載

> [AZURE.SELECTOR]
- [Azure 備份伺服器](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure 備份伺服器 （傳統）](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM （傳統）](backup-azure-dpm-introduction-classic.md)


本文提供使用 Microsoft Azure 備份來保護您的系統管理中心資料保護管理員 (DPM) 伺服器和工作負載的簡介。 藉由讀取，您將瞭解︰

- Azure DPM 伺服器備份的運作方式
- 為了要備份順暢先決條件
- 遇到的常見錯誤，以及如何處理它們
- 支援的案例

系統管理中心 DPM 備份檔案和應用程式的資料。 可以儲存在磁碟上、 膠帶貼上或備份到 Microsoft Azure 備份 Azure dpm 備份資料。 DPM 進行互動 Azure 備份，如下所示︰

- **DPM 部署為實體伺服器或內部部署虛擬機器**— 如果 DPM 部署為實體伺服器或磁碟和膠帶貼上除了 Azure 備份保存庫來備份資料內部部署 HYPER-V 虛擬機器備份。
- **部署為 Azure 虛擬機器 DPM** ，從系統管理中心 2012 R2 更新 3，可以部署 DPM 為 Azure 虛擬機器。 如果 DPM 部署為 Azure 磁碟備份資料 Azure 虛擬機器附加至 DPM Azure 虛擬機器中，或您可以由其備份到 Azure 備份保存庫卸資料儲存區。

## <a name="why-backup-your-dpm-servers"></a>為什麼要備份您 DPM 伺服器？

使用 Azure 備份備份 DPM 伺服器商務有哪些優點包括︰

- 內部部署 DPM 部署，您可以使用長期部署膠帶貼上到另一種 Azure 備份。
- DPM Azure 中的部署，Azure 備份可讓您卸載儲存從 Azure 磁碟空間，讓您可以將較舊的資料儲存在 Azure 備份與磁碟上的新資料來縮放。

## <a name="how-does-dpm-server-backup-work"></a>DPM 伺服器備份如何運作？
若要備份虛擬機器，第一次資料的時間點快照需要。 在排程的時間，起始備份工作及觸發快照副檔名為備份的 Azure 備份的服務。 副檔名為備份協調達成的一致性，在來賓 VSS 服務，並叫用 Azure 儲存體服務的 blob 快照 API，一旦達到一致性。 這是以取得一致的虛擬機器，磁碟的快照，而不必將它關閉。

快照集後，資料會以備份保存庫傳送備份 Azure 服務。 服務負責識別並轉接已從備份儲存與網路進行有效率的最後一個備份的區塊。 資料傳輸完成時，會移除快照，並建立復原點。 Azure 傳統入口網站中可以看到此復原點。

>[AZURE.NOTE] Linux 虛擬機器，有可能是只有檔案一致的備份。

## <a name="prerequisites"></a>必要條件
準備 Azure 備份，DPM 資料，如下所示︰

1. **建立備份保存庫**，在 Azure 備份主控台建立保存庫。
2. **下載保存庫認證**，在 Azure 備份上, 傳您建立的管理憑證至保存庫。
3. **Azure 備份代理程式安裝並註冊伺服器**，從 Azure 備份，每個 DPM 伺服器上安裝代理程式，並在備份保存庫註冊 DPM 伺服器。

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>需求 （和限制）

- DPM 實體伺服器或 HYPER-V 虛擬機器安裝在系統管理中心 2012 SP1 或系統管理中心 2012 R2 上執行。 也可以執行為至少系統管理中心 2012 R2 上執行 Azure 虛擬機器 DPM 2012 R2 更新彙總套件 3年或 Windows 虛擬機器中至少系統管理中心 2012 R2 上執行的 VMWare 更新彙總套件 5。
- 如果您執行的使用系統管理中心 2012 SP1 DPM，您應該安裝更新彙總套件 2 系統管理中心資料保護管理員 sp1。 您可以安裝 Azure 備份代理程式之前，這是必要。
- DPM 伺服器應有 Windows PowerShell 和.Net Framework 4.5 安裝。
- DPM 可以備份 Azure 備份大部分的工作量。 有哪些具有受支援，請參閱的完整清單 Azure 備份支援以下項目。
- Azure 備份中儲存的資料無法復原 」 複製到膠帶貼上] 選項。
- 您必須 Azure 帳戶啟用 Azure 備份項功能。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 閱讀有關[Azure 備份價格](https://azure.microsoft.com/pricing/details/backup/)。
- 使用 Azure 備份需要 Azure 備份代理程式安裝在您想要備份的伺服器上。 每個伺服器必須至少 10%正在備份，提供本機的免費儲存空間的資料的大小。 例如，備份 100 GB 的資料需要的 10 GB 的可用空間的草稿的位置。 10%的最小值時，建議您的本機存放空間可用於快取位置 15%。
- 資料會儲存在 Azure 保存庫的儲存位置。 您可以備份到 Azure 備份地窖的資料量不受限制，但資料來源 （例如虛擬機器或資料庫） 的大小不超過 54,400 GB。

備份到 Azure 可支援這些檔案類型︰

- 加密 （僅限完整備份）
- 壓縮 （支援增量備份）
- 疏鬆 （支援增量備份）
- 壓縮且疏鬆 （視為 Sparse）

然後，這些不受支援︰

- 不支援在區分大小寫檔案系統中的伺服器。
- 永久的連結 （已略過）
- 重新分析點 （已略過）
- 加密與壓縮 （已略過）
- 加密並疏鬆 （略過）
- 壓縮的資料流
- 疏鬆串流

>[AZURE.NOTE] 從在系統管理中心 2012 DPM sp1 這時候開始，您可以備份設定受 DPM Azure 使用 Microsoft Azure 備份的工作量。
