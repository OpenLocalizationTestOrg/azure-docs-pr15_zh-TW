<properties
   pageTitle="使用 [Windows 虛擬機器中的 [SAP |Microsoft Azure"
   description="瞭解如何使用 Microsoft Azure 中的 Windows 虛擬機器 (Vm) 上的 [SAP Lear"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>使用 [Windows Azure 虛擬機器中的 [SAP

雲端運算常用的字詞的最大且跨國公司的小型公司的 IT 產業內的多重要性取得。 Microsoft Azure 是雲端服務的平台向 Microsoft 提供的新的可能性。 現在客戶就能快速佈建和取消提供為雲端服務，應用程式，因此不會限制為技術或編列預算限制。 而非投入時間和預算硬體基礎結構，公司可以著重在應用程式、 商務程序與利益客戶和使用者。

從 Microsoft Azure 虛擬機器，Microsoft 會提供全面涵蓋所有內容的基礎結構服務 (IaaS) 平台。 SAP NetWeaver 基礎應用程式支援上 Azure 虛擬機器 (IaaS)。 白皮書以下說明如何規劃及實作 SAP NetWeaver 基礎的應用程式在 Windows Azure 虛擬機器上。 您也可以在[Linux 虛擬機器](virtual-machines-linux-classic-sap-get-started.md)上實作 SAP NetWeaver 基礎應用程式。

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver 上 Azure-HA

標題︰ SAP NetWeaver Azure-叢集 SAP ASCS/SCS 執行個體與 SIOS DataKeeper Azure 上使用 Windows Server 容錯移轉叢集上

摘要: 「 此文件將說明如何使用 SIOS DataKeeper Azure 中的可用性 SAP ASCS/SCS 組態設定。 SAP 保護其單一的 Windows Server 容錯移轉叢集設定需要共用的磁碟的失敗元件 SAP ASCS/SCS 或佇列中複製服務等。 這些 SAP 元件是不可或缺的 SAP 系統的功能。 因此高可用性功能需要放在 [位置]，確定這些元件可以維持伺服器或 VM 為已完成的 Windows 叢集答對金屬和 HYPER-V 環境的設定失敗。 2015 年 8 月到本身的 Azure 無法提供需要 Windows 版的共用的磁碟基礎高度可用下列重要的 SAP 元件所需的設定。 不過 DataKeeper SIOS 依產品的協助，可以建置 Windows Server 容錯移轉叢集組態的 SAP ASCS/SCS 視 Azure IaaS 平台上。 這本白皮書的步驟-步驟方法說明如何使用共用 SIOS Datakeeper Azure 中所提供的磁碟安裝 Windows Server 容錯移轉叢集設定。 紙張會位於 Azure、 Windows 和 SAP 讓可用性設定以最佳方式使用說明中設定的詳細資料。 紙張補充 SAP 安裝文件和 SAP 備忘稿表示安裝及部署的 SAP 軟體的主要資源上指定的平台。

更新︰ 年 8 月 2015 2015年

[立即下載本指南](http://go.microsoft.com/fwlink/?LinkId=613056)
