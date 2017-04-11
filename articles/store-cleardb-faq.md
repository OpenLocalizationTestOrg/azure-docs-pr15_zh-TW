<properties
    pageTitle="Azure 應用程式服務 ClearDB MySql 資料庫的常見問題集 |Microsoft Azure"
    description="Azure 應用程式服務中使用 ClearDB MySQL 資料庫的常見問題的解答。"
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Azure 應用程式服務 ClearDB MySql 資料庫的常見問題集

此常見問題集解答關於使用和 Azure Web 應用程式購買 ClearDB MySQL 資料庫。

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>我擁有哪些選項的 MySQL Azure 上？

您有幾個選項︰

* [ClearDB 共用 MySQL 資料庫](/marketplace/partners/cleardb/databases/)

* [ClearDB MySQL 進階版叢集](/marketplace/partners/cleardb-clusters/cluster/)

* [Azure VM 上執行的 MySQL 叢集](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [MySQL Azure VM 上執行的單一執行個體](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB 主機服務 MySQL，而您管理 MySQL 基礎結構。 當您執行您自己的資料庫或 MySQL 叢集 Azure 虛擬機器上時，您必須設定 [MySQL 伺服器，並保持其更新修補程式。

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>需要 Web 應用程式 + Azure Marketplace MySQL 範本信用卡嗎？

您使用的的訂閱的類型而定。 以下是一些常用的訂閱類型︰

* [支付另存新檔您移至](/offers/ms-azr-0003p/)︰ 需要的信用卡，與您購買負責您的信用卡付費的 MySQL 資料庫。

* [免費試用版](https://azure.microsoft.com/pricing/free-trial/)︰ 搭配 Microsoft Azure 服務，但不允許購買的協力廠商的資源，包括貸項總計。 若要購買的協力廠商服務或付費的 MySQL 資料庫，您需要使用信用卡啟用訂閱。 Web 應用程式，您可以建立免費 ClearDB MySQL 資料庫。

* [MSDN 訂閱](/pricing/member-offers/msdn-benefits/)並**MSDN 開發所支付的測試移**︰ 類似免費試用版訂閱 MSDN 必須要有信用卡，以從 ClearDB 購買付費的 MySQL 解決方案。

* [Enterprise 合約 (EA)](/pricing/enterprise-agreement/): EA 客戶的計費針對其 EA 所有 Azure Marketplace （第三方） 購買行為個別的合併發票上的每一季。 是向您收費外的任何服務商場購買貨幣認可。 請注意，這次，Azure 市集沒有註冊亞塞拜然、 克羅埃西亞、 挪威及波多黎各中的客戶。 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99)︰ 您可以建立只有免費 ClearDB 資料庫的 Web 應用程式。 沒有任何限制，您可以建立的免費 ClearDB MySQL 資料庫的數目。 請注意，可用的資料庫不要用於生產 web 應用程式]，為此服務只適用於試用版。

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>為什麼已支付的 Web 應用程式 + MySQL 從 Azure Marketplace $3.50？

預設的 [資料庫] 選項是坦，這是 $3.50。 我們不顯示成本資料庫建立時，您可能會誤購買的資料庫，您不想。 我們想要尋找的方式來改善的體驗，但就必須檢查所有您選取價格層 web 應用程式和資料庫的 [**建立**和啟動的資源部署之前。

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>我在自己的 Azure 虛擬機器上執行 MySQL。 我可以到我的資料庫連線我 Azure web 應用程式？

[是]。 只要您 Azure VM 授予遠端存取您的 web 應用程式，您可以將 web 應用程式連線至資料庫中。 如需詳細資訊，請參閱[安裝的 MySQL 虛擬機器上](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)。

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>在其中國家/地區是 ClearDB 進階版 MySQL 叢集支援？

[ClearDB 進階版 MySQL 叢集](/marketplace/partners/cleardb-clusters/cluster/)，可在所有 Azure 區域內全球但印度、 澳大利亞、 巴西南部與 china （中國）。

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>可以建立新的叢集之前建立資料庫 ClearDB 進階版叢集解決方案嗎？

否，不支援建立空白的 ClearDB 叢集。 Azure 入口網站可讓您以叢集，可能會建立新的叢集同時建立資料庫。

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>將我會收到警告如果我想要刪除 ClearDB MySQL 資料庫使用中的 [我的應用程式？

否，Azure 不警告您如果您刪除取決於您的應用程式服務商場購買。

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>哪些地區建立 ClearDB 資料庫？

Azure Marketplace 不適用於在亞塞拜然、 克羅埃西亞、 挪威或波多黎各的客戶。 在下列區域內，無法使用 ClearDB。

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>我該選擇什麼價格層生產 web 應用程式與資料庫？

使用基本或更高的價格層的 Web 應用程式。 為 ClearDB，建議您使用土星 」 或 「 木星方案。 檢閱功能與[Web 應用程式](/pricing/details/app-service/)和[ClearDB MySQL 資料庫](/marketplace/partners/cleardb/databases/)選擇適合您需求的每一個價格層級的限制。

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>如何升級 ClearDB 資料庫從一個方案到另一個？

在[Azure 入口網站](https://portal.azure.com)，您可以調整設定 ClearDB 共用裝載資料庫。 請閱讀，瞭解更多[文章](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/)。 我們目前不支援 ClearDB 進階版叢集 Azure 入口網站中的升級。

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>看不到我的 ClearDB 資料庫 Azure 入口網站中？

如果我們建立 ClearDB 資料庫中使用 Azure 資源管理員] 或 [[新的 Azure 入口網站](https://portal.azure.com)，它不會看到[舊 Azure 入口網站](https://manage.windowsazure.com)中。 若要解決此是手動連結至 web 應用程式的資料庫。 同樣地如果建立 ClearDB 資料庫[舊入口網站](https://manage.windowsazure.com)中，您將無法查看您的資料庫，在[新的 Azure 入口網站](https://portal.azure.com)。 沒有解決的後面的案例。

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>誰連絡支援我的資料庫時向下？

連絡人[ClearDB 支援](https://www.cleardb.com/developers/help/support)任何資料庫的相關問題。 準備好向他們提供您 Azure 訂閱的資訊。

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>可以建立其他使用者的我 ClearDB MySQL 資料庫叢集解決方案嗎？ 

[否]。 您無法建立額外的使用者，但您可以建立其他資料庫 ClearDB 資料庫叢集上。  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>可以資料庫的基本/Pro 數列就地升級類似 ClearDB 入口網站上的今天星球方案嗎？

是的可資料庫的基本數列升級就地 (透過基本 500 基本 60)。 可 pro 數列就地升級 (透過 Pro 1000 Pro 125) 除了 Pro 60。 我們不支援目前升級 Pro 60 資料庫。 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>當到另一個訂閱移轉我的資源時，會 ClearDB MySQL 資料庫移轉以及嗎？ 

當您執行資源移轉訂閱時，便會套用一些[限制](./app-service-web/app-service-move-resources.md)。 ClearDB MySQL 資料庫是協力廠商服務，因此不會不會取得移轉移轉中 Azure 訂閱。 如果您不是管理移轉 MySQL 資料庫移轉 Azure 資源之前，可以停用 ClearDB MySQL 資料庫。 手動將第一次移轉您的資料庫，然後再執行 [web 應用程式的 [Azure 訂閱移轉。 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>我可以將移轉 ClearDB 資料庫從信用卡訂閱 EA 訂閱？

ClearDB 中現有的資料庫中使用現有的訂閱相關聯的信用卡。 若要使用的 EA 訂閱您需要將您的資料移轉到新的資料庫︰

* 購買新的 ClearDB 資料庫 EA 訂閱。
* 將您的資料移轉至新的資料庫。
* 更新您的應用程式使用新的資料庫。
* 刪除舊 ClearDB 資料庫。

當您使用 MySQL (ClearDB) 建立新 web 應用程式，或建立 MySQL 資料庫 (ClearDB) 時，您所選擇的訂閱會決定如何將支付的服務。 以 EA 訂閱，我們不會封鎖協力廠商等服務 ClearDB Azure 入口網站中的採購。 EA 訂閱貨幣認可以外的計費和季度及 arrears 向收費。 EA 客戶必須支付任何協力廠商服務商場的服務設定如信用卡付款方法。

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>我可以在這裡看見 ClearDB 資源 EA 訂閱中的費用？

直接 EA 的客戶，Azure Marketplace 費用會顯示在企業版入口網站中。 請注意，所有服務商場購買和消耗向外貨幣認可收費每季和 arrears 向收費。 EA 客戶必須直接付費的協力廠商服務提供者，而且可以執行啟用如以 EA 帳戶信用卡付款方法。

Indirect EA 客戶可以在 [**管理訂閱**頁面上的企業版入口網站中，找到 Azure Marketplace 訂閱，但隱藏的價格。 客戶必須與其 lsp 進行傳送連絡資訊服務商場費用。

協力廠商服務的存取權 Azure Marketplace 可由您 EA Azure 註冊系統管理員。 他們可以停用或重新啟用存取第 3 廠商購買從存放區中 [管理帳戶和訂閱企業版入口網站中的 [帳戶] 區段下。

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>誰連絡的問題 ClearDB 服務的帳單關於 EA 訂閱？

有關計費下其 EA 註冊，請連絡[企業的客戶支援](http://aka.ms/AzureEntSupport)。 EA 入口網站支援小組會接聽您的問題，或協助解決您的問題。

 



## <a name="more-information"></a>詳細資訊

[Azure Marketplace 常見問題集](/marketplace/faq/)
