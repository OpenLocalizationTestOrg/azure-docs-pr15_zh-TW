<properties
    pageTitle="建立 Azure 搜尋索引 |Microsoft Azure |裝載的雲端搜尋服務"
    description="什麼是 Azure 搜尋索引，以及如何使用？"
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index"></a>建立 Azure 搜尋索引
> [AZURE.SELECTOR]
- [概觀](search-what-is-an-index.md)
- [入口網站](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [其餘](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>什麼是索引？

*索引*是*文件*和其他建構 Azure 搜尋服務所使用的持續存放區。 文件是單一單位的搜尋索引中的資料。 例如，電子商務零售商可能會有每個項目這些文件、 新聞組織可能會有每個文件等文件。將這些概念對應至更容易記住的資料庫對應項目︰*索引*是概念上類似*資料表*中，且*文件*數約略等於表格中的*資料列*。

當您新增/上傳文件並送出到 Azure 搜尋的搜尋查詢時，您會在您的搜尋服務提交您要求的特定索引。

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>欄位類型和 Azure 搜尋索引中的屬性

當您定義您的結構描述，您必須指定索引中的名稱、 類型和每個欄位的屬性。 欄位類型會分類會儲存在該欄位的資料。 個別功能變數會設定屬性來指定欄位的使用方式。 下表列舉類型，您可以指定的屬性。


### <a name="field-types"></a>欄位類型
|類型|描述|
|------------|-----------|
|*Edm.String*|您也可以選擇 （斷字、 失敗等） 的全文檢索搜尋 token 化的文字。|
|*Collection(Edm.String)*|您也可以選擇的全文檢索 token 化的字串的清單。 集合中的項目數目上限沒有理論但內容大小 16 MB 右上限制適用於集合。|
|*Edm.Boolean*|包含 true/false 值。|
|*Edm.Int32*|32 位元整數值。|
|*Edm.Int64*|64 位元整數值。|
|*Edm.Double*|雙精度數字的資料。|
|*Edm.DateTimeOffset*|日期時間值，表示 OData V4 格式 (例如`yyyy-MM-ddTHH:mm:ss.fffZ`或`yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`)。|
|*Edm.GeographyPoint*|點，代表地球上的地理位置。|

您可以找到 Azure 搜尋[支援 MSDN 上的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx)的詳細的資訊。



### <a name="field-attributes"></a>欄位屬性
|屬性|描述|
|------------|-----------|
|*索引鍵*|字串，提供每個文件中，用來設定文件外觀的唯一識別碼。 每個索引必須有一個索引鍵。 只有一個欄位鍵，而其類型必須設定為 [Edm.String。|
|*除外*|指定欄位是否可以傳回搜尋結果中。|
|*可篩選*|可讓功能變數，用來篩選查詢。|
|*可排序*|允許排序搜尋結果，使用此欄位的查詢。|
|*Facetable*|可讓[多面向導覽](search-faceted-navigation.md)結構中用於使用者自我引導篩選的欄位。 通常包含分成多份文件時，您可以使用的重複值的欄位 （例如，多個文件的單一品牌或服務類別目錄] 底下） 適合為 facet。|
|*可搜尋*|標記為全文檢索欄位可搜尋。|

您可以找到有關 Azure 搜尋[MSDN 上的索引屬性](https://msdn.microsoft.com/library/azure/dn798941.aspx)的詳細的資訊。



## <a name="guidance-for-defining-an-index-schema"></a>指導定義索引結構描述

設計您的索引，花一些時間思考每個決策規劃階段中。 請務必設計您的索引，為每個欄位必須被指派[適當的屬性](https://msdn.microsoft.com/library/azure/dn798941.aspx)時您搜尋的使用者體驗與企業需求請謹記。 部署後，變更索引涉及重建和重新載入資料。


如果資料儲存需求變更一段時間，您可以增加或減少藉由新增或移除分割區的容量。 如需詳細資訊，請參閱[管理您的搜尋服務 Azure 中](search-manage.md)或[服務限制](search-limits-quotas-capacity.md)。
