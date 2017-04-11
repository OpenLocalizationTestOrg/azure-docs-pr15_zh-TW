<properties
   pageTitle="疑難排解 Linux VM 部署傳統 |Microsoft Azure"
   description="當您建立新的 Linux 虛擬機器中 Azure 疑難排解傳統部署問題"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>建立新的 Linux 虛擬機器中 Azure 傳統部署問題進行疑難排解

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>收集的稽核記錄

若要開始進行疑難排解，收集稽核記錄，以找出問題與相關聯的錯誤。

Azure 入口網站中，按一下 [**瀏覽** > **虛擬機器** > *Windows 虛擬機器* > **設定** > **稽核記錄**。

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:**如果 OS Linux 一般化，則它是上傳及/或擷取與通用設定，然後就不會有任何錯誤。 同樣地，如果 OS Linux 專業，它是上傳及/或擷取與特定的設定，然後不會有任何錯誤。

**上傳錯誤︰**

**N<sup>1</sup>:**如果 OS Linux 一般化，並為上傳專業，您會收到佈建逾時錯誤，因為 VM 停滯在佈建階段。

**N<sup>2</sup>:**如果 OS Linux 專業，並為一般化上傳，則您會收到佈建失敗的錯誤，因為新 VM 正在使用原始的電腦名稱、 使用者名稱和密碼。

**解決方法︰**

若要解決兩個這些錯誤上, 傳原始 VHD，也就是使用內部部署，使用相同的設定值以 OS （一般化/專用）。 若要上傳一般化時，請執行-deprovision 第一次。 如需詳細資訊，請參閱[建立及上傳虛擬硬碟包含下列的 Linux 作業系統](virtual-machines-linux-classic-create-upload-vhd.md)。

**擷取錯誤︰**

**N<sup>3</sup>:**如果 OS Linux 一般化，並以擷取特定，您會收到佈建逾時錯誤，因為原始 VM 不可用，因為它標示為一般化。

**N<sup>4</sup>:**如果 OS Linux 專業，並擷取為一般化，則您會收到佈建失敗的錯誤，因為新 VM 正在使用原始的電腦名稱、 使用者名稱和密碼。 此外，原始 VM 便無法使用因為已標示為特殊。

**解決方法︰**

若要解決兩個這些錯誤，刪除目前的影像的入口網站，[就從目前的 Vhd](virtual-machines-linux-classic-capture-image.md)使用相同的設定值以 OS （一般化/專用）。

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>問題︰ 自訂 / 庫 / 服務商場圖像。配置失敗
當新的 VM 邀請傳送至叢集，沒有可用的空間以容納邀請，或是無法支援要求虛擬記憶體大小的情況下發生此錯誤。 無法混合安裝不同系列 Vm 相同的雲端服務中。 因此，如果您想要建立新的不同大小超過您的雲端服務可支援 VM，計算要求將會失敗。

根據您用來建立新的 VM 雲端服務的限制式，您可能會遇到所造成的兩種情況下其中一項錯誤。

**會導致 1:**雲端服務已釘選到特定叢集，或已連結至相關性] 群組中，因此釘選到特定叢集的設計。 因此，新的計算資源要求的相關性群組會嘗試在同一個叢集裝載的現有的資源。 不過，相同叢集可能不支援要求的虛擬記憶體大小或有可用空間不足，並產生配置錯誤。 下列情況成立時在新的雲端服務透過或現有的雲端服務，是否建立新的資源。

**解決方法 1:**

- 建立新的雲端服務，並與一個區域或地區為基礎的虛擬網路產生關聯。
- 建立新的雲端服務中的新 VM。
  如果嘗試建立新的雲端服務時，您會收到錯誤，請稍後再試一次，或變更雲端服務的區域。

> [AZURE.IMPORTANT] 如果您嘗試建立新的 VM 現有的雲端服務中，但無法，且必須建立您的新 VM 的新雲端服務，您可以選擇相同的雲端服務中的所有您 Vm 合併彙算。 若要這麼做，請刪除現有的雲端服務中, Vm 並隊伍先從新的雲端服務中的磁碟。 不過，則一定要記住的新的雲端服務會有新的名稱及 VIP，因此您必須更新這些目前這項資訊使用現有的雲端服務的所有相依性。

**會導致 2:**雲端服務是會連結至相關性] 群組中，所以它現已釘選到特定叢集所設計的虛擬網路與相關聯。 所有新的計算資源要求的相關性群組會因此嘗試在同一個叢集裝載的現有的資源。 不過，相同叢集可能不支援要求的虛擬記憶體大小或有可用空間不足，並產生配置錯誤。 下列情況成立時在新的雲端服務透過或現有的雲端服務，是否建立新的資源。

**解決方法 2:**

- 建立新的地區虛擬網路。
- 建立新的虛擬網路中的新 VM。
- [您現有的虛擬網路連線](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)到新的虛擬網路。 請參閱深入瞭解[地區的虛擬網路](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。 或者，您可以[移轉到地區虛擬網路，您相關性群組為基礎的虛擬網路](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)，然後建立新的 VM。

## <a name="next-steps"></a>後續步驟
如果您遇到問題，當您啟動停止的 Linux VM 或現有的 Linux VM Azure 中的調整大小，請參閱[重新啟動，或調整大小現有的 Linux 虛擬機器中 Azure 疑難排解傳統部署問題](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)。
