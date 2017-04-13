<properties
   pageTitle="資料表設計工具建構篩選字串 |Microsoft Azure"
   description="資料表設計工具建構篩選字串"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>[資料表設計工具建構篩選字串

## <a name="overview"></a>概觀

若要篩選 Azure Visual Studio**資料表設計工具**中顯示的表格中的資料，您會建構篩選字串，並輸入到 [篩選] 欄位。 篩選字串語法所定義之 WCF Data Services 及 SQL WHERE 子句，類似，但會傳送至表格服務，透過 HTTP 要求。 **資料表設計工具**處理適當編碼了，因此，若要在您要的屬性值篩選，您需要只輸入屬性名稱、 比較運算子、 準則值，與您也可以布林值運算子篩選欄位中的。 您不需要即如果您已建構 URL 查詢透過[儲存服務 REST API 參照](http://go.microsoft.com/fwlink/p/?LinkId=400447)資料表包含 $filter 查詢選項。

WCF Data Services 為基礎[開放式資料通訊協定](http://go.microsoft.com/fwlink/p/?LinkId=214805)(OData)。 篩選系統查詢選項 (**$filter**) 的詳細資訊，請參閱[OData URI 慣例規格](http://go.microsoft.com/fwlink/p/?LinkId=214806)。

## <a name="comparison-operators"></a>比較運算子

邏輯運算子支援所有內容類型︰

|邏輯運算子|描述|篩選字串的範例|
|---|---|---|
|eq|等於|縣/市 eq '雷蒙市 」|
|gt|大於|價格 gt 20|
|ge|大於或等於|價格 ge 10|
|lt|小於|價格 lt 20|
|le|小於或等於|價格 le 100|
|ne|不等於|縣/市 ne '倫敦 」|
|與|與|價格 le 200 和價格 gt 3.5|
|或|或|價格 le 3.5 或價格 gt 200|
|不|不|不 isAvailable|

建構篩選字串，下列規則很重要︰

- 您可以使用邏輯運算子來比較的屬性值。 請注意，它可能比較動態值; 屬性運算式的一側必須是常數。

- 篩選字串的所有部分都會區分大小寫。

- 固定的值必須屬於相同的資料類型的屬性篩選的順序來傳回正確的結果。 如需支援的內容類型的詳細資訊，請參閱[瞭解表格服務的資料模型](http://go.microsoft.com/fwlink/p/?LinkId=400448)。

## <a name="filtering-on-string-properties"></a>字串屬性篩選

當您篩選字串的屬性時，請以單引號括住括住的字串常數。

下列範例篩選**PartitionKey**和**RowKey**屬性;非索引鍵的其他屬性也無法加入篩選字串︰

    PartitionKey eq 'Partition1' and RowKey eq '00001'

您可以括住每個篩選運算式括號括住，雖然這不是必要︰

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

請注意，表格服務不支援萬用字元查詢，並不支援在 [資料表設計工具中表示。 不過，您可以執行符合所要的前置詞使用的比較運算子的前置字元。 下列範例會傳回包含字母 「 A 」 開頭的姓氏屬性的實體︰

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>篩選數字的屬性

若要篩選的整數或浮點數字上，指定數字沒有用引號。

此範例會傳回的值大於 30 年齡屬性的所有項目︰

    Age gt 30

此範例會傳回的值小於或等於 100.25 AmountDue 屬性的所有項目︰

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>布林值屬性篩選

若要篩選的布林值，指定**為 true**或**false**沒有用引號。

下列範例會傳回所有項目位置 IsActive 屬性設為**true**:

    IsActive eq true

您也可以撰寫此沒有使用邏輯運算子的篩選運算式。 在下列範例中，表格服務也會傳回所有項目中 IsActive 為**true**:

    IsActive

若要傳回所有實體其中 IsActive 是 false，您可以使用 not 運算子︰

    not IsActive

## <a name="filtering-on-datetime-properties"></a>DateTime 屬性篩選

若要篩選日期時間值，指定**日期時間**關鍵字，後面接著以單引號括住的日期/時間常數。 日期/時間常數必須合併 UTC 格式，[設定格式化的日期時間屬性值](http://go.microsoft.com/fwlink/p/?LinkId=400449)所述。

下列範例會傳回 CustomerSince 屬性等於 2008 年 7 月 10 日的項目︰

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
