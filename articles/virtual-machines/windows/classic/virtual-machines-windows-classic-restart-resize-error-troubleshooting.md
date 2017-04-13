<properties
   pageTitle="VM 重新啟動，或調整大小的問題 |Microsoft Azure"
   description="疑難排解重新啟動，或調整大小現有的 Windows 虛擬機器中 Azure 傳統部署問題"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>疑難排解重新啟動，或調整大小現有的 Windows 虛擬機器中 Azure 傳統部署問題

> [AZURE.SELECTOR]
- [傳統](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
- [資源管理員](../../virtual-machines-windows-restart-resize-error-troubleshooting.md)

當您嘗試啟動停止 Azure 虛擬機器 (VM)，或調整大小現有的 Azure VM 時，您會遇到的常見錯誤是配置失敗。 當叢集或地區沒有可用的資源，或是無法支援要求的虛擬記憶體大小，就會產生這個錯誤。
> [AZURE.IMPORTANT] Azure 有兩種不同的部署模型建立及使用的資源︰[資源管理員] 及 [傳統](../../../resource-manager-deployment-model.md)。  本文涵蓋使用傳統的部署模型。 Microsoft 建議最新的部署使用資源管理員模型。

[AZURE.INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>收集的稽核記錄

若要開始進行疑難排解，收集稽核記錄，以找出問題與相關聯的錯誤。

Azure 入口網站中，按一下 [**瀏覽** > **虛擬機器** > _Windows 虛擬機器_ > **設定** > **稽核記錄**。

## <a name="issue-error-when-starting-a-stopped-vm"></a>啟動停止的 VM 時的問題︰ 錯誤

您想要開始停止的 VM 卻配置失敗。

### <a name="cause"></a>原因

停止的 VM 的邀請有嘗試在原始叢集裝載雲端服務。 不過，叢集沒有要求的可用空間。

### <a name="resolution"></a>解決方法

* 建立新的雲端服務，並將它與關聯其中一個區域或地區為基礎的虛擬網路，但不是相關性群組。

* 刪除已停止的 VM。

* 使用磁碟重新建立新的雲端服務中 VM。

* 開始重新建立的 VM。

如果嘗試建立新的雲端服務時，您會收到錯誤，請稍後再試一次，或變更雲端服務的區域。

> [AZURE.IMPORTANT] 新的雲端服務必須在新的名稱及 VIP，因此您必須變更現有的雲端服務中使用這項資訊的所有相依性的資訊。

## <a name="issue-error-when-resizing-an-existing-vm"></a>調整大小的現有 VM 問題︰ 錯誤

您想要調整大小的現有 VM 卻配置失敗。

### <a name="cause"></a>原因

若要調整大小 VM 要求有嘗試在原始叢集裝載雲端服務。 不過，叢集不支援要求的虛擬記憶體大小。

### <a name="resolution"></a>解決方法

減少要求的虛擬記憶體大小，並再試一次調整要求。

* 按一下 [**瀏覽所有** > **（傳統） 的虛擬機器** > _虛擬機器_ > **設定** > **大小**。 如需詳細步驟，請參閱[調整虛擬機器](https://msdn.microsoft.com/library/dn168976.aspx)。

如果不減少虛擬記憶體大小，請遵循下列步驟︰

  * 建立新的雲端服務，確保它不會連結至相關性群組，而不會連結至相關性群組的虛擬網路與相關聯。

  * 在其中建立新的大小較大的 VM。

您可以在同一個雲端服務中的所有您 Vm 合併彙算。 如果您現有的雲端服務與地區為基礎的虛擬網路相關聯，您可以連線到現有的虛擬網路的新的雲端服務。

如果不地區為基礎的虛擬網路與相關聯的現有的雲端服務，您必須從其磁碟新的雲端服務中工作表並刪除現有的雲端服務中, Vm。 不過，則一定要記住的新的雲端服務會有新的名稱及 VIP，因此您必須更新這些目前這項資訊使用現有的雲端服務的所有相依性。

## <a name="next-steps"></a>後續步驟

如果您遇到問題，當您建立新的 Windows VM Azure 中時，請參閱[建立新的 Windows 虛擬機器 Azure 中的疑難排解部署問題](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md)。
