<properties
    pageTitle="建立多個虛擬機器 |Microsoft Azure"
    description="建立多個虛擬機器 Windows 上的選項"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>建立多個 Azure 虛擬機器

有許多情況下建立大量類似的虛擬機器 (Vm) 的所需的位置。 部分範例包括高效能運算 (HPC)、 大型資料分析，可調整和通常無中間層] 或 [後端伺服器 （例如 webservers) 及分散式的資料庫。

本文討論若要建立多個 Vm Azure 中可用的選項。 手動建立一系列的 Vm 簡單的情況下可以試試這些選項。 若要建立多個 Vm，如果您需要建立超過少數幾個 Vm 不縮放程序，您通常會使用。

若要建立多個類似 Vm 的其中一個方法是使用的_資源迴圈_Azure 資源管理員建構。

## <a name="resource-loops"></a>資源迴圈

資源迴圈是 Azure 資源管理員範本中的語法速記法。 資源迴圈可以建立一組同樣設定資源循環。 您可以使用資源迴圈，若要建立多個儲存帳戶、 網路介面或虛擬機器。 如需有關資源迴圈的詳細資訊，請參閱[建立 Vm，在可用性設定為使用資源迴圈](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/)。

## <a name="challenges-of-scale"></a>縮放比例的挑戰

雖然資源迴圈讓您更容易建置在雲端基礎架構，並產生更簡潔的範本，但會保留特定的挑戰。 例如，如果您要建立 100 的虛擬機器使用資源循環播放，您需要建立網路介面控制器 (Nic) 對應 Vm 與儲存帳戶之間的關係。 因為 Vm 數很可能儲存帳戶的數目不同，則必須太處理不同的資源循環播放大小。 以下是可解決的問題，但複雜度大幅增加小數位數。

當您需要調整 elastically 基礎結構時，就會發生另一個的挑戰。 例如，您可能會想自動調整大小基礎結構，自動增加或減少工作量來回應 Vm 數。 Vm 不提供在數字 （延展和中） 整合式的機制。 如果您不按比例縮放中移除 Vm，很難保證高的顯示狀態，以確保 Vm 均等更新與錯誤的網域。

最後，當您使用的資源循環播放，若要建立資源的多個電話會移至基礎結構。 多個呼叫建立類似的資源，Azure 有隱含商機改善此設計並最佳化部署可靠性與效能。 這是_虛擬機器縮放比例設定_來源位置。

## <a name="virtual-machine-scale-sets"></a>虛擬機器縮放設定

虛擬機器比例集是 Azure 計算資源部署及管理相同 Vm 一組。 使用所有 Vm 設定集很容易中調整及擴充的相同，VM 刻度。 您只要變更 Vm 的數字，在設定。 您也可以設定 VM 比例集，以根據之工作負載的要求的自動調整大小。

為需要計算資源看]，並在不按比例縮放的應用程式，比例作業會隱含地均等發生錯誤，並更新的網域。

而非相互關聯 「 多個的資源，例如 Nic 和 Vm，VM 縮放比例設定有網路、 儲存、 虛擬機器和擴充屬性，集中，您可以設定。

VM 比例組簡介資訊，請參閱的[虛擬機器縮放比例設定產品頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。 如需詳細資訊，移至[虛擬機器縮放比例設定文件](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)。
