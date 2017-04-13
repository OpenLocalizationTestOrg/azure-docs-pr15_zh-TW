<properties
   pageTitle="疑難排解 Linux VM 部署 RM |Microsoft Azure"
   description="當您建立新的 Linux 虛擬機器中 Azure 疑難排解資源管理員部署問題"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>建立新的 Linux 虛擬機器 Azure 中的資源管理員部署問題進行疑難排解

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>收集的稽核記錄

若要開始進行疑難排解，收集稽核記錄，以找出問題與相關聯的錯誤。 下列連結會包含在追蹤的程序的詳細的資訊。

[疑難排解 Azure 入口網站的資源群組部署](../resource-manager-troubleshoot-deployments-portal.md)

[稽核作業與資源管理員](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:**如果 OS Linux 一般化，則它是上傳及/或擷取與通用設定，然後就不會有任何錯誤。 同樣地，如果 OS Linux 專業，它是上傳及/或擷取與特定的設定，然後不會有任何錯誤。

**上傳錯誤︰**

**N<sup>1</sup>:**如果 OS Linux 一般化，並為上傳專業，您會收到佈建逾時錯誤，因為 VM 停滯在佈建階段。

**N<sup>2</sup>:**如果 OS Linux 專業，並為一般化上傳，則您會收到佈建失敗的錯誤，因為新 VM 正在使用原始的電腦名稱、 使用者名稱和密碼。

**解決方法︰**

若要解決兩個這些錯誤上, 傳原始 VHD，也就是使用內部部署，使用相同的設定值以 OS （一般化/專用）。 若要上傳一般化時，請執行-deprovision 第一次。

**擷取錯誤︰**

**N<sup>3</sup>:**如果 OS Linux 一般化，並以擷取特定，您會收到佈建逾時錯誤，因為原始 VM 不可用，因為它標示為一般化。

**N<sup>4</sup>:**如果 OS Linux 專業，並擷取為一般化，則您會收到佈建失敗的錯誤，因為新 VM 正在使用原始的電腦名稱、 使用者名稱和密碼。 此外，原始 VM 便無法使用因為已標示為特殊。

**解決方法︰**

若要解決兩個這些錯誤，刪除目前的影像的入口網站，[就從目前的 Vhd](virtual-machines-linux-capture-image.md)使用相同的設定值以 OS （一般化/專用）。

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>問題︰ 自訂 / 庫 / 服務商場圖像。配置錯誤
當新的 VM 邀請不支援要求，虛擬記憶體大小或是沒有可用的空間以容納要求的叢集現已釘選情況發生此錯誤。

**會導致 1:**叢集無法支援要求的虛擬記憶體大小。

**解決方法 1:**

- 使用較小的虛擬記憶體大小要求再試一次。
- 如果無法變更的要求 VM 大小︰
  - 停止可用性組中的所有 Vm。
  按一下 [**資源群組** > *資源群組* > **資源** > *設定您的顯示狀態* > **虛擬機器** > *虛擬機器* > **停止**。
  - 所有 Vm 都停止之後，建立新的 VM 中所要的大小。
  - 開始新的 VM 第一次，然後選取每個已停止 Vm 並按一下 [**開始]**。

**會導致 2:**叢集沒有可用的資源。

**解決方法 2:**

- 在稍後再次要求。
- 如果新 VM 可以是不同的顯示狀態集的一部分
  - 建立新的 VM 不同的可用性 （位於相同的地區） 設定。
  - 將相同的虛擬網路中的新 VM。

## <a name="next-steps"></a>後續步驟
如果您在啟動停止的 Linux VM 或調整大小 Azure 中現有 Linux VM 時遇到問題，請參閱[重新啟動，或調整大小現有的 Linux 虛擬機器中 Azure 疑難排解資源管理員部署問題](virtual-machines-linux-restart-resize-error-troubleshooting.md)。
