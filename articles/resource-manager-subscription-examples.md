<properties
   pageTitle="案例的及範例訂閱管理 |Microsoft Azure"
   description="提供如何實作 Azure 訂閱管理常見案例的範例。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>實作 Azure 企業 scaffold 的範例

本主題提供範例企業可以如何實作[Azure 企業 scaffold](resource-manager-subscription-governance.md)的建議。 它會使用虛構公司為 「 contoso 」 以描繪出常見的案例的最佳作法。 

## <a name="background"></a>背景

Contoso 是全球公司的所有項目從 「 軟體與服務 「 模型封裝模型中的客戶提供供應鏈解決方案部署內部部署。  使用大量的開發中心印度、 美國和加拿大全球發展軟體。 

公司的 ISV 部分分成幾個重要的企業管理產品的獨立的業務單位。 每個業務單位有自己的開發人員、 產品經理和設計師。 

企業技術服務 (ETS) 業務單位提供集中式的 IT 功能，與管理多個業務單位位置主控自己的應用程式的資料中心。 管理資料中心，以及 ETS 組織提供及管理集中的共同作業 （例如電子郵件和網站） 和電話語音網路/服務。 他們也可以管理客戶具工作負載的較小的業務單位沒有操作人員。 

本主題中使用下列角色︰

- Dave 是 ETS Azure 系統管理員。
- Alice 是 Contoso 主管的開發，在供應鏈業務單位。 

Contoso 必須建立列的商務應用程式和客戶具應用程式。 決定其 Azure 上執行應用程式。 Dave 讀取[規定訂閱管理](resource-manager-subscription-governance.md)主題，並現在已準備就緒，若要執行的建議。 

## <a name="scenario-1-line-of-business-application"></a>情況 1︰ 線條的商務應用程式

Contoso 建置原始程式碼管理系統 (BitBucket) 可供世界各地的開發人員。  應用程式，以服務 (IaaS) 使用基礎結構，並由網頁伺服器與資料庫伺服器。 開發人員存取他們的開發環境中的伺服器，但是他們不需要 access Azure 中的伺服器。 Contoso ETS 想要讓應用程式擁有者與小組管理應用程式。 僅限 Contoso 公司網路上時使用應用程式。 Dave 必須設定此應用程式的訂閱。 訂閱，也會在未來裝載其他開發人員相關軟體。  

### <a name="naming-standards--resource-groups"></a>命名標準和資源群組

Dave 所建立的訂閱支援所有的所有業務單位的開發人員工具。 他需要建立的訂閱及資源群組 （適用於應用程式與網路） 有意義的名稱。 他會建立下列訂閱和資源群組︰

| 項目 | 名稱 | 描述 |
| ---- | ---- | ----------- |
| 訂閱 | Contoso ETS DeveloperTools 生產 | 支援一般開發人員工具 |
| 資源群組 | rgBitBucket | 包含的應用程式網頁伺服器與資料庫伺服器 |
| 資源群組 | rgCoreNetworks | 包含的虛擬網路和網站-閘道器的連線 |


### <a name="role-based-access-control"></a>角色型存取控制

建立他的訂閱之後, Dave 要確保適當的團隊和應用程式擁有人可以存取他們的資源。 Dave 可辨識的每個小組都有不同的需求。 他利用有已同步處理來自 Contoso 的內部部署 Active Directory (AD) 到 Azure Active Directory 群組，並提供正確的小組存取層級。 

Dave 指派的訂閱的下列角色︰ 

| 角色 | 指派給 | 描述 |
| ---- | ----------- | ----------- |
| [擁有者](./active-directory/role-based-access-built-in-roles.md#owner) | 從 Contoso 管理識別碼 AD | 此 ID 控制，僅使用時間 (JIT) 存取透過 Contoso 的身分識別管理工具，並確保已完全稽核訂閱的擁有者存取。 |
| [安全性管理員](./active-directory/role-based-access-built-in-roles.md#security-manager) | 安全性與風險管理部門 | 此角色可讓使用者查看 [Azure 資訊安全中心與資源的狀態。 |
| [網路參與者](./active-directory/role-based-access-built-in-roles.md##network-contributor) | 網路小組 | 此角色可讓 Contoso network 小組管理網站 VPN 和虛擬網路。 |
| *自訂的角色* | 應用程式擁有者 | Dave 會建立一個角色授與修改 [資源] 群組中的資源的能力。 如需詳細資訊，請參閱[Azure RBAC 自訂角色](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>原則

Dave 具有管理資源訂閱中的下列需求︰

- 開發工具世界支援開發人員，因此他不想要封鎖的任何區域中建立資源的使用者。 不過，他還需要知道會建立資源。 
- 他的注意成本。 因此，他會想要建立必要昂貴的虛擬機器時，防止應用程式的擁有者。  
- 因為此應用程式中多個業務單位是開發人員，他會想要標記的每個資源與商務單位和應用程式擁有者。 藉由使用這些標記，ETS 可以結算適當的小組。

他會建立下列[資源管理員原則](resource-manager-policy.md)︰ 

| 功能變數 | 效果 | 描述 |
| ----- | ------ | ----------- |
| 位置 | 稽核 | 稽核建立的任何區域中的資源 |
| 類型 | 拒絕 | 拒絕建立 G 數列虛擬機器 |
| 標記 | 拒絕 | 需要應用程式的擁有者標記 |
| 標記 | 拒絕 | 需要成本中心標記 |
| 標記 | 附加 | 將標籤名稱**BusinessUnit**及標籤值**ETS**新增到所有的資源 |


### <a name="resource-tags"></a>資源標籤

Dave 瞭解他需要有識別 BitBucket 實作成本中心帳單上的特定資訊。 此外，Dave 想知道 ETS 擁有的所有資源。 

他會將下列[標籤](resource-group-using-tags.md)資源群組與資源。 
 
| 標籤名稱 | 標記值 |
| -------- | --------- |
| ApplicationOwner | 管理此應用程式之人員的名稱。 |
| CostCenter | 成本中心 Azure 消耗已支付的群組]。 |
| BusinessUnit | **ETS**（與訂閱相關聯業務單位） |

### <a name="core-network"></a>核心網路

Contoso ETS 資訊的安全性和風險管理小組檢閱 Dave 建議計劃，以移動 Azure 應用程式。 他們想要確定應用程式網際網路上公開。  Dave 也是在未來將會移至 Azure 的開發人員應用程式。 這些應用程式要求公用介面。  若要符合這些需求，他會提供內部和外部的虛擬網路和網路安全性群組限制存取。

他會建立下列資源︰

| 資源類型 | 名稱 | 描述 |
| ------------- | ---- | ----------- |
| 虛擬網路 | vnInternal | BitBucket 應用程式搭配使用，而且透過 ExpressRoute 連線到 Contoso 公司網路。  子網路 (sbBitBucket) 提供的應用程式與特定的 IP 位址空間。 |
| 虛擬網路 | vnExternal | 可供未來的應用程式需要-在公開結束點。 |
| 網路安全性群組 | nsgBitBucket | 可確保攻擊此工作負載的最小化，藉由使用只連線的連接埠 443 子網路應用程式所在 (sbBitBucket)。 |

### <a name="resource-locks"></a>資源鎖定 

Dave 會辨識 Contoso 公司網路內部的虛擬網路的連線必須防止任何和指令碼或不慎刪除。 

他會建立下列[資源鎖定](resource-group-lock-resources.md)︰

| 鎖定類型 | 資源 | 描述 |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | 防止使用者刪除的虛擬網路或子網路，但不是會阻止的新的子網路。 |

### <a name="azure-automation"></a>Azure 自動化 

Dave 具自動化此應用程式。 雖然他建立 Azure 自動化帳戶時，他一開始就不會使用它。 

### <a name="azure-security-center"></a>Azure 資訊安全中心 

Contoso IT 服務管理必須快速找出並處理威脅。 也會想要瞭解可能有什麼問題。  

若要符合這些需求，Dave 會啟用[Azure 資訊安全中心](./security-center/security-center-intro.md)，，並提供安全性管理員角色的存取。 

## <a name="scenario-2-customer-facing-app"></a>案例 2︰ 面對客戶的應用程式

供應鏈商務單位商務領導發現有各種商機使用忠誠卡片增加與 Contoso 客戶的互動。 Alice 小組必須建立此應用程式，並決定 Azure 增加符合業務需求的能力。 Alice 搭配 Dave 從 ETS 設定開發與作業系統此應用程式的兩個訂閱。

### <a name="azure-subscriptions"></a>Azure 訂閱 

Dave Azure 企業版入口網站登入並可看見供應鏈部門已經存在。  不過，為此專案是供應鏈小組 Azure 中的第一個開發專案，Dave 會辨識需要 Alice 開發小組的新帳戶。  他建立小組成員的 「 研發 「 帳戶，並將存取指派給 Alice。 愛麗絲透過帳戶入口網站中的記錄，並建立兩個訂閱︰ 一個用於保留開發伺服器和一個按住生產伺服器。  建立下列訂閱時，她就會依照先前建立的命名標準︰ 

| 訂閱使用 | 名稱 |
| ---------------- | ---- |
| 開發 | SupplyChain ResearchDevelopment LoyaltyCard 開發 |
| 生產 | SupplyChain 作業 LoyaltyCard 生產 |

### <a name="policies"></a>原則

Dave Alice 討論應用程式，並找出這個應用程式只是北美地區的客戶。  Alice 與小組成員計劃使用 Azure 的應用程式服務環境和 Azure SQL 來建立應用程式。 他們可能需要建立虛擬機器開發期間。  愛蕾絲想以確保其開發人員可以瀏覽，並檢查問題，而不抽取 ETS 中所需的資源。 

如需**開發訂閱**，他們可以建立下列原則︰

| 功能變數 | 效果 | 描述 |
| ----- | ------ | ----------- |
| 位置 | 稽核 | 稽核建立的任何區域中的資源。 |

不會限制的 sku，使用者可以建立在開發中，輸入並不需要任何資源群組或資源的標籤。

**生產訂閱**，請他們可以建立下列原則︰

| 功能變數 | 效果 | 描述 |
| ----- | ------ | ----------- |
| 位置 | 拒絕 | 拒絕建立的任何外部適用於美國的資料中心的資源。 |
| 標記 | 拒絕 | 需要應用程式的擁有者標記 |
| 標記 | 拒絕 | 需要部門標記。 | 
| 標記 | 附加 | 新增標籤至每個資源] 群組中，指出生產環境。 |

他們不限制使用者可以建立在生產環境中的 sku 的類型。

### <a name="resource-tags"></a>資源標籤 

Dave 瞭解他需要有特定的資訊來識別正確商務群組計費並擁有權。 他定義資源群組與資源的資源的標記。 
 
標籤名稱 | 標記值 |
| -------- | --------- |
| ApplicationOwner | 管理此應用程式之人員的名稱。 |
| 部門 | 成本中心 Azure 消耗已支付的群組]。 |
| EnvironmentType | **生產**（即使訂閱名稱中包含**生產**，包括此標籤可讓易於識別時，在入口網站，或帳單上的資源。） |

### <a name="core-networks"></a>核心網路

Contoso ETS 資訊的安全性和風險管理小組檢閱 Dave 建議計劃，以移動 Azure 應用程式。 要確保忠誠卡片應用程式是正確的隔離和 DMZ 網路中受保護。  若要執行這項需求，Dave 和 Alice 建立外部虛擬網路和網路安全性群組隔離 Contoso 公司網路忠誠卡片應用程式。  

如需**開發訂閱**，他們建立︰

| 資源類型 | 名稱 | 描述 |
| ------------- | ---- | ----------- |
| 虛擬網路 | vnInternal | 做 Contoso 忠誠卡片的開發環境，並透過 ExpressRoute 連線到 Contoso 公司網路。 |

**生產訂閱**，請他們建立︰

| 資源類型 | 名稱 | 描述 |
| ------------- | ---- | ----------- |
| 虛擬網路 | vnExternal | 主控忠誠卡片應用程式，並不直接連接到 Contoso ExpressRoute。 程式碼會被透過原始程式碼系統直接 PaaS 服務。 |
| 網路安全性群組 | nsgBitBucket | 可確保攻擊此工作負載的最小化，只允許在 TCP 443 上的 [繫結通訊。  Contoso 也調查 Web 應用程式防火牆用額外的保護。 |  

### <a name="resource-locks"></a>資源鎖定 

Dave Alice 授予，並決定要新增資源鎖定上的重要的資源，以防不慎刪除錯誤的程式碼推入期間的環境中的部分。 

他們會建立下列鎖定︰

| 鎖定類型 | 資源 | 描述 |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | 若要防止使用者將刪除的虛擬網路或子網路。 鎖定不會阻止的新的子網路。 |

### <a name="azure-automation"></a>Azure 自動化 

Alice 和開發小組有大量 runbooks 來管理此應用程式的環境。 Runbooks 允許應用程式和其他 DevOps 工作節點的加法/刪除。 

若要使用這些 runbooks，他們可以啟用[自動化](./automation/automation-intro.md)。

### <a name="azure-security-center"></a>Azure 資訊安全中心 

Contoso IT 服務管理必須快速找出並處理威脅。 也會想要瞭解可能有什麼問題。  

若要完成這些需求，Dave 可讓 Azure 資訊安全中心。 他也可確保 Azure 資訊安全中心監控資源，並提供存取 DevOps 及安全性小組。 

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立資源管理員範本，請參閱[建立 Azure 資源管理員範本的最佳作法](resource-manager-template-best-practices.md)。

*本主題 aggregate[卡爾 Kuhnhausen](https://github.com/karlkuhnhausen) 。*
