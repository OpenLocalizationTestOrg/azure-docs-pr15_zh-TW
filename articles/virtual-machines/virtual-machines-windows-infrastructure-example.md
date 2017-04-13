<properties
    pageTitle="範例基礎結構的逐步解說 |Microsoft Azure"
    description="深入了解部署 Azure 中的範例基礎結構的重要的設計及實作方針。"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="example-azure-infrastructure-walkthrough"></a>範例 Azure 基礎結構的逐步解說

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

本文將引導建置出範例應用程式基礎結構。 我們詳細設計結合所有的指導方針和命名慣例與可用性集、 虛擬網路負載平衡器、 決策簡單線上市集基礎結構和實際部署虛擬機器 (Vm)。


## <a name="example-workload"></a>範例工作負載

Adventure Works 循環想要建立線上存放區中的應用程式 Azure 所組成︰

- 執行前端網頁層用戶端的兩個 IIS 伺服器
- 處理資料與應用程式層中的訂單的兩個 IIS 伺服器
- 兩個 AlwaysOn 可用性群組 （兩個 SQL Server 與大部分節點見證） 儲存在資料庫層的產品資料] 和 [訂單的 Microsoft SQL Server 執行個體
- 兩個 Active Directory 網域控制站客戶帳戶和驗證層的供應商
- 所有伺服器都位於兩個子網路︰
    - 前端網頁伺服器的子網路 
    - 應用程式伺服器、 SQL 叢集及網域控制站的後端子網路

![不同層級的應用程式基礎結構的圖表](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

安全的內送網路流量必須是負載平衡網頁伺服器之間客戶瀏覽線上存放區。 訂購形式的網頁伺服器必須平衡之間的應用程式伺服器的 HTTP 要求的處理流量。 此外，必須設計基礎結構的可用性。

必須加入產生的設計︰

- Azure 訂閱與帳戶
- 單一資源群組
- 儲存帳戶
- 具有兩個子網路的虛擬網路
- 設定與類似角色 Vm 可用性
- 虛擬機器

所有以上遵循這些的命名慣例︰

- Works 循環用途的探險**[IT 工作量]-[位置]-[Azure 資源]**做為前置詞
    - 例如，「**azos**」 （Azure 線上商店） IT 工作負載名稱，而 「**使用**」 （東亞美國 2） 就是位置
- 儲存帳戶使用 adventureazosusesa**[描述]**
    - 「 adventure' 已新增至要提供唯一性的前置字元和儲存體帳戶名稱不支援使用連字號。
- 虛擬網路使用 AZOS-使用-VN**[編號]**
- 可用性集使用 azos-使用-為-**[角色]**
- 虛擬機器名稱會使用 azos-使用-vm-**[vmname]**


## <a name="azure-subscriptions-and-accounts"></a>Azure 訂閱與帳戶

Adventure Works 循環使用其企業訂閱，名為 Adventure Works 企業訂閱，提供此 IT 工作負載的計費。


## <a name="storage-accounts"></a>儲存帳戶

Adventure Works 循環取決於所需的兩個儲存帳戶︰

- 標準的儲存空間的網頁伺服器、 應用程式伺服器及網域控制站和其資料磁碟**adventureazosusesawebapp** 。
- **adventureazosusesasql**的進階版儲存空間的 SQL Server Vm 和其資料。


## <a name="virtual-network-and-subnets"></a>虛擬網路和子網路

虛擬的網路不需要進行中 Adventure 工作循環內部部署網路連線能力，因為他們決定雲端專用的虛擬網路上。

他們可以建立雲端專用的虛擬網路使用 Azure 入口網站的下列設定︰

- 名稱︰ AZOS 使用 VN01
- 位置︰ 東亞美國 2
- 虛擬網路位址空間︰ 10.0.0.0/8
- 第一個子網路︰
    - 名稱︰ 主選單
    - 地址空間︰ 10.0.1.0/24
- 第二個子網路︰
    - 名稱︰ 後端
    - 地址空間︰ 10.0.2.0/24


## <a name="availability-sets"></a>可用性集

若要維持可用性其線上存放區的所有四個階層，Adventure Works 循環決定四個可用性集︰

- **azos-使用-為-web**網頁伺服器
- **azos-使用-為-應用程式**的應用程式伺服器
- SQL Server 的**azos-使用-為-sql**
- 網域控制站**azos 使用-為-dc**


## <a name="virtual-machines"></a>虛擬機器

Adventure Works 循環決定其 Azure Vm 的下列名稱︰

- **azos 使用-vm web01**的第一個網頁伺服器
- **azos 使用-vm web02**的第二個網頁伺服器
- 第一個應用程式伺服器**azos 使用-vm app01**
- 第二個應用程式伺服器**azos 使用-vm app02**
- 第一個 SQL Server 伺服器叢集**azos 使用-vm sql01**
- 第二個 SQL Server 伺服器叢集**azos 使用-vm sql02**
- 第一個網域控制站**azos 使用-vm dc01**
- 第二個網域控制站**azos 使用-vm dc02**

以下是結果的設定。

![部署 Azure 中的最後的應用程式基礎結構](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

此設定會併入︰

- 使用兩個子網路 （FrontEnd 和後端） 以雲端專用虛擬網路
- 兩個儲存帳戶
- 四個可用性集線上存放區的每一層
- 四個層虛擬機器
- 外部的負載平衡為 HTTPS 的 web 網頁伺服器從網際網路流量導向設定
- 內部的負載平衡加密的網頁流量從網頁伺服器的應用程式的伺服器設定
- 單一資源群組


## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 