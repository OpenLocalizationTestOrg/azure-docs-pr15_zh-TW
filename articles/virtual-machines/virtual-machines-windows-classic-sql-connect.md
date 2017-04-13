<properties
    pageTitle="連線至 SQL Server 虛擬機器 （傳統） |Microsoft Azure"
    description="瞭解如何連線至 SQL Server 中 Azure 虛擬機器上執行。 本主題會使用傳統的部署模型。 案例取決於網路的設定，以及用戶端的位置。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>連線至 SQL Server 虛擬機器上 Azure （傳統部署）

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-connect.md)
- [傳統](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>概觀

本主題說明如何連線至 SQL Server 執行個體 Azure 虛擬機器上執行。 它會說明某些[一般連線案例](#connection-scenarios)，並提供[詳細的步驟，設定 Azure VM 中的 SQL Server 連線](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]如果您使用的資源管理員 Vm，請參閱[連線至 SQL Server 虛擬機器上 Azure 使用資源管理員](virtual-machines-windows-sql-connect.md)。

## <a name="connection-scenarios"></a>連線案例

用戶端連線到虛擬機器上執行的 SQL Server 的方式，會用戶端和電腦/網路設定的位置。 這些情況包括︰

- [在相同的雲端服務中連線至 SQL Server](#connect-to-sql-server-in-the-same-cloud-service)
- [透過網際網路連線至 SQL Server](#connect-to-sql-server-over-the-internet)
- [在相同的虛擬網路中連線至 SQL Server](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] 您使用任何一種方法連線之前，您必須遵循的[步驟以設定連線的這篇文章](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>在相同的雲端服務中連線至 SQL Server

可以建立多個虛擬機器中相同的雲端服務。 若要瞭解這個虛擬機器案例中，請參閱[如何連線虛擬網路或雲端服務的虛擬機器](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service)。 這種情況時，嘗試連線至 SQL Server 執行相同的雲端服務中的另一個虛擬機器上的上一個虛擬機器的用戶端。

在此案例中，您可以使用連線 VM**名稱**（也顯示為**電腦名稱**或在入口網站**主機名稱**）。 這是您 vm 建立期間所提供的名稱。 例如，如果您將 SQL VM **mysqlvm**，相同的雲端服務中的用戶端 VM 可以使用下列的連接字串連線︰

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>透過網際網路連線至 SQL Server

如果您想要從網際網路連線至您的 SQL Server 資料庫引擎，您必須建立連入 TCP 通訊的虛擬機器的端點。 這項 Azure 設定步驟，會指示 TCP 連接埠虛擬機器可存取的內送的 TCP 連接埠流量。

若要透過網際網路連線，您必須使用 VM DNS 名稱和 VM 端點連接埠號碼 （本文稍後的設定）。 若要尋找 [DNS 名稱，瀏覽 Azure 入口網站，然後選取 [ **（傳統） 的虛擬機器**。 然後選取您的虛擬機器。 **DNS 名稱**會顯示在**概觀**一節。

例如，請考慮傳統的虛擬機器命名**mysqlvm** **mysqlvm7777.cloudapp.net** DNS 名稱與**57500**的 VM 結束點。 假設經妥善設定的連線，下列連接字串可從任何位置存取虛擬機器在網際網路上︰

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

這會用戶端的連線啟用在網際網路上時，雖然這不表示任何人都可以連線至 SQL Server。 外部用戶端均正確的使用者名稱和密碼。 如需額外的安全性，不使用已知的連接埠 1433年公用虛擬機器端點。 如果可能的話，請考慮加入 ACL 和在您允許您只是，用戶端限制流量的端點上。 使用 Acl 具有端點的指示，請參閱[管理端點的 ACL](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint)。

>[AZURE.NOTE] 請務必請注意，當您進行通訊的 SQL Server Azure 資料中心的所有外寄資料受到標準[價格輸出資料傳輸上](https://azure.microsoft.com/pricing/details/data-transfers/)使用此技巧。

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>在相同的虛擬網路中連線至 SQL Server

[虛擬網路](../virtual-network/virtual-networks-overview.md)可讓其他案例。 您可以在相同的虛擬網路中，連線 Vm，即使這些 Vm 存在於中不同的雲端服務。 與[網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)，您可以建立與內部部署網路和電腦連線 Vm 混合式架構。

虛擬網路也可讓您加入您的 Azure Vm 網域。 這是使用 SQL server 的 Windows 驗證的唯一方法。 其他連線案例需要 SQL 驗證的使用者名稱和密碼。

如果您要設定的網域環境和 Windows 驗證，您不需要使用本文中的步驟，設定公用端點或 SQL 驗證及登入。 在此案例中，您可以指定連線字串中的 SQL Server VM 名稱連線至您的 SQL Server 執行個體。 下列範例會假設也設定 Windows 驗證的使用者擁有已授與存取權的 SQL Server 執行個體。

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>設定連線 SQL Server Azure VM 中的步驟

下列步驟說明如何使用 SQL Server 管理 Studio (SSMS)，透過網際網路連線至 SQL Server 執行個體。 不過，相同的步驟適用於進行可用於執行兩個內部部署在您的應用程式和 Azure 中存取您的 SQL Server 虛擬機器。

您可以從另一個 VM 或網際網路連線至 SQL Server 執行個體之前，您必須完成下列工作，請依照下列各節所述︰

- [建立虛擬機器 TCP 端點](#create-a-tcp-endpoint-for-the-virtual-machine)
- [在 [Windows 防火牆開啟 TCP 連接埠](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [設定接聽 TCP 通訊協定的 SQL Server](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [設定混合的模式驗證的 SQL Server](#configure-sql-server-for-mixed-mode-authentication)
- [建立 SQL Server 驗證登入](#create-sql-server-authentication-logins)
- [判定虛擬機器 DNS 的名稱](#determine-the-dns-name-of-the-virtual-machine)
- [從另一部電腦連線至資料庫引擎](#connect-to-the-database-engine-from-another-computer)

下圖會彙總連線路徑︰

![連線至 SQL Server 虛擬機器](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>後續步驟

如果您打算可用性和損毀修復使用 AlwaysOn 可用性群組，您應該考慮實作接聽程式。 資料庫用戶端連線到接聽項，而不是直接到其中一個 SQL Server 執行個體。 接聽路由傳送至 [可用性] 群組中的主要複本的用戶端。 如需詳細資訊，請參閱[設定 AlwaysOn Azure 中的可用性群組 ILB 接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)。

請務必 Azure 虛擬機器上執行的 SQL server 中檢閱所有的安全性的最佳作法。 如需詳細資訊，請參閱[SQL Server Azure 虛擬機器中的商務連絡人的安全性考量](virtual-machines-windows-sql-security.md)。

SQL server Azure 虛擬機器上[探索學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)。 

執行 SQL Server Azure Vm 中其他主題，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。
