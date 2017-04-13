<properties
 pageTitle="計算 Linux Vm 事先分數 |Microsoft Azure"
 description="比較 Azure Vm 執行 Linux CoreMark 計算事先分數"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="cynthn"/>

# <a name="compute-benchmark-scores-for-linux-vms"></a>計算 Linux Vm 事先分數

下列 CoreMark 事先分數顯示執行 Ubuntu Azure 的高效能 VM 排列的計算效能。 計算事先分數也可供[Windows Vm](virtual-machines-windows-compute-benchmark-scores.md)。




## <a name="a-series---compute-intensive"></a>A 數列-運算密集


大小 | vCPUs | NUMA 節點 | CPU | 執行 | 反覆運算秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 179 | 110,294 | 554
Standard_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 189 | 210,816| 2,126
Standard_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 110,025 | 1,045
Standard_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 210,727| 2,073

## <a name="dv2-series"></a>Dv2 數列


大小 | vCPUs | NUMA 節點 | CPU | 執行 | 反覆運算秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_D1_v2 | 1 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 140 | 14,852 | 780
Standard_D2_v2 | 2 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 133 | 29,467 | 1,863
Standard_D3_v2 | 4 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 139 | 56,205 | 1,167
Standard_D4_v2 | 8 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 126 | 108,543 | 3,446
Standard_D5_v2 | 16 | 2 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 126 | 205,332 | 9,998
Standard_D11_v2 | 2 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 125 | 28,598 | 1,510
Standard_D12_v2 | 4 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 131 | 55,673 | 1,418
Standard_D13_v2 | 8 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 140 | 107,986 | 3,089
Standard_D14_v2 | 16 | 2 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 140 | 208,186 | 8,839
Standard_D15_v2 | 20 | 2 | Intel Xeon E5 2673 v3 @ 2.4 GHz |28 | 268,560 | 4,667

## <a name="f-series"></a>F 數列

大小 | vCPUs | NUMA 節點 | CPU | 執行 | 反覆運算秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_F1 | 1 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 154 | 15,602 | 787
Standard_F2 | 2 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 126 | 29,519 | 1,233
Standard_F4 | 4 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 147 | 58,709 | 1,227
Standard_F8 | 8 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 224 | 112,772 | 3,006
Standard_F16 | 16 | 2 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 42 | 218,571 | 5,113



## <a name="g-series"></a>G 數列


大小 | vCPUs | NUMA 節點 | CPU | 執行 | 反覆運算秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_G1 | 2 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 83 | 31,310 | 2,891
Standard_G2 | 4 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 84 | 60,112 | 3,537
Standard_G3 | 8 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 84 | 107,522 | 4,537
Standard_G4 | 16 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 83 | 195,116 | 5,024
Standard_G5 | 32 | 2 | Intel Xeon E5 2698B v3 @ 2 GHz | 84 | 360,329 | 14,212

## <a name="gs-series"></a>GS 數列


大小 | vCPUs | NUMA 節點 | CPU | 執行 | 反覆運算秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_GS1 | 2 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 84 | 28,613 | 1,884
Standard_GS2 | 4 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 83 | 54,348 | 3,474
Standard_GS3 | 8 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 83 | 104,564 | 1,834
Standard_GS4 | 16 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 84 | 194,111 | 4,735
Standard_GS5 | 32 | 2 | Intel Xeon E5 2698B v3 @ 2 GHz | 84 | 357,396 | 16,228


## <a name="h-series"></a>H 數列

大小 | vCPUs | NUMA 節點 | CPU | 執行 | 反覆運算秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_H8 | 8 | 1 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 28 | 140,782 | 2,512
Standard_H16 | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 35 | 275,289 | 7,110 
Standard_H18m | 8 | 1 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 28 | 139,071 | 3,988 
Standard_H16m | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 28 | 275,988 | 6,963 
Standard_H16r | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 28 | 273,982 | 6,069 
Standard_H16mr | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 28 | 274,523 | 5,698 



## <a name="about-coremark"></a>關於 CoreMark

已計算 Ubuntu 上執行[CoreMark](http://www.eembc.org/coremark/faq.php) Linux 數字。 CoreMark 設定設為虛擬 Cpu 數的數字和並行設為 PThreads。 目標重複次數所提供的 （通常是更長的時間） 20 秒至少執行階段預期的效能，根據調整大小。 最後的分數表示重複次數完成除以執行測試所花的秒數。 每個測試在每個 VM 上執行至少有 7 次。 測試 （除了的 H series_ 在 2015 年 10 月中執行的每個 Azure 公用區域中的多個 Vm VM 所支援上執行的日期。

## <a name="next-steps"></a>後續步驟



* 儲存容量、 磁碟的詳細資訊，並選擇 VM 大小之間的其他考量，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要在 Linux Vm 執行 CoreMark 指令碼，下載的[CoreMark 指令碼套件](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)。