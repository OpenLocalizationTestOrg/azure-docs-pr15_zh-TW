<properties
    pageTitle="Azure 製作出酷炫 blob 儲存體 |Microsoft Azure"
    description="Azure Blob 儲存體的儲存空間層提供存取模式為基礎的物件資料的儲存空間成本有效。 製作出酷炫的儲存空間層最適合存取較少的資料。"
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Azure Blob 儲存體中︰ 熱和很棒的儲存空間層

## <a name="overview"></a>概觀

Azure 儲存體現在提供 Blob 儲存體 （物件儲存區） 的兩個儲存層，以便您可以儲存您的資料最有效根據您使用的方式。 Azure**作用儲存層**最適合儲存經常存取的資料。 Azure**製作出酷炫的儲存空間層**最適合儲存的資料，且不常存取久。 製作出酷炫的儲存空間層中的資料可容許較低的顯示狀態，但仍需要高持續性存取類似的時間和處理量特性，做為快速鍵的資料。 製作出酷炫的資料，較低的可用性 SLA 及更新版本的 access 成本，可接受多下方的儲存空間成本折衷方案。

現在，儲存在雲端的資料成長指數的速度。 若要管理您展開的儲存空間需求的成本，還是有幫助組織資料的屬性，例如頻率存取和計劃的保留期限的項目。 儲存在雲端的資料可以完全不同方式，是產生、 處理及存取在其生命。 某些資料積極存取並修改整個存留期間。 某些資料的存取經常在其生命週期卸除大幅為資料年紀存取。 一些資料保持在雲端的空閒和很少，如果，存取一次儲存。

每個資料存取案例上述從已針對特定的存取方式而最佳化的儲存空間差異層的優點。 使用的快速鍵，製作出酷炫的儲存空間層簡介，Azure Blob 儲存現在地址與差異的儲存空間層此需要分隔價格模型。

## <a name="blob-storage-accounts"></a>Blob 儲存體帳戶

**Blob 儲存體帳戶**是特殊的儲存空間可供您非結構化的資料儲存為二進位大型物件 （物件） 中 Azure 儲存體帳戶。 Blob 儲存體帳戶，您可以立即選擇儲存作用中且製作出酷炫的儲存空間層之間少存取製作出酷炫資料在下方的儲存空間成本，以及更頻繁地存取快速鍵成本較低 access 資料儲存。 Blob 儲存體帳戶類似於您現有的一般用途的儲存空間帳戶及共用所有好持續性、 可用性、 延展性，以及您使用 today、 包括 100 %api 一致的區塊 blob 的效能功能及附加二進位大型物件。

> [AZURE.NOTE] Blob 儲存體帳戶支援只區塊，和加二進位大型物件，以及不頁面二進位大型物件。

Blob 儲存體帳戶公開**存取層**的屬性，讓您指定的儲存空間層**作用****製作出酷炫**根據儲存的帳戶中的資料。 如果您的資料使用模式中的變更，您也可以切換隨時這些儲存層之間。

> [AZURE.NOTE] 變更儲存層可能會導致額外的費用。 請如需詳細資訊的[價格及帳單](storage-blob-storage-tiers.md#pricing-and-billing)] 區段，參閱。

在作用儲存層的範例使用情況包括︰

- 在使用或預期會經常存取 （從已讀取和寫入） 的資料。
- 已分段進行處理及最終的移轉，製作出酷炫的儲存空間層的資料。

製作出酷炫的儲存空間層的範例使用情況包括︰

- 備份，保存和損毀復原資料集。
- 舊版的媒體內容不會經常再檢視，但應該要立即存取時，才可使用。
- 需要供未來處理所收集的更多資料時有效地為儲存的成本的大型資料集。 （*例如*，長期科學記號的資料，從製造設備的原始遙測資料的儲存空間）
- 必須保留，即使已處理最終的可用表單的原始 （未經處理） 資料。 （*例如*，原始的媒體檔案後轉碼為其他格式）
- 規範與保存資料需要儲存一段時間，很存取。 （*例如*、 安全性相機資料、 舊 X-Rays/MRIs 醫療機構、 音訊錄製和文字記錄的財務服務的客戶來電）

儲存帳戶的詳細資訊，請參閱[關於 Azure 儲存體帳戶](storage-create-storage-account.md)。

只需要的應用程式會封鎖，或附加 blob 儲存體，建議您使用 Blob 儲存體帳戶，才能利用差異價格的層級式儲存模型。 不過，我們了解，這可能無法在某些情況下使用通用儲存帳戶應的方式，例如位置︰

- 您需要使用表格、 佇列或檔案，並想您儲存在同一個儲存帳戶的二進位大型物件。 記事會儲存在同一個帳戶以外具有相同沒有技術好處共用索引鍵。
- 您仍需要使用傳統部署模型。 Blob 儲存體帳戶僅可透過 Azure 資源管理員部署模型。
- 您需要使用頁面二進位大型物件。 Blob 儲存體帳戶不支援頁面二進位大型物件。 我們通常建議使用區塊二進位大型物件，除非您需要特定頁面二進位大型物件。
- 您使用的版本低於 4.x，早於 2014年-02-14[儲存服務 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx)或用戶端文件庫的版本，而且無法升級您的應用程式。

> [AZURE.NOTE] 在大部分的更多有關 Azure 區域追蹤目前支援 blob 儲存體帳戶。 您可以找到可用的區域，[依地區的 Azure 服務](https://azure.microsoft.com/regions/#services)] 頁面上的更新的清單。

## <a name="comparison-between-the-storage-tiers"></a>儲存層之間的比較

下表會反白顯示兩個儲存層之間的比較︰

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>作用儲存層</center></strong></td>
    <td><strong><center>製作出酷炫的儲存空間層</center></strong></td
</tr>
<tr>
    <td><strong><center>顯示狀態</center></strong></td>
    <td><center>99.9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>顯示狀態<br>（RA GRS 讀取）</center></strong></td>
    <td><center>99.99%</center></td>
    <td><center>99.9%</center></td>
</tr>
<tr>
    <td><strong><center>使用費用</center></strong></td>
    <td><center>較高的儲存空間成本<br>降低存取及交易成本</center></td>
    <td><center>降低儲存空間的成本<br>較高的存取和交易成本</center></td>
</tr>
<tr>
    <td><strong><center>最小的物件大小<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>最小值的儲存空間的持續時間<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>延遲<br>（第一個位元組時間）<center></strong></td>
    <td colspan="2"><center>毫秒為單位</center></td>
</tr>
<tr>
    <td><strong><center>延展性和效能的目標<center></strong></td>
    <td colspan="2"><center>相同帳戶用途的儲存空間</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Blob 儲存體帳戶支援的相同的效能與延展性目標做為帳戶用途的儲存空間。 如需詳細資訊，請參閱[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

## <a name="pricing-and-billing"></a>價格與帳單

Blob 儲存體帳戶使用新的價格模型為基礎的儲存空間層 blob 儲存體。 在使用 Blob 儲存體帳戶時，下列帳單事項︰

- **儲存空間的成本**︰ 除了儲存的資料量，儲存資料的成本而有所不同的儲存空間層。 每 gb 成本為較低，製作出酷炫的儲存空間層比作用儲存層。
- **資料存取成本**︰ 製作出酷炫的儲存空間層中的資料，您需要付費 gb 每個資料存取收費讀取和寫入。
- **交易成本**︰ 有兩個層級的每一筆費用。 不過，製作出酷炫的儲存空間層的每一筆成本是高於作用儲存層。
- **地理複寫資料傳輸成本**︰ 這只適用於帳戶與地理複寫設定，包括 GRS 和 RA GRS。 地理複寫的資料傳輸作業需要每 gb 收費。
- **輸出資料傳輸成本**︰ 輸出資料傳輸 （傳送不在 Azure 的地區的資料） 會造成頻寬使用量每 gb 每用途的儲存空間帳戶與帳單。
- **變更儲存層**︰ 從至熱炫變更的儲存空間層會對等於讀取每個轉換儲存帳戶中的所有資料的費用。 另一方面，變更炫從作用儲存層是成本的免費。

> [AZURE.NOTE] 若要讓使用者試用新的儲存空間層和驗證功能文章啟動，變更儲存空間的費用從至熱炫層將會 waived 關閉直到年 6 月 30 2016年。 開始年 7 月 1 2016年，費用會套用到所有的轉場效果至熱炫從。 如需詳細資訊的價格的模型，Blob 儲存體帳戶請參閱[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)的頁面。 如需詳細資訊，在輸出資料傳輸費用請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)] 頁面。

## <a name="quick-start"></a>快速入門

此區段中，我們會示範下列情況使用 Azure 入口網站︰

- 如何建立 Blob 儲存體帳戶。
- 如何管理 Blob 儲存體帳戶。

### <a name="using-the-azure-portal"></a>使用 [Azure 入口網站

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>建立 Blob 儲存體帳戶使用 Azure 入口網站

1. [Azure 入口網站](https://portal.azure.com)登入。

2. 在中樞] 功能表中，選取 [**新增** > **資料 + 的儲存空間** > **儲存的帳戶**。

3. 輸入您儲存帳戶的名稱。

    此名稱必須是全域唯一的。用來存取儲存帳戶中的物件的 URL 的一部分使用。  

4. 選取**資源管理員**部署模型。

    資源管理員儲存帳戶; 可以僅能使用分層儲存空間這是新的資源的建議的部署模型。 如需詳細資訊，請參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。  

5. 在 [帳戶類型] 下拉式清單中，選取 [ **Blob 儲存體**]。

    這是帳戶的您選取的儲存空間類型。 分層的儲存不適用於用途的儲存空間。它只適用於 Blob 儲存體類型帳戶。    

    請注意，當您選取此選項時，效能層會設定為標準。 無法使用與進階版效能層分層儲存空間。

6. 選取的儲存空間帳戶複寫選項︰ **LRS**、 **GRS**或**RA GRS**。 預設值為**RA GRS**。

    LRS = 本機多餘的儲存空間。GRS = 地理多餘的儲存空間 （2 地區）。RA GRS 是讀取權限地理多餘的儲存空間 （第二個 「 讀取 」 權限與 2 區域）。

    如需 Azure 儲存體複寫選項的詳細資訊，請參閱[Azure 儲存體複寫](storage-redundancy.md)。

7. 選取您的需求的右邊的儲存空間層︰ 設為**製作出酷炫**] 或 [**作用**中的**存取層**。 預設值為**作用**。

8. 選取您要建立新的儲存空間帳戶的訂閱。

9. 指定新的資源群組，或選取現有的資源群組。 如需有關資源群組的詳細資訊，請參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。

10. 選取您儲存帳戶的地區。

11. 按一下 [**建立**]，建立儲存帳戶]。

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>變更 Blob 儲存體帳戶使用 Azure 入口網站的儲存空間層

1. [Azure 入口網站](https://portal.azure.com)登入。

2. 若要瀏覽至您儲存的帳戶，請選取所有的資源，然後選取您儲存的帳戶。

3. 在設定刀中，按一下 [**設定**檢視及/或變更帳戶設定]。

4. 選取您的需求的右邊的儲存空間層︰ 設為**製作出酷炫**] 或 [**作用**中的**存取層**。

5. 按一下 [儲存刀頂端。

> [AZURE.NOTE] 變更儲存層可能會導致額外的費用。 請如需詳細資訊的[價格及帳單](storage-blob-storage-tiers.md#pricing-and-billing)] 區段，參閱。

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>評估，和移轉至 Blob 儲存體帳戶

本節的目的，是可協助使用者進行平滑的轉場效果，使用 Blob 儲存體帳戶。 有兩個使用者案例︰

- 您有現有的一般用途的儲存空間帳戶，而且想要評估與右邊的儲存空間層 Blob 儲存體帳戶變更。
- 您決定要使用 Blob 儲存體帳戶或已經有一個，並且想要評估是否應使用的快速鍵或製作出酷炫的儲存空間層。

在這兩種情況下，首先是成本儲存和存取您儲存在 Blob 儲存體帳戶中的資料，比較您目前的成本。

### <a name="evaluating-blob-storage-account-tiers"></a>評估 Blob 儲存體帳戶層

以估計成本儲存和存取儲存在 Blob 儲存體帳戶中的資料，您必須評估您現有的使用模式或估算您的預期的使用模式。 一般而言，您會想知道︰

- 您的儲存空間消耗-如何儲存的資料量，以及如何這會變更每個月嗎？
- 您儲存空間的存取方式-資料量已讀取和寫入 （包括新的資料） 的帳戶？ 資料存取，請使用多少交易，以及哪些類型的交易它們？

#### <a name="monitoring-existing-storage-accounts"></a>監控現有的儲存空間帳戶

監控您現有的儲存空間帳戶和收集資料，您可以利用 Azure 儲存體分析執行記錄，並提供儲存帳戶的指標資料。
儲存分析可以儲存包含彙總的交易用途儲存帳戶以及 Blob 儲存體帳戶 Blob 儲存體服務要求的相關統計資料和容量資料的指標。
此資料儲存在相同的儲存空間帳戶中的已知的表格中。

如需詳細資訊，請參閱[關於儲存分析指標](https://msdn.microsoft.com/library/azure/hh343258.aspx)和[儲存分析指標表格的結構描述](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] Blob 儲存體帳戶公開僅適用於儲存和存取該帳戶的指標資料的表格服務端點。

若要監視儲存消耗量 Blob 儲存體服務，您將需要啟用容量指標。
使用此啟用，容量資料被錄製儲存帳戶的 Blob 服務的每日和寫入內的相同的儲存空間帳戶*$MetricsCapacityBlob*資料表中的表格項目為記錄。

若要監控 Blob 儲存體服務資料的存取方式，您必須啟用下限交易指標 API 層級。
使用此啟用，每個 API 交易彙總的每個小時，而寫入內的相同的儲存空間帳戶*$MetricsHourPrimaryTransactionsBlob*資料表中的表格項目為記錄。 *$MetricsHourSecondaryTransactionsBlob*表格記錄至第二個端點若 RA GRS 儲存帳戶的交易。

> [AZURE.NOTE] 在必要用途的儲存空間，您帳戶頁面 blob 與同時區塊的虛擬機器磁碟儲存附加 blob 資料時，就不適用此估計程序。 這是 blob 的因為您將無法用來分辨容量的交易指標只根據的類型，封鎖與附加 blob 的可以移轉到 Blob 儲存體帳戶。

若要取得您的資料使用和存取模式的好近似值，我們建議您選擇保留期間您一般的使用狀況，代表眼及推斷。
其中一個選項是月份的 7 天保留計量資料，以及收集資料，請在每一週，分析結尾。
另一個選項是 30 天的保留計量資料和收集並分析資料 30 天內的結尾。

如需啟用的詳細資訊，來收集及檢視指標資料，請參閱，[啟用 Azure 儲存指標及檢視指標資料](storage-enable-and-view-metrics.md)。

> [AZURE.NOTE] 儲存、 存取及下載分析資料也會負責就像一般的使用者資料。

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>利用使用指標來估計成本

##### <a name="storage-costs"></a>儲存空間的成本

容量計量表*$MetricsCapacityBlob*列按鍵*[資料]*中的最新項目會顯示由使用者資料的儲存容量。
最新的項目中容量指標表格*$MetricsCapacityBlob*與列鍵*」 分析 」*會顯示使用狀況分析記錄的儲存容量。

此容量總數由兩個使用者資料和分析記錄 （若啟用） 然後可用來儲存資料的儲存空間帳戶中的成本。
相同的方法也可用於評估區塊的儲存空間成本，並新增二進位大型物件在一般用途的儲存空間的帳戶。

##### <a name="transaction-costs"></a>交易成本

總和*'TotalBillableRequests'*，在 API 交易指標資料表中的所有項目會指出該特定的 API 的交易的總數。 *例如*，在指定期間內的*「 GetBlob'*交易的總數可以依據加總的所有項目的總計費要求以計算資料列識別碼*' 使用者資訊。GetBlob'*。

以估計 Blob 儲存體帳戶的交易成本，您必須劃分成三個群組的交易，因為它們以不同方式價格。

- 撰寫交易，例如*'PutBlob'*、 *'PutBlock'*、 *'PutBlockList'*、 *'AppendBlock'*、 *'ListBlobs'*、 *'ListContainers'*、 *'CreateContainer 」*、 *「 SnapshotBlob 」*及*'CopyBlob'*。
- 刪除交易，例如*「 DeleteBlob 」*和*「 DeleteContainer 」*。
- 所有其他的交易。

若要估計的一般用途儲存帳戶的交易成本，您要彙總所有交易不管作業/API 資料。

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>資料存取和地理複寫的資料傳輸成本

時儲存分析不提供的讀取和寫入儲存帳戶的資料量，其可大致估計查看交易計量資料表。
在 API 交易指標資料表中的所有項目*」 TotalIngress 」*的總和表示該特定的 API 位元組中輸入資料的總數量。
同樣的*「 TotalEgress 」*的總和指出輸出資料，請在位元組的總數量。

以 Blob 儲存體帳戶估計資料的存取，您必須劃分成兩個群組的交易。

- 查看總和*'TotalEgress 「*主要*」 GetBlob 」*和*「 CopyBlob'*作業來評估從儲存的帳戶所擷取的資料量。
- 可查看*「 TotalIngress 」*的總和的主要*'PutBlob'*、 *'PutBlock 」*、 *「 CopyBlob 」*和*「 AppendBlock 」*作業來估計的寫入儲存帳戶的資料量。

也可以使用寫入 GRS 或 RA GRS 儲存帳戶的情況下的資料時數的估計計算地理複寫 Blob 儲存體帳戶的資料傳輸的成本。

> [AZURE.NOTE] 如需關於計算使用中的快速鍵或製作出酷炫的儲存空間層的成本詳細範例中，請查看標題為*「 什麼是作用和炫存取層及如何應該決定要 use? 一個 」*的常見問題集 在 [[價格] 頁面的 Azure 儲存空間](https://azure.microsoft.com/pricing/details/storage/)。

### <a name="migrating-existing-data"></a>移轉現有的資料

Blob 儲存體帳戶專門用來儲存只區塊，並附加二進位大型物件。 現有的一般用途的儲存空間帳戶，讓您儲存資料表、 佇列、 檔案和磁碟，以及二進位大型物件，無法轉換至 Blob 儲存體帳戶。 若要使用的儲存空間層，您必須建立新的 Blob 儲存體帳戶，並將您的現有資料移轉至新建立的帳戶。
您可以使用下列方法，將現有資料移轉到從內部部署存放裝置、 從協力廠商的雲端儲存空間提供者，或者從現有用途的儲存空間帳戶中 Azure Blob 儲存體帳戶︰

#### <a name="azcopy"></a>AzCopy

AzCopy 是專為高效能複製的資料與 Azure 儲存體 Windows 命令列公用程式。 若要將資料複製到您的 Blob 儲存體帳戶，從您現有的一般用途的儲存空間帳戶，或將資料從您的內部部署存放裝置上傳至您的 Blob 儲存體帳戶，您可以使用 AzCopy。

如需詳細資訊，請參閱[將資料以 AzCopy 命令列公用程式傳送](storage-use-azcopy.md)。

#### <a name="data-movement-library"></a>資料移動文件庫

Azure 儲存資料移動文件庫的.NET 根據力量 AzCopy 核心資料移動架構。 高效能、 可靠和簡單的資料傳輸作業 AzCopy 類似的設計文件庫。 這個選項可讓您利用所提供的功能 AzCopy 應用程式中原本就不必處理執行，並監控 AzCopy 外部執行個體的完整優點。

如需詳細資訊，請參閱[Azure 儲存資料移動文件庫的.Net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>REST API 或用戶端文件庫

您可以建立自訂的應用程式至 Blob 儲存體帳戶使用其中一個 Azure 用戶端文件庫或 Azure 儲存服務 REST API 移轉您的資料。 Azure 儲存體提供多種語言和.NET、 Java、 c + +、 Node.JS、 PHP]、 [注音標示及 Python 等平台的豐富型用戶端文件庫。 用戶端程式庫提供進階的功能，例如重試邏輯、 記錄及平行上傳。 您也可以直接對 REST API，可以讓 HTTP/HTTPS 要求任何語言稱為開發。

如需詳細資訊，請參閱[開始使用 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

> [AZURE.NOTE] 使用用戶端加密加密 blob 儲存加密相關與 blob 儲存的中繼資料。 這是絕對關鍵任何複製機制應該確認 blob 中繼資料和特別是加密相關的中繼資料，會保留。 如果複製這個中繼資料不 blob，blob 內容不會擷取一次。 如需有關加密相關的中繼資料的詳細資訊，請參閱[Azure 儲存體用戶端加密](storage-client-side-encryption.md)。

## <a name="faqs"></a>常見問題集

1. **現有仍可使用的儲存空間帳戶？**

    是的現有的儲存空間帳戶仍可使用及時不會變更價格或功能。  他們沒有選擇儲存層的能力，並不會在未來有 tiering 功能。

2. **為什麼以及何時應該開始使用 Blob 儲存體帳戶？**

    Blob 儲存體帳戶專為儲存二進位大型物件，並且允許我們介紹 blob 為主的新功能。 轉接，Blob 儲存體帳戶的建議方式是儲存為未來的功能，例如階層式的儲存空間的二進位大型物件，並且 tiering 將介紹帳戶類型。 不過，則最多時您想要移轉根據您的業務需要。

3. **我可以將現有的儲存空間我的帳戶轉換 Blob 儲存體帳戶？**

    [否]。 Blob 儲存體帳戶不同種類的儲存空間帳戶，而您則必須建立新並上述移轉您的資料。

4. **可以儲存物件在兩個儲存空間中層同一個帳戶？**

    表示儲存層*' 存取層]*屬性設定帳戶層級，且適用於該帳戶中的所有物件。 您無法在物件層級設定存取層屬性。

5. **我可以變更我的 Blob 儲存體帳戶的儲存空間層嗎？**

    [是]。 您可以變更的儲存空間層儲存帳戶設定*」 存取層 '*屬性。 變更儲存層會套用至儲存在帳戶的所有物件。 炫從作用儲存層不會對任何費用，從至熱炫變更時的變更會對每 GB 成本閱讀帳戶中的所有資料。

6. **頻率可以變更我的 Blob 儲存體帳戶的儲存空間層？**

    雖然我們不會強制執行限制可以變更的儲存空間層的頻率，請注意到熱炫從變更的儲存空間層的會造成嚴重的費用。 不建議您經常變更的儲存空間層。

7. **會在製作出酷炫的儲存空間層 blob 會有不同行為比在作用儲存層中的項目？**

    二進位大型物件在作用儲存層用途的儲存空間帳戶中有二進位大型物件為相同的延遲。 二進位大型物件製作出酷炫的儲存空間層用途的儲存空間帳戶中有為二進位大型物件類似延遲 （以毫秒為單位）。

    二進位大型物件製作出酷炫的儲存空間層會有較低顯示狀態服務比層級 (SLA) blob 儲存在作用儲存層。 如需詳細資訊，請參閱[SLA 儲存空間](https://azure.microsoft.com/support/legal/sla/storage)。

8. **我能將儲存頁面二進位大型物件及虛擬機器磁碟在 Blob 儲存體帳戶？**

    Blob 儲存體帳戶支援只區塊，和加二進位大型物件，以及不頁面二進位大型物件。 Azure 虛擬機器磁碟頁面二進位大型物件以備份，因此 Blob 儲存體帳戶無法用來儲存虛擬機器磁碟。 不過也可為 Blob 儲存體帳戶區塊 blob 儲存備份的虛擬機器磁碟。

9. **我是否需要變更我現有的應用程式，以使用 Blob 儲存體帳戶？**

    Blob 儲存體帳戶 100 %api 組塊用途的儲存空間帳戶與一致，而且附加二進位大型物件。 只要您的應用程式使用封鎖二進位大型物件，或附加 blob，您使用的[儲存空間服務 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx)或更大的 2014年-02-14 版本然後應該只會使用您的應用程式。 如果您使用的舊版的通訊協定，您會需要更新您的應用程式使用新的版本，以便能順利使用兩種類型的儲存空間帳戶。 一般而言，我們一律使用最新版本，無論儲存帳戶的建議您輸入您使用。

10. **會有使用者體驗的變更？**

    Blob 儲存體帳戶，兩者十分相似儲存組塊用途的儲存空間帳戶和附加 blob，與支援 Azure 儲存體，包括高持續性及可用性、 延展性、 效能及安全性所有重要功能。 非功能和限制特定 Blob 儲存體帳戶和其上方，一切已呼叫的儲存空間層級保持不變。

## <a name="next-steps"></a>後續步驟

### <a name="evaluate-blob-storage-accounts"></a>評估 Blob 儲存體帳戶

[查看依地區 Blob 儲存體帳戶的顯示狀態](https://azure.microsoft.com/regions/#services)

[啟用 Azure 儲存指標評估您目前的儲存空間帳戶使用方式](storage-enable-and-view-metrics.md)

[核取 [依地區價格的 Blob 儲存體](https://azure.microsoft.com/pricing/details/storage/)

[核取資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>開始使用 Blob 儲存體帳戶

[開始使用 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)

[移動資料，以及從 Azure 儲存體](storage-moving-data.md)

[傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)

[瀏覽與探索您儲存的帳戶](http://storageexplorer.com/)
