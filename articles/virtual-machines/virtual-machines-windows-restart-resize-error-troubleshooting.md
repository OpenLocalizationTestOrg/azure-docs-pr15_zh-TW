<properties
   pageTitle="VM 重新啟動，或調整大小的問題 |Microsoft Azure"
   description="重新啟動，或調整大小現有的 Windows 虛擬機器中 Azure 資源管理員部署問題的疑難排解"
   services="virtual-machines-windows, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>重新啟動，或調整大小現有的 Windows 虛擬機器中 Azure 資源管理員部署問題的疑難排解

當您嘗試啟動停止 Azure 虛擬機器 (VM)，或調整大小現有的 Azure VM 時，您會遇到的常見錯誤是配置失敗。 當叢集或地區沒有可用的資源，或是無法支援要求的虛擬記憶體大小，就會產生這個錯誤。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>收集的稽核記錄

若要開始進行疑難排解，收集稽核記錄，以找出問題與相關聯的錯誤。 下列連結會包含程序的詳細的資訊︰

[Azure 入口網站與疑難排解資源群組部署](../resource-manager-troubleshoot-deployments-portal.md)

[稽核作業與資源管理員](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>啟動停止的 VM 時的問題︰ 錯誤

您想要開始停止的 VM 卻配置失敗。

### <a name="cause"></a>原因

停止的 VM 的邀請有嘗試在原始叢集裝載雲端服務。 不過，叢集沒有要求的可用空間。

### <a name="resolution"></a>解決方法

*   停止在可用設定，所有 Vm，然後重新啟動每個 VM。

  1. 按一下 [**資源群組** > _資源群組_ > **資源** > _設定您的顯示狀態_ > **虛擬機器** > _虛擬機器_ > **停止**。

  2. 所有 Vm 都停止之後，請選取每個已停止 Vm，然後按一下 [開始]。

*   在稍後再次重新啟動要求。

## <a name="issue-error-when-resizing-an-existing-vm"></a>調整大小的現有 VM 問題︰ 錯誤

您想要調整大小的現有 VM 卻配置失敗。

### <a name="cause"></a>原因

若要調整大小 VM 要求有嘗試在原始叢集裝載雲端服務。 不過，叢集不支援要求的虛擬記憶體大小。

### <a name="resolution"></a>解決方法

* 使用較小的虛擬記憶體大小要求再試一次。

* 如果無法變更的要求 VM 大小︰

  1. 停止可用性組中的所有 Vm。

    * 按一下 [**資源群組** > _資源群組_ > **資源** > _設定您的顯示狀態_ > **虛擬機器** > _虛擬機器_ > **停止**。

  2. 所有 Vm 都停止之後，調整大小所要的大小放大 VM。
  3. 選取 [調整的 VM 並按一下 [**開始**]，再啟動每個已停止 Vm。

## <a name="next-steps"></a>後續步驟

如果您遇到問題，當您建立新的 Windows VM Azure 中時，請參閱[建立新的 Windows 虛擬機器 Azure 中的疑難排解部署問題](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)。
