<properties
   pageTitle="獲得深入見解到消耗 Microsoft Azure 資源 |Microsoft Azure"
   description="提供 Azure 帳單的使用情況和 RateCard Api，這用來提供到消耗 Azure 資源和趨勢的深入見解的概觀。"
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>獲得深入見解到消耗 Microsoft Azure 資源

客戶及合作夥伴要求對於預測及管理其 Azure 成本的能力。  當他們從 Capex 移至 Opex 模型，也需要執行 showback 與 chargeback 分析，以及提供模式逼真度估計和帳單，尤其是對於大型雲端部署中的功能。

Azure 資源使用狀況及工資率卡片 Api 討論此文章地址這些需求，讓新的深入見解的 Azure 的資源將。  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>介紹 Azure 資源使用狀況及 RateCard Api

Azure 資源使用狀況和 RateCard Api 實作為資源提供者，做為公開 Azure 資源管理員 Api 系列的一部分。  

### <a name="azure-resource-usage-api-preview"></a>Azure 資源使用狀況 API （預覽版本）
客戶及合作夥伴可以使用 Azure 資源使用狀況 API 來取得其估計 Azure 消耗資料。 功能包括︰

- **Azure 角色型存取控制**的客戶及合作夥伴可以設定其存取原則[Azure 入口網站](https://portal.azure.com)上，或透過[Azure PowerShell cmdlet](powershell-install-configure.md)來指定哪些使用者或應用程式可以取得訂閱的使用情況資料的存取權。 來電者必須使用標準的 Azure Active Directory 權杖進行驗證。 來電者也必須新增閱讀程式、 擁有者或參與者的角色，以取得特定 Azure 訂閱的存取權之使用狀況。

- **每小時或每日的彙總**-來電者可以指定是否要在每小時值區或每日值區其 Azure 的使用情況資料。 預設值是每日。

- **提供的執行個體中繼資料 （包括資源標記）** – 執行個體層級的詳細資料，例如的完整的資源 uri (/subscriptions/ {訂閱識別碼} /...)，以及 [資源] 群組中的資訊和資源標籤將會提供在回應中。 將決定性協助客戶，並以程式控制方式的使用案例等跨充電配置標記，以使用方式。

- **資源所提供的中繼資料**資源詳細資料，例如量表名稱、 量表類別、 量表子類別、 單位和區域也會在回應中，讓來電者深入了解已耗用的傳遞。 我們也同時使用對齊 Azure 入口網站中，資源中繼資料術語 Azure 使用量 CSV，EA 帳單 CSV，以及其他-在公開的體驗，讓客戶體驗跨相互關聯資料。

- **所有優惠類型的使用狀況**– 使用情況資料可存取的所有提供包括 Pay-as-you-go、 MSDN、 貨幣認可、 貨幣信用卡及 EA 還有其他的類型。

### <a name="azure-resource-ratecard-api-preview"></a>Azure 資源 RateCard API （預覽版本）
客戶及合作夥伴可以使用 Azure 資源 RateCard API 存取清單可用 Azure 的資源，並搭配估價每資訊。 功能包括︰

- **Azure 角色型存取控制**的客戶及合作夥伴可以設定其存取原則[Azure 入口網站](https://portal.azure.com)上，或透過[Azure PowerShell cmdlet](powershell-install-configure.md)來指定哪些使用者或應用程式可以取得 RateCard 資料的存取權。 來電者必須使用標準的 Azure Active Directory 權杖進行驗證。 來電者也必須新增閱讀程式、 擁有者或參與者的角色，以取得特定 Azure 訂閱的存取權之使用狀況。

- **支援 Pay-as-you-go、 MSDN、 貨幣認可和貨幣信用卡提供 (不支援 EA)** -此 API 提供 Azure 優惠層級工資率的詳細資訊，與訂閱層級。  此 api 來電者必須經過優惠的資訊，以取得資源詳細資料] 與 [工資率。  EA 優惠有自訂每個註冊工資率，我們無法提供 EA 費率這一次。

## <a name="scenarios"></a>案例

以下是一些所做的使用狀況] 和 [RateCard Api 組合可能的案例︰

- **Azure 花在當月期間**-客戶可以使用使用狀況及組合，取得更好的深入見解，將其雲端的 RateCard Api 期間月花費分析的使用狀況] 和 [收費的估計值下限及每日值區。

- **設定提醒**– 客戶及合作夥伴可以設定資源或貨幣為基礎的通知，其雲端消耗上取得估計的消耗及使用的使用狀況] 和 [RateCard API 收費估計值。

- **Predict 帳單**– 客戶及合作夥伴可以取得其估計的消耗並花在雲端，並套用電腦學習演算法來預測其帳單就會在收費週期結束為止。

- **測試消耗成本分析**– 客戶可以使用 RateCard API 預測多少其帳單是結果前往他們的工作量 Azure，以提供也想要使用數字。 如果客戶會有其他雲朵或私人雲朵中的現有工作負載，他們也可以將對應 Azure 及其用法以取得更好的估計值，其估計 Azure 的費率花。 這會提供什麼可透過[Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)的增強型的檢視 （例如） 計費合作夥伴提供優惠在樞紐分析及比較/對比 Pay-As-You-Go，包括貨幣認可和貨幣信用以外的不同優惠類型之間的能力。 Api 也會提供可執行成本估計變更，依地區，啟用為部署資源中不同的 Dc 世界各地的總成本具有直接的影響，部署決策所需的模擬分析的類型。

- **模擬分析** -

    - 客戶及合作夥伴可以決定是否要加值執行他們的工作量，在另一個區域中，或 Azure 資源的其他設定。 Azure 資源成本可能不同基礎 Azure 區域中執行，而這個選項可讓客戶及合作夥伴，以取得成本最佳化。

    - 客戶及合作夥伴也可以決定其他 Azure 優惠類型會在 Azure 資源提供更佳的工資率。

## <a name="partner-solutions"></a>合作夥伴解決方案

[Microsoft Azure 使用情況和 RateCard Api 啟用 Cloudyn 至提供的客戶的 ITFM](billing-usage-rate-card-partner-solution-cloudyn.md)說明 Azure 計費 API 合作夥伴[Cloudyn](https://www.cloudyn.com/microsoft-azure/)所提供的整合體驗。  本文提供詳細的涵蓋他們的體驗，包括的簡短影片，其中顯示如何 Azure 客戶可以使用 Cloudyn 和 Azure 計費 Api，獲得深入見解從其 Azure 消耗資料。

[雲端巡洋艦和 Microsoft Azure 計費 API 整合](billing-usage-rate-card-partner-solution-cloudcruiser.md)說明[雲端巡洋艦 Express Azure 套件的](http://www.cloudcruiser.com/partners/microsoft/)運作方式直接從 WAP 入口網站，讓客戶流暢地管理其 Microsoft Azure 私人或託管公用雲端的操作及財務方面，以單一使用者介面。   

## <a name="next-steps"></a>後續步驟
+ 請參閱[Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)如需這兩個 Api 的一部分的 Api 提供 Azure 資源管理員所設定的詳細資訊。
+ 如果您想要深入瞭解向右將程式碼範例，請查看我們 Microsoft Azure 計費 API 程式碼範例[Azure 程式碼](https://azure.microsoft.com/documentation/samples/?term=billing)範例。

## <a name="learn-more"></a>深入瞭解
+ 請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)，瞭解更多關於 Azure 資源管理員。
+ 如需有關的套件的必要大致瞭解雲端花、 Gartner 文章[IT 財務管理 (ITFM) 工具市場指南](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)，請參閱協助工具的詳細資訊。
