<properties
 pageTitle="計算 Windows Vm 事先分數 |Microsoft Azure"
 description="執行 Windows Server Azure Vm 比較 SPECint 計算事先分數"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="cynthn"/>

# <a name="compute-benchmark-scores-for-windows-vms"></a>Windows Vm 計算事先分數

下列 SPECInt 事先分數顯示執行 Windows Server Azure 的高效能 VM 排列的計算效能。 計算事先分數也可供[Linux Vm](virtual-machines-linux-compute-benchmark-scores.md)。



## <a name="a-series---compute-intensive"></a>A 數列-運算密集


大小 | vCPUs | NUMA 節點 | CPU | 執行 | 平均基底工資率 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 236.1 | 1.1 （英文)
Standard_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 450.3 | 7.0
Standard_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 5 | 235.6 | 0.9
Standard_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz |7 | 454.7 | 4.8

## <a name="dv2-series"></a>Dv2 數列


大小 | vCPUs | NUMA 節點 | CPU | 執行 | 平均基底工資率 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_D1_v2 | 1 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 83 | 36.6 | 2.6
Standard_D2_v2 | 2 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 27 | 70.0 | 3.7 捨位
Standard_D3_v2 | 4 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 19 | 130.5 | 4.4
Standard_D4_v2 | 8 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 19 | 238.1 | 5.2
Standard_D5_v2 | 16 | 2 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 14 | 460.9 | 15.4
Standard_D11_v2 | 2 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 19 | 70.1 | 3.7 捨位
Standard_D12_v2 | 4 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 2 | 132.0 | 1.4
Standard_D13_v2 | 8 | 1 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 17 | 235.8 | 3.8
Standard_D14_v2 | 16 | 2 | Intel Xeon E5 2673 v3 @ 2.4 GHz | 15 | 460.8 | 6.5


## <a name="g-series-gs-series"></a>G 系列，GS 數列


大小 | vCPUs | NUMA 節點 | CPU | 執行 | 平均基底工資率 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_G1 Standard_GS1  | 2 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 31 | 71.8 | 6.5
Standard_G2 Standard_GS2 | 4 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 5 | 133.4 | 13.0
Standard_G3 Standard_GS3 | 8 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 6 | 242.3 | 6.0
Standard_G4 Standard_GS4 | 16 | 1 | Intel Xeon E5 2698B v3 @ 2 GHz | 15 | 398.9 | 6.0
Standard_G5 Standard_GS5 | 32 | 2 | Intel Xeon E5 2698B v3 @ 2 GHz | 22 | 762.8 | 3.7 捨位

## <a name="h-series"></a>H 數列

大小 | vCPUs | NUMA 節點 | CPU | 執行 | 反覆運算秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_H8 | 8 | 1 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 5 | 297.4 | 0.9
Standard_H16 | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 5 | 575.8 | 6.8
Standard_H8m | 8 | 1 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 5 | 297.0 | 1.2
Standard_H16m | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 5 | 572.2 | 3.9
Standard_H16r | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 5 | 573.2 | 2.9
Standard_H16mr | 16 | 2 | Intel Xeon E5 2667 v3 @ 3.2 GHz | 7 | 569.6 | 將 2.8 顯示

## <a name="about-specint"></a>關於 SPECint

在 Windows Server 上執行[SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html)已計算 Windows 數字。 SPECint 執行基本的工資率] 選項 (SPECint_rate2006)，使用核心每一份複本。 SPECint 組成 12 個別測試，每個執行三次，記的中位數的值，從每個測試加權這些表單複合分數。 跨多個 Vm 提供顯示平均成績再執行這些測試。


## <a name="next-steps"></a>後續步驟

* 儲存容量、 磁碟的詳細資訊，並選擇 VM 大小之間的其他考量，請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md)。
