<properties
   pageTitle="Azure VM 備份失敗︰ 無法通訊與 VM 代理程式的快照集狀態-快照 VM 子任務逾 |Microsoft Azure"
   description="發生上述任何症狀原因和相關的 Azure VM 備份失敗的解決方案無法通訊與 VM 代理程式的快照集狀態。 快照 VM 子任務逾時錯誤"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="cfreeman"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jimpark; markgal;genli"/>

# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Azure VM 備份失敗︰ 無法通訊與 VM 代理程式的快照集狀態-快照 VM 子任務逾時

## <a name="summary"></a>摘要

註冊及排程備份 Azure 虛擬機器 (VM) 後, 備份 Azure 服務會啟動備份工作的時間點快照 VM 中副檔名為備份與通訊在排程的時間。 有可能會造成快照的條件觸發的潛在客戶以備份失敗問題。 本文提供 Azure VM 快照逾時錯誤相關的備份失敗的相關問題的疑難排解步驟。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>徵狀

Microsoft Azure 備份的基礎結構服務 (IaaS) VM 失敗，並傳回工作錯誤詳細資料，在[Azure 入口網站](https://portal.azure.com/)中的下列錯誤訊息︰

**無法與 VM 代理程式的快照集狀態通訊-快照 VM 子任務逾時。**

## <a name="cause"></a>原因
有四個這項錯誤的常見原因︰

- VM 沒有存取網際網路。
- 安裝在 VM Microsoft Azure VM 代理程式是最新的 （適用於 Linux Vm)。
- 備份副檔名無法更新或載入。
- 無法擷取快照狀態或無法取得的快照。

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>原因 1: VM 不能存取網際網路
部署需求，每 VM 沒有網際網路存取權，或在禁止存取 Azure 基礎結構的限制。

備份擴充功能需要連線至 Azure 的公用 IP 位址正確運作。 這是因為其傳送給 Azure 儲存體端點 (HTTP URL) 若要管理的 VM 快照的命令。 如果副檔名為沒有公用網際網路存取權，備份最後會失敗。

### <a name="solution"></a>解決方案
在此案例中，使用下列方法之一來解決問題︰

- Whitelist Azure 資料中心 IP 範圍
- 建立路徑以 HTTP 的流量

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Whitelist Azure 資料中心的 IP 範圍

1. 取得要 whitelisted [Azure 資料中心 Ip 的清單](https://www.microsoft.com/download/details.aspx?id=41653)。
2. 使用新增 NetRoute 指令程式來解除封鎖 Ip。 在 Azure VM 提高權限 （以系統管理員身分執行） PowerShell 視窗中執行這個指令程式。
3. 如果您有允許存取 IPs，新增規則網路安全性群組 (NSG)。

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>若要建立 HTTP 流量的路徑

1. 如果您有網路限制的位置 (例如，NSG) 時，部署若要將流量路由傳送 HTTP proxy 伺服器。
2. 如果您有網路安全性群組 (NSG)，新增規則，允許從 HTTP proxy 存取網際網路。

瞭解如何[設定 [HTTP proxy] 的 VM 備份](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)。

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2: VM 中已安裝 Microsoft Azure VM 代理程式是最新的 （適用於 Linux Vm)

### <a name="solution"></a>解決方案
針對 Linux Vm 最代理程式與或副檔名相關的失敗次數造成的影響舊 VM 代理程式的問題。 為一般指導方針，若要解決此問題的第一個步驟所示︰

1. [安裝最新的 Azure VM 代理程式](https://github.com/Azure/WALinuxAgent)。
2. 請確定 Azure 代理程式正在執行 VM 上。 若要這麼做，請執行下列命令︰```ps -e```

    如果不執行此程序，使用下列命令，重新啟動它。

    針對 Ubuntu:```service walinuxagent start```

    其他散發: '' 服務 waagent 開始
```

3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.

    We require the following logs from the customer’s VM:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:

    Enable verbose logging (y|n)

2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension

1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| ----- | ----- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. | Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. | If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. | It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. | If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
|The VM cannot get host/fabric address from DHCP.|DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).|
