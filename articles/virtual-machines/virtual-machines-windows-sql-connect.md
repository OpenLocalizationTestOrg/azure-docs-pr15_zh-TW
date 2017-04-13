<properties
    pageTitle="連線至 SQL Server 虛擬機器 （資源管理員） |Microsoft Azure"
    description="瞭解如何連線至 SQL Server 中 Azure 虛擬機器上執行。 本主題會使用傳統的部署模型。 案例取決於網路的設定，以及用戶端的位置。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>連線至 SQL Server 虛擬機器上 Azure （資源管理員）

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-connect.md)
- [傳統](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>概觀

本主題說明如何連線至 SQL Server 執行個體 Azure 虛擬機器上執行。 它會說明某些[一般連線案例](#connection-scenarios)，並提供[詳細的步驟，設定 Azure VM 中的 SQL Server 連線](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。 若要檢視精簡版本的本文，請參閱[連線至 SQL Server 虛擬機器上 Azure 傳統](virtual-machines-windows-classic-sql-connect.md)。

如果您希望佈建與連線的完整逐步解說，請參閱[佈建 Azure SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

## <a name="connection-scenarios"></a>連線案例

用戶端連線到虛擬機器上執行的 SQL Server 的方式，會用戶端和電腦/網路設定的位置。 這些情況包括︰

- [透過網際網路連線至 SQL Server](#connect-to-sql-server-over-the-internet)
- [在相同的虛擬網路中連線至 SQL Server](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>透過網際網路連線至 SQL Server

如果您想要從網際網路連線至您的 SQL Server 資料庫引擎，有幾個步驟所需，例如設定防火牆、 啟用 SQL 驗證及設定您的網路安全性群組，您必須在網路安全性群組規則，允許 TCP 連接埠 1433年的流量。

如果您使用入口網站與資源管理員提供的 SQL Server 虛擬機器圖像時，當您選取 [**公用**SQL 連線選項完成這些步驟您︰

![期間佈建公用 SQL 連線選項](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

如果這不是在期間佈建，然後您可以手動設定 SQL Server 與您的虛擬機器依照下列[步驟手動設定連線的本文中](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)。

>[AZURE.NOTE] SQL Server Express edition 的虛擬機器圖像不會自動啟用 TCP/IP 通訊協定。 Express 版本，您必須以[手動方式啟用 TCP/IP 通訊協定](#configure-sql-server-to-listen-on-the-tcp-protocol)使用 SQL Server 組態管理員建立 VM 之後。

完成之後，存取網際網路的任何用戶端可以連線至 SQL Server 執行個體，藉由指定 [虛擬機器中的公用 IP 位址或指派給該 IP 位址的 [DNS] 標籤。 SQL Server 連接埠是 1433年，如果您不需要連線字串中加以指定。

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

這會用戶端的連線啟用在網際網路上時，雖然這不表示任何人都可以連線至 SQL Server。 外部用戶端均正確的使用者名稱和密碼。 如需額外的安全性，您可以避免已知的連接埠 1433年。 例如，如果您設定為接聽連接埠 1500年的 SQL Server，並建立適當的防火牆，網路安全性群組規則，您無法連線將連接埠號碼附加至的伺服器名稱，如下列範例所示︰

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] 請務必請注意，當您進行通訊的 SQL Server Azure 資料中心的所有外寄資料受到標準[價格輸出資料傳輸上](https://azure.microsoft.com/pricing/details/data-transfers/)使用此技巧。

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>在相同的虛擬網路中連線至 SQL Server

[虛擬網路](../virtual-network/virtual-networks-overview.md)可讓其他案例。 即使這些 Vm 存在於不同的資源群組中，您可以在相同的虛擬網路連線 Vm。 與[網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)，您可以建立與內部部署網路和電腦連線 Vm 混合式架構。

虛擬網路也可讓您加入您的 Azure Vm 網域。 這是使用 SQL server 的 Windows 驗證的唯一方法。 其他連線案例需要 SQL 驗證的使用者名稱和密碼。

如果您使用入口網站與資源管理員提供的 SQL Server 虛擬機器圖像時，虛擬網路上通訊的防火牆規則會是設定，當您選取 [**私人**SQL 連線選項。 如果這不是在期間佈建，然後您可以手動設定 SQL Server 與您的虛擬機器依照下列[步驟手動設定連線的本文中](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)。 但如果您打算設定的網域環境和 Windows 驗證，您不需要使用本文中的步驟，設定 SQL 驗證與登入。 您也不需要在網際網路上設定的存取權的網路安全性群組規則。

>[AZURE.NOTE] SQL Server Express edition 的虛擬機器圖像不會自動啟用 TCP/IP 通訊協定。 Express 版本，您必須以[手動方式啟用 TCP/IP 通訊協定](#configure-sql-server-to-listen-on-the-tcp-protocol)使用 SQL Server 組態管理員建立 VM 之後。

假設您已設定 DNS 虛擬網路中，您可以連線至您的 SQL Server 執行個體來指定連線字串中的 SQL Server VM 電腦名稱。 下列範例也假設也設定 Windows 驗證的使用者擁有已授與存取權的 SQL Server 執行個體。

    "Server=mysqlvm;Integrated Security=true"

請注意，在此案例中，您也可以指定 VM 的 IP 位址。

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>手動設定 Azure VM 中的 [SQL Server 連線的步驟

下列步驟將示範如何手動安裝 [連線至 SQL Server 執行個體，然後您也可以選擇透過網際網路使用 SQL Server 管理 Studio (SSMS)。 請注意，在入口網站中選取適當的 SQL Server 連線選項時，為您完成的步驟執行。

您可以從另一個 VM 或網際網路連線至 SQL Server 執行個體之前，您必須完成下列工作，請依照下列各節所述︰

- [在 [Windows 防火牆開啟 TCP 連接埠](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [設定接聽 TCP 通訊協定的 SQL Server](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [設定混合的模式驗證的 SQL Server](#configure-sql-server-for-mixed-mode-authentication)
- [建立 SQL Server 驗證登入](#create-sql-server-authentication-logins)
- [設定網路安全性群組輸入的規則](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [設定 DNS 標籤的公用 IP 位址](#configure-a-dns-label-for-the-public-ip-address)
- [從另一部電腦連線至資料庫引擎](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>後續步驟

若要查看佈建以及連線步驟的指示，請參閱[佈建 Azure SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

SQL server Azure 虛擬機器上[探索學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)。

執行 SQL Server Azure Vm 中其他主題，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。
