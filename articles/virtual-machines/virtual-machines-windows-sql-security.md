<properties
    pageTitle="Azure 中的 SQL Server 的安全性考量 |Microsoft Azure"
    description="本主題資源的標準部署模型，以建立參照，並提供保護執行 Azure 虛擬機器中的 SQL Server 的一般指導方針。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
   editor=""    
   tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Azure 虛擬機器中的 SQL Server 的安全性考量
 
本主題包括中 Azure VM 建立安全存取 SQL Server 執行個體的整體安全性指導方針。 不過，若要確保您的 SQL Server 資料庫執行個體中 Azure 更好的保護，建議您實作 Azure 的傳統的內部部署安全性作法，以及安全性最佳作法。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


如需有關 SQL Server 的安全性作法的詳細資訊，請參閱[SQL Server 2008 R2 安全性最佳作法-作業及管理工作](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure 符合幾個業界法規及標準，可讓您執行的虛擬機器中的 SQL server 建置相容的解決方案。 使用 Azure 法規遵循的相關資訊，請參閱[Azure 信任中心]](https://azure.microsoft.com/support/trust-center/)。

下列是安全性的建議，請務必考慮設定及連線至 Azure VM 中的 SQL Server 執行個體的清單。

## <a name="considerations-for-managing-accounts"></a>管理帳戶的考量事項︰

- 建立唯一的本機系統管理員帳戶名稱不是**系統管理員**。

- 使用您的帳戶複雜的強式密碼。 如需有關如何建立強式密碼，請參閱[建立強式密碼的提示](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password)文件。

- 根據預設，Azure 會選取 Windows 驗證，在 SQL Server 虛擬機器安裝期間。 因此， **SA**登入] 會停用，並安裝程式指派密碼。 我們建議您應該**SA**登入無法使用或啟用。 如果想要的 SQL 登入，下列是不同的策略︰
    - 建立具有系統管理員的成員資格的 SQL 帳戶。
    - 如果您必須使用**SA**登入，啟用登入並將其重新命名並指定新密碼。
    - 之前所提到的兩個選項都需要變更驗證模式以**SQL Server 和 Windows 驗證模式**。 如需詳細資訊，請參閱[變更伺服器驗證模式](https://msdn.microsoft.com/library/ms188670.aspx)。

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>設定 Azure 虛擬機器連接的安全性考量︰

- 請考慮使用[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)管理虛擬機器，而不是公用 RDP 連接埠。

- 使用[網路安全性群組](../virtual-network/virtual-networks-nsg.md)(NSG) 若要允許或拒絕您的虛擬機器網路流量。 如果您想要使用 NSG 和端點 ACL 已經在的位置，請先移除端點 ACL。 有關如何進行此動作，請參閱[管理存取控制清單 (Acl) 使用 PowerShell 端點](../virtual-network/virtual-networks-acl-powershell.md)。

- 如果您使用的 「 結束點 」，移除虛擬機器上的任何端點，如果您不使用它們。 使用 Acl 具有端點的指示，請參閱[管理端點的 ACL](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint)。

- SQL Server 資料庫引擎 Azure 虛擬機器中的商務連絡人的執行個體中啟用的加密的連線選項。 設定 SQL server 執行個體的已簽署的憑證。 如需詳細資訊，請參閱[啟用加密連線至資料庫引擎](https://msdn.microsoft.com/library/ms191192.aspx)及[連接字串語法](https://msdn.microsoft.com/library/ms254500.aspx)。

- 如果您的虛擬機器應僅從特定的網路存取，使用 [Windows 防火牆存取，限制特定 IP 位址或網路子網路。

## <a name="next-steps"></a>後續步驟

如果您是也感興趣的效能的最佳做法，請參閱[效能的 SQL Server Azure 虛擬機器中的商務連絡人的最佳作法](virtual-machines-windows-sql-performance.md)。

執行 SQL Server Azure Vm 中其他主題，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
