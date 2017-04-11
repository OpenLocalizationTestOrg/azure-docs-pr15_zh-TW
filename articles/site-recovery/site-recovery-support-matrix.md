<properties
    pageTitle="Azure 網站復原支援矩陣 |Microsoft Azure"
    description="Azure 網站復原摘要支援的作業系統和元件"
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
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="azure-site-recovery-support-matrix"></a>Azure 網站復原支援矩陣圖

這份文件摘要列出支援的作業系統和 Azure 網站復原部署元件。 針對每個部署案例有支援的元件和先決條件的清單中每個對應的部署文件，與此文件摘要列出它們。

## <a name="supported-operating-systems-for-virtualization-servers"></a>支援的作業系統的虛擬伺服器


**來源** | **目標** | **主機作業系統**
---|---|---|--- 
**（不含 VMM) HYPER-V 主機** | Azure | Windows Server 2012 R2 使用最新的更新
**HYPER-V 主機 （含 VMM)** | Azure | Windows Server 2012 R2 使用最新的更新
**HYPER-V 主機 （含 VMM)** | 次要 VMM 網站 | 將 at 至少 Windows Server 2012 的最新的更新
**VMware 主機/vCenter** | Azure | vCenter 5.5 或 6.0 （5.5 功能僅支援） <br/><br/> vSphere 6.0、 5.5 或 5.1 的最新的更新
**VMware 主機/vCenter** | 次要 VMware 網站 | vCenter 5.5 或 6.0 （5.5 功能僅支援） <br/><br/> vSphere 6.0、 5.5 或 5.1 的最新的更新

## <a name="supported-requirements-for-replicated-machines"></a>支援的需求複寫機器

**來源** | **什麼被複寫** | **目標** | **主機作業系統**
---|---|---|--- 
**HYPER-V Vm** | 任何工作負載 | Azure | 任何來賓 OS， [Azure 支援](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Vm 必須符合[Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**HYPER-V Vm （含 VMM)** | 任何工作負載 | Azure | 任何來賓 OS， [Azure 支援](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Vm 必須符合[Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**HYPER-V Vm （含 VMM)** | 任何工作負載 | 次要 VMM 網站 | 任何來賓 OS，[支援 HYPER-V](https://technet.microsoft.com/library/mt126277.aspx)
**VMware Vm** | 在 Windows VM 上執行任何工作負載 | Azure | 64 位元 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 R2，至少 SP1<br/><br/> Vm 必須符合[Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware Vm** | Linux VM 上執行任何工作負載 | Azure | 紅色的角色企業 Linux 6.7，7.1、 7.2 <br/><br/> Centos 6.5，6.6 6.7、 7.0、 7.1、 7.2 <br/><br/> Oracle 企業 Linux 6.4，6.5 執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存空間︰ 檔案系統 （EXT3 ETX4、 ReiserFS、 XFS）;多重路徑軟體裝置 （多重路徑） 的對應程式）。大量管理員: (LVM2)。 不支援使用 HP CCISS 控制器儲存空間的實體伺服器。 只有 SUSE Linux Enterprise Server 11 SP3 支援 ReiserFS 檔案系統。<br/><br/> Vm 必須符合[Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware Vm** | 在 Windows VM 上執行任何工作負載 | 次要 VMware 網站 | 64 位元 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 R2，至少 SP1
**VMware Vm** | Linux VM 上執行任何工作負載 | 次要 VMware 網站 | 紅色的角色企業 Linux 6.7，7.1、 7.2 <br/><br/> Centos 6.5，6.6 6.7、 7.0、 7.1、 7.2 <br/><br/> Oracle 企業 Linux 6.4，6.5 執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存空間︰ 檔案系統 （EXT3 ETX4、 ReiserFS、 XFS）;多重路徑軟體裝置 （多重路徑） 的對應程式）。大量管理員: (LVM2)。 不支援使用 HP CCISS 控制器儲存空間的實體伺服器。 只有 SUSE Linux Enterprise Server 11 SP3 支援 ReiserFS 檔案系統。
**實體伺服器** | 在 Windows 上執行任何工作負載 | Azure | 64 位元 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 使用在最 SP1
**實體伺服器** | Linux 上執行任何工作負載 | Azure | 紅色的角色企業 Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5、 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle 企業 Linux 6.4，6.5 執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存空間︰ 檔案系統 （EXT3 ETX4、 ReiserFS、 XFS）;多重路徑軟體裝置 （多重路徑） 的對應程式）。大量管理員: (LVM2)。 不支援使用 HP CCISS 控制器儲存空間的實體伺服器。 只有 SUSE Linux Enterprise Server 11 SP3 支援 ReiserFS 檔案系統。
**實體伺服器** | 在 Windows 上執行任何工作負載 | 次要網站 | 64 位元 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 使用在最 SP1
**實體伺服器** | Linux 上執行任何工作負載 | 次要網站 | 紅色的角色企業 Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5、 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle 企業 Linux 6.4，6.5 執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 所需的儲存空間︰ 檔案系統 （EXT3 ETX4、 ReiserFS、 XFS）;多重路徑軟體裝置 （多重路徑） 的對應程式）。大量管理員: (LVM2)。 不支援使用 HP CCISS 控制器儲存空間的實體伺服器。 只有 SUSE Linux Enterprise Server 11 SP3 支援 ReiserFS 檔案系統。


## <a name="provider-versions"></a>提供者版本

**名稱** | **描述** | **最新版本** | **支援** | **詳細資料**
---|---|---|---| ---
**Azure 網站修復提供者** | 協調內部部署伺服器和 Azure/次要網站之間的通訊 <br/><br/> 如果沒有 VMM 伺服器內部部署 VMM 伺服器或 HYPER-V 伺服器上安裝 | 5.1.1700 （從入口網站提供） | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**Azure 網站復原整合設定 (VMware 至 Azure)** | 協調內部部署 VMware 伺服器和 Azure 之間的通訊 <br/><br/> 在內部部署 VMware 伺服器上安裝 | 9.3.4246.1 （可從入口網站） | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 內部部署 VMware 伺服器/實體伺服器和 Azure/次要網站之間的座標複寫 | NA （可從入口網站） | 每個 VMware VM 或您想要複製的實體伺服器上安裝。 **Microsoft Azure 復原服務 （火星） 代理程式** | 協調 HYPER-V Vm 和 Azure 之間的複寫<br/><br/> 在內部部署 （含或不 VMM 伺服器） HYPER-V 伺服器上安裝 | 2.0.8689.0 （可從入口網站） | 此代理程式會使用 Azure 網站復原和 Azure 備份）。 [瞭解更多](https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>後續步驟

[準備進行部署](site-recovery-best-practices.md)

