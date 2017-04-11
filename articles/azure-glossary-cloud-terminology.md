<properties
    pageTitle="Azure 詞彙-Azure 字典 |Microsoft Azure"
    description="若要瞭解雲端術語 Azure 平台上的使用 Azure 詞彙。 這個簡短的 Azure 字典提供 Azure 常見雲端規定定義。"
    keywords="Azure 的字典、 雲端術語、 Azure 詞彙、 術語定義、 雲端字詞"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure 詞彙︰ 雲端術語 Azure 平台上的字典

Microsoft Azure 詞彙是簡短雲端術語 Azure 平台的字典。

## <a name="find-service-definitions-and-other-cloud-terms"></a>尋找服務定義和其他雲端字詞

* Azure 服務和其 AWS 定義的[Microsoft Azure 及 Amazon Web 服務](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)，請參閱版本有何差異。

* 一般產業雲端字詞請參閱[雲端運算的字詞](https://azure.microsoft.com/overview/cloud-computing-dictionary/)。

使用上述兩個參照 Azure 詞彙提供 Azure 和雲端產業的端對端分類。  

## <a name="azure-glossary-list"></a>Azure 詞彙清單


### <a name="account"></a>帳戶  
公司或學校或個人的 Microsoft 帳戶，可用來存取及管理 Azure 的訂閱。  
另請參閱[如何 Azure 訂閱的 Azure Active Directory 與相關聯](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>顯示狀態設定  
受管理放在一起，提供應用程式重複和可靠性的虛擬機器集合。 使用可用性設定可確保計畫之內進行的維修作業事件期間至少有一個虛擬機器可用。  
請參閱[管理的 Windows 虛擬機器可用性](./virtual-machines/virtual-machines-windows-manage-availability.md)] 或 [[管理 Linux 虛擬機器中的可用性](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Azure 傳統部署模型  
下列其中一項用來部署資源 （新的模型是 Azure 資源管理員） 的 Azure 中的兩種[部署模型](resource-manager-deployment-model.md)。 Azure 資源可以部署一個模型] 或 [其他]，而其他人可以部署兩個模型中。 接下來要專攻資源的個別 Azure 資源詳細資料的相關指引，可以使用部署。


### <a name="cli"></a>Azure 命令列介面 (CLI)  
[命令列介面](xplat-cli-install.md)，可用於管理 Azure 服務的 Windows，OSX 和 Linux 電腦。


### <a name="powershell"></a>Azure PowerShell  
從 Windows 電腦管理透過命令列的 Azure 服務[命令列介面](powershell-install-configure.md)。 僅透過 PowerShell 或 CLI 可管理部分的服務或服務的功能。 接下來要專攻資源的每個個別的 Azure 資源詳細資料的相關指引，可以使用部署。   
另請參閱[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md)


### <a name="arm-model"></a>Azure 資源管理員部署模型  
下列其中一項用來在 （另一個是傳統的部署模型） 的 Microsoft Azure 中部署資源的兩種[部署模型](resource-manager-deployment-model.md)。 Azure 資源可以部署一個模型] 或 [其他]，而其他人可以部署兩個模型中。 接下來要專攻資源的個別 Azure 資源詳細資料的相關指引，可以使用部署。


### <a name="fault-domain"></a>故障網域  
在同一時間可以可能失敗的可用性集中的虛擬機器的集合。 範例是在機架共用常見 power 來源和網路切換參數的電腦的群組。 在 Azure 虛擬機器中的顯示狀態設定自動分隔跨多個錯誤網域。  
請參閱[管理的 Windows 虛擬機器可用性](./virtual-machines/virtual-machines-windows-manage-availability.md)] 或 [[管理 Linux 虛擬機器中的可用性](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="geo"></a>地理  
資料 residency 通常包含兩個或多個區域的已定義的邊界。 邊界可能或超出國家的框線，然後稅法規會受到影響。 每個地理有一個以上的區域。 亞太地區和日本，就會是 geos 的範例。 也稱為*地理位置*。  
另請參閱[Azure 區域](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>地理複寫  
自動複製內容，例如二進位大型物件、 表格和地區的對佇列程序。  
另請參閱[Azure SQL 資料庫的作用中地理複寫](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>圖像  
包含的作業系統和應用程式的設定，可以用來建立虛擬機器中的任何數字的檔案。 Azure 中有兩種類型的圖像︰ VM 圖像和 OS 圖像。 VM 圖像包含作業系統，圖像會建立時，附加至虛擬機器的所有磁碟。 OS 圖像包含只一般化的作業系統沒有資料磁碟設定。  
請參閱[瀏覽並選取 CLI PowerShell 的 Azure 中的 Windows 虛擬機器圖像](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>限制  
您可以建立的資源或可達到效能事先數目。 限制是通常與訂閱、 服務與產品相關聯。  
請參閱[Azure 訂閱及服務限制，配額和限制](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>負載平衡器  
分散對齊網路的電腦之間的連入流量資源。 Azure 中負載平衡器分配負載平衡器設定中所定義的虛擬機器流量。 [負載平衡器](./load-balancer/load-balancer-overview.md)可以具網際網路，或者可以是內部。  


### <a name="offer"></a>優惠  
價格、 信用額度及適用 Azure 訂閱相關的字詞。  
請參閱[Azure 提供詳細資料] 頁面](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>入口網站  
用來部署及管理 Azure 服務安全 Web 入口網站。  有兩個入口網站︰ [Azure 入口網站](http://portal.azure.com/)與[傳統入口網站](http://manage.windowsazure.com/)。 某些服務可在兩個入口網站，而其他人只可在其中一個。 [Azure 入口網站的可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)列出哪些服務可在哪一個入口網站。  


### <a name="region"></a>區域  
地理不 cross 國家的框線，其中包含一或多個資料中心內的區域。 價格、 地區服務與優惠類型被公開區域層級。 地區通常搭配最多可有數種數百英哩，以形成地區組中的其他區域。 地區組可以做為機制損毀修復和高可用性案例。 也稱為通常*位置*。  
另請參閱[Azure 區域](best-practices-availability-paired-regions.md)


### <a name="resource"></a>資源  
Azure 方案的一部分的項目。 每個 Azure 服務可讓您部署不同類型的資源，例如資料庫或虛擬機器。   
另請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>資源群組  
資源管理員] 中保留應用程式的相關的資源容器。 資源群組可以包含的所有資源的應用程式或只邏輯群組在一起的資源。 您可以決定要如何根據什麼最適合貴組織的資源群組配置資源。  
另請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>資源管理員範本  
JSON 檔案的宣告方式定義一或多個 Azure 的資源，可定義部署資源之間的相依性。 範本可用於一致的方式和重複部署資源。  
另請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>資源提供者  
服務，提供您可以部署及管理透過資源管理員的資源。 每個資源提供者提供的部署資源所使用的作業。 可透過 Azure 入口網站與 PowerShell 的 Azure 數個程式設計 Sdk 存取資源提供者。  
另請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>角色  
所代表的意義來控制可以指派給使用者、 群組和服務的存取。 角色都能執行的動作，例如建立，管理及閱讀 Azure 資源。  
另請參閱[RBAC︰ 內建的角色](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>服務等級協定 (SLA)  
將 Microsoft 的承諾說明的執行時間和連線合約。 每個 Azure 服務會有特定的 SLA。  
另請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>儲存帳戶  
可讓您的儲存空間帳戶存取 Azure 儲存體中的 Azure Blob、 佇列、 表格和檔案的服務。 您儲存的帳戶提供的 Azure 儲存體資料物件的唯一的命名空間。  
另請參閱[關於 Azure 儲存體帳戶](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>訂閱  
客戶合約與 Microsoft，讓它們取得 Azure 服務。 訂閱價格和相關的字詞都受到選擇的訂閱優惠。 請參閱[Microsoft 線上訂閱合約](https://azure.microsoft.com/support/legal/subscription-agreement/)。  
另請參閱[如何 Azure 訂閱的 Azure Active Directory 與相關聯](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>標記  
可讓您將根據您的需求管理或帳單資源分類編製索引項目。 如果您有複雜的各種資源群組和資源，且您需要以視覺化方式呈現資產最有意義的方式，您可以使用標籤。 例如，您可能標記作為類似的角色，您組織中或屬於相同的部門的資源。  
另請參閱[使用標籤以組織 Azure 資源](resource-group-using-tags.md)


### <a name="update-domain"></a>更新的網域  
會同時更新的虛擬機器中的可用性組合的集合。 在相同的更新網域的虛擬機器重新啟動一起預定進行的維護期間。 Azure 不重新啟動一次的多個更新的網域。 也稱為升級的網域。  
請參閱[管理的 Windows 虛擬機器可用性](./virtual-machines/virtual-machines-windows-manage-availability.md)] 或 [[管理 Linux 虛擬機器中的可用性](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="vm"></a>虛擬機器  
軟體的實作的作業系統的電腦。 多個虛擬機器可以同時執行相同的硬體上。 中 Azure 虛擬機器中可以使用各種不同的大小。  
另請參閱[虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>虛擬機器副檔名  
資源實作行為或功能，可以協助您其他程式使用，或執行的電腦，能讓您的互動。 例如，您可以使用副檔名為 VM 存取重設或修改遠端存取 Azure 虛擬機器中的值。  
請參閱[關於虛擬機器 extensions 和功能 (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md)或[虛擬機器 extensions 和功能 (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>虛擬網路  
網路，提供您與其他所有 Azure 租用戶隔離的 Azure 資源之間的連線。 它可以透過[Azure VPN 閘道](./vpn-gateway/vpn-gateway-about-vpngateways.md)其他 Azure 虛擬網路，並使用[多個選項](./vpn-gateway/vpn-gateway-cross-premises-options.md)您內部部署網路連線。 您也可以完全控制 IP 地址區塊、 DNS 設定、 安全性原則，以及傳送這個網路中的資料表。  
另請參閱[虛擬網路概觀](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**另請參閱**  
- [Azure 快速入門](https://azure.microsoft.com/get-started/)
- [雲端資源中心](https://azure.microsoft.com/resources/)  
- [Azure 商務應用程式](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure 中資料中心](https://azure.microsoft.com/overview/business-apps-on-azure/) 





