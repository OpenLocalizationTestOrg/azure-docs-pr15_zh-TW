<properties
 pageTitle="雲端服務的大小 |Microsoft Azure"
 description="列出 Azure 雲端服務 web 及工作者角色不同的虛擬機器大小 （和識別碼）。"
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>雲端服務的大小

本主題說明的可用的大小和雲端服務角色執行個體 （web 角色及工作角色） 的選項。 同時也會提供時要注意的計劃使用這些資源的部署考量。 每個大小的識別碼會放在您的[服務定義檔案](cloud-services-model-and-package.md#csdef)。

雲端服務是其中一種類型的 Azure 所提供的計算資源。 按一下[這裡](cloud-services-choose-me.md)取得雲端服務的相關詳細資訊。

> [AZURE.NOTE]若要查看相關 Azure 限制，請參閱[Azure 訂閱及服務限制，配額和限制](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Web 及工作者角色執行個體的大小

有多個標準大小，來選擇從 Azure。 部分這些大小的考量因素包括︰

* D 數列 Vm 被用來執行要求較高的運算能力和暫時磁碟效能的應用程式。 D 數列 Vm 的暫存的磁碟，提供更快速的處理器，較高的記憶體-核心比例，solid-state 的磁碟機 (SSD)。 如需詳細資訊，請參閱 Azure 部落格[新 D 數列虛擬機器大小](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)上的公告。

* Dv2-系列，後續的原始的 D-系列功能更強大的 CPU。 Dv2 數列 CPU 是關於 35%的速度比 D 數列 CPU。 為基礎的最新的產生 2.4 GHz Intel Xeon® E5 2673 v3 (Haswell) 處理器，並利用 Intel 快速增量技術 2.0，可以移最 3.1 GHz。 Dv2 數列具有相同的記憶體和磁碟設定為 [D 系列。

*   G 數列 Vm 提供最多記憶體，並執行含有 Intel Xeon E5 V3 家庭處理器主機上。

*   在各種不同的硬體類型與處理器，可以部署的數列 Vm。 節流大小，根據硬體，提供執行的執行個體，無論部署的硬體一致的處理器效能。 若要判斷實體硬體大小部署時，查詢虛擬機器中的虛擬硬體。

*   A0 大小是在實體硬體過度訂閱。 僅限此特定大小，其他客戶部署可能會影響您執行的工作負載的效能。 相對效能下述為預期的比較基準，大約的變化，15%的主旨。


虛擬機器的大小會影響的價格。 大小也會影響虛擬機器處理、 記憶體和儲存空間容量。 儲存空間的成本會分別根據使用的頁面，在儲存帳戶計算。 如需詳細資訊，請參閱[虛擬機器定價詳細資料](https://azure.microsoft.com/pricing/details/virtual-machines/)和[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)。 


下列考量也許可以協助您決定使用哪種大小︰


* A8 A11 和 H 數列大小的又稱為是*運算密集執行個體*。 設計和最佳化運算密集執行這些大小的硬體和網路密切應用程式，包括高效能運算 (HPC) 叢集應用程式、 模型、 和模擬。 A8 A11 數列使用 Intel Xeon E5 2670 @ 2.6 GHZ 與 H 數列所用的 Intel Xeon E5 2667 v3 @ 3.2 GHz。 如需詳細的資訊與瞭解如何使用這些大小的考量，請參閱[H 數列和運算密集的數列 Vm](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)。 

* Dv2 系列，D 數列、 G 系列，非常適合需要更快速的 Cpu 的應用程式、 更好的本機磁碟效能，或有較高的記憶體需求。  提供許多企業級應用程式功能強大的組合。

*   Azure 資料中心的實體主機部分可能不支援更大的虛擬機器大小，例如 A5 – A11。 如此一來，您可能會看到的錯誤訊息**無法設定虛擬機器 {電腦名稱}**或**建立虛擬機器 {電腦名稱} 失敗**調整大小時現有的虛擬機器新的大小。2013 年 4 月 16 日; 之前所建立的虛擬網路中建立新的虛擬機器或將新的虛擬機器新增至現有的雲端服務。 請參閱[錯誤: 「 無法設定虛擬機器 」](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows)的每個部署案例的因應措施支援論壇上。  

* 您的訂閱也可能會限制您可以將部署中的特定大小系列的核心數目。 若要增加配額，請連絡 Azure 支援。


## <a name="performance-considerations"></a>考量效能

我們已建立的 Azure 計算單位 (ACU) 提供一種透過 Azure Sku 比較運算 (CPU) 效能的概念。 這將可協助您輕鬆找出 SKU 是最可能符合您的效能需求。  ACU 目前標準化上一個小型 (Standard_A1) 然後正在 100 及所有其他 Sku VM 代表大約如何更快速 SKU 可以執行的標準的基準。 

>[AZURE.IMPORTANT] ACU 是的指導方針。  您的工作量的結果可能會不同。 

<br>

|SKU 家族 |ACU/核心 |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5 7](#a-series) |100 |
|[A8 A11](#a-series)    |225 *|
|[D1 14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210-250 *|
|[G1 5](#g-series)  |180-240 *|
|[H](#h-series) |290-300 *|

標示 ACUs * 使用 Intel® 快速技術來提高 CPU 頻率，並提供的效能提升。  增量數量可能會根據虛擬記憶體大小、 工作量和其他主機上執行的工作負載。

## <a name="size-tables"></a>表格大小

下表顯示的大小和所提供的容量。

* 儲存容量會顯示在 [單位鉤或 1024年 ^3 個位元組。 當比較磁碟以 GB (1000年 ^3 個位元組) 以鉤磁碟 (1024年 ^3) 請記住，可能會出現小容量鉤中指定的數字。 例如，1023年鉤 = 1098.4 GB

* 以輸入輸出作業秒 (IOPS) 為單位的磁碟處理能力 mbps 位置 MBps = 10 ^6 個位元組秒。

* 在快取或無快取模式下資料磁碟可以運作。 快取的資料磁碟作業，主機快取模式已設為**唯讀**或**ReadWrite**。  無快取的資料磁碟作業，主機快取模式設定為 [**無**。

* 最大的網路頻寬是最大彙總的頻寬配置及指定每個 VM 類型。 最大的頻寬提供用於選取正確的 VM 類型，以確保適當的網路容量指導有。 移動時低、 中等、 最高及更高之間，處理量會根據這一點來增加。 實際網路效能許多因素包括網路和應用程式載入和應用程式的網路設定而定。


## <a name="a-series"></a>A 數列

| 大小        | CPU 核心 | 記憶體︰ 鉤 | 本機 HDD︰ 鉤 | 最大資料磁碟 | 最大資料磁碟處理能力︰ IOPS | 最大值 Nic / 網路頻寬 |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1/低                   |
| Standard_A1 | 1         | 1.75         | 70                    | 2              | 2 x 500              | 1/中等              |
| Standard_A2 | 2         | 3.5 GB       | 135                   | 4              | 4 x 500              | 1/中等              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2/高                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4/高                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1/中等              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2/高                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4/高                  |

## <a name="a-series---compute-intensive-instances"></a>A 數列-運算密集執行個體

如需使用這些大小的考量和資訊，請參閱[H 數列和運算密集的數列 Vm](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)。


| 大小         | CPU 核心 | 記憶體︰ 鉤 | 本機 HDD︰ 鉤 | 最大資料磁碟 | 最大資料磁碟處理能力︰ IOPS | 最大值 Nic / 網路頻寬 |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2/高                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4/更高             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2/高                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4/更高             |

* RDMA 簡訊

## <a name="d-series"></a>D 數列


| 大小         | CPU 核心 | 記憶體︰ 鉤 | 本機 SSD︰ 鉤 | 最大資料磁碟 | 最大資料磁碟處理能力︰ IOPS | 最大值 Nic / 網路頻寬 |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1/中等              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2/高                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4/高                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8/高                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2/高                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4/高                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8/高                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8/更高             |

## <a name="dv2-series"></a>Dv2 數列

| 大小            | CPU 核心 | 記憶體︰ 鉤 | 本機 SSD︰ 鉤 | 最大資料磁碟 | 最大資料磁碟處理能力︰ IOPS | 最大值 Nic / 網路頻寬 |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1/中等              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2/高                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4/高                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8/高                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8/極高        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2/高                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4/高                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8/高                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8/極高        |
| Standard_D15_v2 | 20        | 140          | 1000                | 40             | 40 x 500             | 8/極高        |

## <a name="g-series"></a>G 數列

| 大小        | CPU 核心 | 記憶體︰ 鉤  | 本機 SSD︰ 鉤  | 最大資料磁碟 | 最大值磁碟處理能力︰ IOPS | 最大值 Nic / 網路頻寬 |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1/高                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2/高                  |
| Standard_G3 | 8         | 112          | 1536                | 16             | 16 x 500           | 4/更高             |
| Standard_G4 | 16        | 224          | 3072                | 32             | 32 x 500           | 8/極高        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8/極高        |


## <a name="h-series"></a>H 數列

Azure H 數列虛擬機器是運算 Vm 瞄準高階運算需求，例如屬於分子模型，以及計算流暢 dynamics 下一個產生高效能。 這些 8 到 16 核心 Vm 是專 Intel Haswell E5 2667 V3 處理器技術內含 DDR4 記憶體和本機 SSD 基礎儲存空間。 

除了大量 CPU 的能力，H 數列會提供各種不同的選項，以使用 FDR InfiniBand 和數個記憶體組態來支援記憶體大量運算需求低延遲 RDMA 網路。


| 大小           | CPU 核心 | 記憶體︰ 鉤 | 本機 SSD︰ 鉤 | 最大資料磁碟 | 最大值磁碟處理能力︰ IOPS | 最大值 Nic / 網路頻寬 |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8/高                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8/更高                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8/高                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8/更高                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8/更高                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8/更高                  |


* RDMA 簡訊

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>附註︰ 標準 A0 A4 使用 CLI 和 PowerShell 

在傳統的部署模型，某些 VM 大小名稱是 CLI 和 PowerShell 稍有不同︰

* Standard_A0 是 ExtraSmall 
* Standard_A1 是小型企業
* 為 「 中 」 Standard_A2
* Standard_A3 是大
* Standard_A4 是 ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>設定大小的雲端服務

您可以指定一個角色執行個體的虛擬機器大小做為所描述的[服務定義檔案](cloud-services-model-and-package.md#csdef)的服務模型的一部分。 角色的大小會決定 CPU 核心、 記憶體容量和本機檔案系統大小配置給執行執行個體數目。 選擇根據您的應用程式資源需求的角色大小。

以下是設定為[Standard_D2](#general-purpose-d)網頁角色執行個體的角色大小的範例︰

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>後續步驟

- 深入了解[azure 訂閱及服務限制，配額和限制式](../azure-subscription-service-limits.md)。
- 瞭解更多[關於 H 數列和運算密集的數列 Vm](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)工作負載等高效能運算 (HPC)。

