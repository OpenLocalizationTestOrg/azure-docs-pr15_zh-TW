<properties
    pageTitle="對應的儲存空間 Azure 網站修復 HYPER-V 內部部署資料中心之間的虛擬機器複寫 |Microsoft Azure"
    description="準備 HYPER-V 虛擬機器複寫 Azure 網站復原的兩個內部部署資料中心之間的儲存空間對應。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>準備 HYPER-V 虛擬機器複寫 Azure 網站復原的兩個內部部署資料中心之間的儲存空間對應


Azure 網站復原佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 本文將說明儲存對應網站復原使用兩個內部部署 VMM 資料中心之間複製 HYPER-V 虛擬機器時，的有助於您進行最佳化使用的儲存空間。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="overview"></a>概觀

您複製 HYPER-V 虛擬機器中從主要的資料中心以使用 HYPER-V 複本或舊複寫，如下所示的次要資料中心的 VMM 雲朵] 時，才相關儲存對應︰


- **內部部署與 HYPER-V 複本的內部部署複寫)**— 您設定儲存對應的對應儲存分類來源及目標 VMM 伺服器，執行下列動作︰

    - **識別目標儲存複本虛擬機器**— 虛擬機器會複製到儲存目標 （SMB 共用或叢集共用的區 (CSVs)），您選擇。
    - **複本虛擬機器位置**— 儲存對應用來以最佳方式將 HYPER-V 主機伺服器上的 [複本的虛擬機器。 將可以存取對應的儲存空間分類的主機上放置複本虛擬機器。
    - **沒有儲存對應**，如果您沒有設定儲存對應，將會虛擬機器複寫複本虛擬機器相關聯的 HYPER-V 主機伺服器上指定的預設儲存位置。

- **內部部署與舊的內部部署複寫**— 您設定儲存對應的對應儲存陣列資料庫來源及目標 VMM 伺服器。
    - **指定資料庫**，指定哪些次要儲存集區接收主要資料庫複寫資料。
    - **識別目標儲存集區**，可確保來源複寫群組中的 Lun 會複製到您所選擇的對應的目標儲存集區。

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>設定 HYPER-V 複寫的儲存空間分類

當您使用的網站復原複寫 HYPER-V 複本時，您將對應之間來源及目標 VMM 伺服器或單一 VMM 伺服器上的儲存空間分類如果兩個網站受相同 VMM 伺服器。 請注意︰

- 當對應已正確設定，並已啟用複寫時，虛擬機器的虛擬硬碟主要位置會複製到對應的目標位置中的儲存空間。
- 儲存空間分類必須可供位於來源及目標雲朵主機群組。
- 分類不需要擁有相同的儲存空間。 例如，您可以將對應包含 SMB 共用可以包含 CSVs 目標分類來源分類。
- 瞭解如何[建立 VMM 中的儲存空間分類](https://technet.microsoft.com/library/gg610685.aspx)的更多。

## <a name="example"></a>範例

如果 VMM 中，當您選取的來源和目標 VMM 伺服器在儲存對應分類正確設定，則會顯示來源和目標分類。 以下是範例儲存檔案共用及分類組織紐約 」 和 「 芝加哥的兩個位置。

**位置** | **VMM 伺服器** | **檔案共用 （來源）** | **分類 （來源）** | **若要對應** | **檔案共用 （目標）**
---|---|--- |---|---|---
紐約 | VMM_Source| SourceShare1 | GOLD | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | 「 銀色 」 | SILVER_TARGET | TargetShare2
 | | SourceShare3 | 青銅 | BRONZE_TARGET | TargetShare3
芝加哥 | VMM_Target |  | GOLD_TARGET | 未對應 |
| | | SILVER_TARGET | 未對應 |
 | | | BRONZE_TARGET | 未對應

您想要設定這些**伺服器儲存空間**] 索引標籤上的網站復原入口網站的 [**資源**] 頁面中。

![設定儲存對應](./media/site-recovery-storage-mapping/storage-mapping1.png)

使用此範例中︰
- 當複本虛擬機器會建立任何的虛擬機器上金色儲存空間 (SourceShare1)，它會複製到 GOLD_TARGET 儲存空間 (TargetShare1)。
- 銀色儲存空間 (SourceShare2) 上的任何虛擬機器建立複本虛擬機器時，它會複製到 SILVER_TARGET (TargetShare2) 儲存空間，並等。

實際檔案共用區和其指派的分類 VMM 中會出現在下一步的螢幕擷取畫面。

![VMM 中的儲存空間分類](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>多個儲存位置

如果目標分類指派給多個 SMB 共用或 CSVs，虛擬機器在保護狀態時自動已選取的最佳的儲存位置。 如果不相符的目標儲存空間以指定分類，指定 HYPER-V 主機上的預設儲存位置用來將複本虛擬硬碟中。

下表顯示如何儲存分類和叢集共用區設定在我們的範例。

**位置** | **分類** | **相關聯的儲存空間**
---|---|---
紐約 | GOLD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | 「 銀色 」 | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
芝加哥 | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

下表摘要列出行為，當您啟用虛擬機器 (VM1-VM5) 在此範例環境中的保護。

**虛擬機器** | **來源儲存空間** | **來源分類** | **對應的目標儲存空間**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | GOLD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>兩個 GOLD_TARGET</p>
VM2 | \\FileServer\SourceShare1 | GOLD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>兩個 GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | 「 銀色 」 | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | 「 銀色 」 |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>兩個 SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | N/A | 沒有任何對應，因此會使用 HYPER-V 主機的預設儲存位置

## <a name="next-steps"></a>後續步驟

現在，您需要進一步了解儲存空間對應，[準備好要部署 Azure 網站復原](site-recovery-best-practices.md)。
