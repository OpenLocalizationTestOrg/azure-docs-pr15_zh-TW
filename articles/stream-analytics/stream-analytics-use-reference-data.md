<properties
    pageTitle="在 [串流分析中使用參考資料與查閱表格 |Microsoft Azure"
    description="使用資料流分析查詢中的 [參考資料"
    keywords="查閱表格]，[參考資料"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>使用中的資料流分析的輸入資料流的參考資料或查閱表格

參考資料 （也稱為查閱表格） 是以資料集的靜態或減慢性，變更用來執行查閱或與您的資料流。 若要使用 Azure 資料流分析工作中的 [參考資料會通常使用[參考資料加入](https://msdn.microsoft.com/library/azure/dn949258.aspx)您在查詢中。 資料流分析使用 Azure Blob 儲存體儲存層作為參考資料和 Azure 資料工廠參考資料可以會轉換及/或從[任何數目的雲端基礎和內部部署資料存放區](../data-factory/data-factory-data-movement-activities.md)複製到 Azure Blob 儲存體，作為參考資料。 參考資料都以二進位大型物件 （在輸入設定中所定義） 以遞增順序 blob 名稱中指定日期/時間的序列建立模型。 它**只**支援使用日期/時間**大於**指定順序中的最後一個 blob 的項目新增至順序結尾。

## <a name="configuring-reference-data"></a>設定參考資料

若要設定您的參考資料，您必須建立為類型**參考資料**輸入。 下表說明您需要提供時建立參考資料輸入描述每個屬性︰

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>描述</td>
</tr>
<tr>
<td>輸入的別名</td>
<td>將工作查詢中用來參照這個輸入好記的名稱。</td>
</tr>
<tr>
<td>儲存帳戶</td>
<td>Blob 檔案的所在位置的儲存體帳戶名稱。 如果這是在同一份訂閱為您的資料流分析工作中，您可以從下拉式清單選取向下。</td>
</tr>
<tr>
<td>儲存帳戶金鑰</td>
<td>儲存帳戶相關聯私密金鑰。 這會自動填入的儲存空間帳戶是在同一份訂閱為您的資料流分析工作。</td>
</tr>
<tr>
<td>儲存容器</td>
<td>容器提供 blob 儲存在 Microsoft Azure Blob 服務中的邏輯群組。 當您上載 blob Blob 服務時，您必須指定為該 blob 的容器。</td>
</tr>
<tr>
<td>路徑圖樣</td>
<td>用來找出您的二進位大型物件指定容器中的檔案路徑。 內路徑，您可以選擇指定下列 2 變數的一或多個執行個體︰<BR>{日期}，{time}<BR>範例 1: products/{date}/{time}/product-list.csv<BR>範例 2: products/{date}/product-list.csv
</tr>
<tr>
<td>[選擇性] 的日期格式</td>
<td>如果您有使用 {日期} 您指定路徑模式中，您可以從下拉式清單的支援格式選取組織檔案的 [日期] 格式。 範例︰ YYYY/MM/DD</td>
</tr>
<tr>
<td>[選擇性] 時間格式</td>
<td>如果您有使用 {time} 您指定路徑模式中，您可以從下拉式清單的支援格式選取組織檔案的時間格式。 範例︰ HH</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>若要確定您的查詢的運作的方式預期，必須知道的序列化格式串流分析您使用的內送的資料流。 參考資料] 中支援的格式為 CSV 和 JSON。</td>
</tr>
<tr>
<td>編碼</td>
<td>Utf-8 這次是唯一的支援編碼格式</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>產生的排程的參考資料

如果您的參考資料緩資料集，然後重新整理資料已啟用指定路徑模式中使用 {日期} 輸入組態參照支援和 {時間} 替代權杖。 根據此路徑模式的更新的參考資料定義挑選串流分析。 例如的圖樣`sample/{date}/{time}/products.csv`以及**「 YYYY-MM-DD 」**及時間的日期格式的**「 hh: mm 」**格式會指示串流分析，選擇 [更新 blob`sample/2015-04-16/17:30/products.csv`上年 4 月 16 2015 UTC 5:30 PM，時區。

> [AZURE.NOTE] 目前資料流分析工作 blob 重新整理只時尋找電腦時間前往編碼 blob 名稱中的時間。 工作會尋找例如`sample/2015-04-16/17:30/products.csv`一經可能但稍早於 4 月 16 2015 UTC 上 5:30 PM 時區。 它會*永遠不會*編碼時間早於發現的最後一個檔案的外觀。
> 
> 例如︰ 後的工作會找出 blob`sample/2015-04-16/17:30/products.csv`會忽略編碼的日期早於 5:30 PM 年 4 月 16 2015年任何檔案因此，如果落後送達`sample/2015-04-16/17:25/products.csv`blob 取得建立相同的容器中的工作不會使用它。
> 
> 同樣地如果`sample/2015-04-16/17:30/products.csv`，才會產生下午 10:03 年 4 月 16 2015年但沒有 blob 較早的日期與容器中，則工作會使用下午 10:03 年 4 月 16 2015年啟動此檔案，就使用先前的參考資料。
> 
> 此例外狀況當工作需要重新處理資料的時間，或第一個工作時開始。 在工作所尋找最新的 blob 之前作業所產生的開始時間啟動指定的時間。 這是以確保**非空白**的參考資料集時工作開始。 如果其中一個找不到，該工作會顯示下列診斷︰ `Initializing input without a valid reference data blob for UTC time <start time>`。


[Azure 資料工廠](https://azure.microsoft.com/documentation/services/data-factory/)可用來協調建立更新的 blob 更新參考資料定義資料流分析所需的工作。 資料工廠是以雲端為基礎的資料整合服務協調，會自動執行的動作及資料轉換。 資料工廠支援[連接到大量的雲端與內部部署資料存放區](../data-factory/data-factory-data-movement-activities.md)及移動資料輕鬆地您指定的定期排程。 如需詳細資訊以及如何設定產生參考資料重新整理排程預先定義的資料流分析資料工廠管線逐步指引，請參閱此[GitHub 範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)。

## <a name="tips-on-refreshing-your-reference-data"></a>重新整理您的參考資料的秘訣 ##

1. 覆寫 [參考資料 blob 並不會導致重新載入 blob 串流分析，在某些情況下，可能會導致失敗的工作。 若要變更參考資料的建議的方式若要新增新 blob 使用相同的容器，而路徑圖樣工作輸入中定義，並使用 [日期/時間**大於**指定順序中的最後一個 blob 的項目。
2.  二進位大型物件並未排序 blob 的 「 上次修改日期 」 的時間，但是只能由 blob 中指定的日期和時間的參考資料命名使用 {日期}，{時間} 替代]。
3.  在工作必須返回時間的一些情況下，因此參考資料 blob 必須不變更或刪除。

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
您已經知道什麼是資料流分析，受管理的服務串流分析的資料是來自網際網路的項目。 若要進一步瞭解這項服務，請參閱︰

- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
