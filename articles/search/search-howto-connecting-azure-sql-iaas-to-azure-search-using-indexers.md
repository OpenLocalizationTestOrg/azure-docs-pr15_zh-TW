<properties 
    pageTitle="設定從 Azure 搜尋索引子連線至 SQL Server Azure 虛擬機器上 |Microsoft Azure |索引子" 
    description="啟用加密的連線，並設定防火牆，以允許連線至 SQL Server Azure 虛擬機器 (VM) 上 Azure 搜尋索引。" 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>設定從 Azure 搜尋索引子連線至 SQL Server Azure VM

如[要使用索引子 Azure 搜尋連線 Azure SQL 資料庫](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions)所述，建立索引子針對**SQL Server Azure Vm 上**（或**SQL Azure Vm**的簡稱） 支援由 Azure 搜尋，但有幾個安全性相關的先決條件負責的第一個。 

**任務工期︰**關於 30 分鐘，假設您已經安裝 VM 上的憑證。

## <a name="enable-encrypted-connections"></a>啟用加密的連線

Azure 搜尋所有重新要求都需要加密的通道，透過公用網際網路連線。 本節會列出進行這項工作的步驟。

1. 檢查內容的憑證驗證的主體名稱是 Azure VM 的完整的網域名稱 (FQDN)。 若要檢視的內容，您可以使用的工具，例如 CertUtils 或憑證嵌入式管理單元 」。 您可以從 VM 服務刀基本資訊] 區段的**公用 IP 位址/DNS 名稱標籤**中的欄位， [Azure 入口網站](https://portal.azure.com/)取得 FQDN。

    - 使用較新的**資源管理員**範本建立 Vm 的 FQDN 格式化為`<your-VM-name>.<region>.cloudapp.azure.com`。 

    - 建立**傳統**VM 舊版 Vm 的 FQDN 格式化為`<your-cloud-service-name.cloudapp.net>`。 

2. 設定 [SQL Server 使用憑證使用登錄編輯程式]。 

    雖然這個工作經常使用 SQL Server 組態管理員，您無法使用它這種情況。 它不會找出匯入的憑證，因為在 Azure VM 的 FQDN 不符合 FQDN 由 VM （它會識別為本機電腦或網路的網域就已加入網域）。 如果不相符的名稱，可用於 regedit 指定的憑證。

    - 在 regedit，瀏覽至下列登錄機碼︰ `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`。
     
    `[MSSQL13.MSSQLSERVER]`組件而有所不同版本及執行個體名稱。 

    - **憑證**金鑰的值設為**指紋**您匯入到 VM 的 SSL 憑證。

    有數種方式可取得指紋，某些較。 如果您將郵件複製從**憑證**嵌入式管理單元中，您可能挑選隱藏前置字元[的支援本文所述](https://support.microsoft.com/kb/2023869/)，當您嘗試連線，導致錯誤。 修正這個問題的有幾個因應措施。 最簡單的方法是指紋的按 backspace 鍵，然後再次輸入 regedit 的關鍵值欄位中移除的前置字元的第一個字元。 或者，您可以使用不同的工具來複製指紋。

3. 授與服務帳戶的權限。 

    請確定授與適當的權限私密金鑰的 SSL 憑證 SQL Server 服務帳戶。 如果您忽略此步驟中，不會啟動 SQL Server。 您可以使用的**憑證**嵌入式管理單元或**CertUtils**此工作。

4. 重新啟動 SQL Server 服務。

## <a name="configure-sql-server-connectivity-in-the-vm"></a>設定 VM 中的 SQL Server 連線能力

設定 Azure 搜尋所需的加密連線之後，有其他設定步驟內建到 SQL Server Azure Vm 上。 如果您尚未這麼做下, 一步是完成使用其中一個這些文件的設定︰

- **資源管理員**VM，請參閱[連線至 SQL Server 虛擬機器上 Azure 使用資源管理員](../virtual-machines/virtual-machines-windows-sql-connect.md)。 

- 如**傳統**VM，請參閱[連線至 SQL Server 虛擬機器上 Azure 傳統](../virtual-machines/virtual-machines-windows-classic-sql-connect.md)。

特別檢視中的 「 透過網際網路連線 「 每篇文章的一節。

## <a name="configure-the-network-security-group-nsg"></a>設定網路安全性群組 (NSG)

您無法異常設定，讓他人可以存取您 Azure VM NSG 和對應的 Azure 端點或存取控制清單 (ACL)。 有可能是您已完成此之前允許連線到您的 SQL Azure VM 應用程式邏輯。 是您的 SQL Azure vm Azure 搜尋連線無不同。 

下列連結提供指示 VM 部署 NSG 設定。 使用這些指示 ACL Azure 搜尋端點根據其 IP 位址。

> [AZURE.NOTE] 背景，請參閱[什麼是網路安全性群組？](../virtual-network/virtual-networks-nsg.md)

- **資源管理員**VM，請參閱[如何建立 NSGs ARM 部署](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。 

- **傳統**VM，請參閱[如何建立傳統部署 NSGs](../virtual-network/virtual-networks-create-nsg-classic-ps.md)。

IP 位址可能會造成幾個很容易解決，如果您知道的問題，可能的因應措施的挑戰。 剩餘的各節提供建議來處理 ACL 中的 IP 位址的相關問題。

#### <a name="restrict-access-to-the-search-service-ip-address"></a>限制存取至搜尋服務的 IP 位址

我們強烈建議您限制，而不是提供的任何連線要求寬開啟您的 SQL Azure Vm ACL 中您搜尋服務的 IP 位址的存取。 您可以輕鬆地瞭解的 IP 位址 ping FQDN (例如， `<your-search-service-name>.search.windows.net`) 的搜尋服務。

#### <a name="managing-ip-address-fluctuations"></a>管理 IP 位址變動

如果您的搜尋服務只有一個搜尋單位 （也就是一個複本和一個磁碟分割），將會常式服務會重新啟動，停用現有 ACL 搜尋服務的 IP 位址的期間變更的 IP 位址。

若要避免後續連線錯誤的其中一個方法是 Azure 在搜尋中使用多個複本，一個磁碟分割。 這樣會增加成本，但它也問題已解決 IP 位址。 在 Azure 搜尋，您有多個搜尋單位時，不變更 IP 位址。

第二個的方法是允許連線失敗，而重新設定中 NSG Acl。 平均，您可以預期若要變更每隔幾週的 IP 位址。 執行不常用的基準控制編製索引的客戶，此方法可能可行。

第三個固有 （但不是很安全） 的方法是指定您搜尋服務佈建後的位置 Azure 區域的 IP 位址範圍。 從 Azure 資源配置的公用 IP 位址的 IP 範圍清單刊登在[Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>包含 Azure 搜尋入口網站的 IP 位址

如果您使用 Azure 入口網站建立索引，Azure 搜尋入口網站邏輯也需要存取您的 SQL Azure VM 期間建立時間。 Azure 搜尋入口網站的 IP 位址可找到 ping `stamp2.search.ext.azure.com`。

## <a name="next-steps"></a>後續步驟

使用的方式設定，您現在可以指定 SQL Server Azure VM 上為 Azure 搜尋索引的資料來源。 如需詳細資訊，請參閱[Azure 搜尋使用索引子連線 Azure SQL 資料庫](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)。
