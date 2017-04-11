<properties
    pageTitle="如何在 Azure 搜尋複雜的資料類型的模型 |Microsoft Azure 搜尋"
    description="巢狀或階層式資料結構的模型中使用平面資料列集和集合資料類型 Azure 搜尋索引。"
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>如何在 Azure 搜尋模型複雜的資料類型

用來填入 Azure 搜尋索引有時的外部資料集包含不會中斷向下整齊至表格式資料列集的階層式或巢狀子結構。 這種結構的範例可能包含多個位置和電話號碼的單一客戶、 多個色彩和大小的單一活頁簿，多位作者的 SKU 等等。 模型條款，您可能會看到這些結構稱為*複雜的資料類型*、*複合資料型別*、*複合索引的資料類型*，或是*彙總資料類型*等等。

在 Azure 搜尋，原本即不支援複雜的資料類型，但經過驗證的因應措施包含兩個步驟的程序簡維結構]，然後使用 [以重新組成內部結構的 [**集合**資料類型。 遵循本文中所述的技巧允許內容進行搜尋多面向，排序和篩選。

## <a name="example-of-a-complex-data-structure"></a>複雜的資料結構的範例

一般而言，為一組 JSON 或 XML 文件或項目，例如 DocumentDB NoSQL 存放區中，且存有資料問題。 結構，挑戰源自有多個搜尋並篩選所需要的子項目。  起點的說明因應措施將下列 JSON 文件會列出一組連絡人作為範例︰

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

欄位名稱 'id'，「 名稱 」 和 「 公司 」 可以輕鬆地對應一對一內 Azure 搜尋索引的欄位，但 [位置] 欄位包含陣列的位置，有兩個位置的識別碼以及位置描述的一組。 假設 Azure 搜尋未與任何支援的資料類型，我們需要使用不同的方式來建立此模型 Azure 搜尋中。 

> [AZURE.NOTE] 這項技巧也描述吳 Evans 部落格文章[Azure 搜尋與編製索引的 DocumentDB](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/)，技巧稱為 「 簡維資料 」 會顯示在您有一部分的欄位，`locationsID`和`locationsDescription`同時[集合](https://msdn.microsoft.com/library/azure/dn798938.aspx)（或陣列中的字串）。   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>第 1 部分︰ 簡陣列維至個別功能變數

若要建立可容納此資料集的 Azure 搜尋索引，建立個別功能變數的巢狀子結構︰`locationsID`和`locationsDescription`與資料類型的[集合](https://msdn.microsoft.com/library/azure/dn798938.aspx)（或陣列中的字串）。 這些欄位中您想要編製索引 '1' 和 [2] 的值至`locationsID`王寶生和 '3 」 和 「 4 」 的值的欄位將`locationsID`珍菱的欄位。  

您 Azure 搜尋中的資料看起來像這樣︰ 

![範例資料，2 個資料列](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>第 2 部分︰ 在索引定義加入集合欄位。

索引結構描述中看起來類似此範例中的欄位定義。

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>驗證搜尋行為，您也可以延伸 [索引

假設您已建立索引並載入資料，您現在可以測試方案，以確認對資料集的搜尋查詢執行。 每個**集合**] 欄位應**可搜尋**，**可篩選**和**facetable**。 您可以執行查詢，例如︰

* 尋找所有在 Adventureworks 總部地址工作的人員。
* 取得常用 Office 」 中工作的人員的數目。  
* 在 「 常用 Office' 工作的人員，顯示其運作以及每個位置之人員的計數哪些其他辦公室。  

這項技巧相隔落的位置，就必須執行搜尋，結合的位置識別碼以及位置描述。 例如︰

* 找出所有人員在他們擁有常用 Office，而且有 4 的位置識別碼。  

如果您還記得原始內容看起來像這樣︰

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

不過，現在，我們有資料分成不同欄位中，我們也無法瞭解 if 的常用 Office 的珍菱與`locationsID 3`或`locationsID 4`。  

若要處理這種情況，請在索引中的所有資料合併成單一集合定義另一個欄位。  我們的範例中，我們會呼叫此欄位`locationsCombined`，我們將不同的內容與`||`雖然您可以選擇任何您認為的分隔符號是一組唯一的字元，為您的內容。 例如︰ 

![範例資料，且分隔符號的資料列 2](./media/search-howto-complex-data-types/sample-data-2.png)

使用此`locationsCombined`] 欄位中，我們可以現在包含更多的查詢，例如︰

* 顯示在 ' 常用 Office 」 位置識別碼的 「 4' 工作的人員。  
* 搜尋工作常用 office 」 的人員位置識別碼 」 4 」。 

## <a name="limitations"></a>限制

這項技巧很有用的情況下，但不是哪一種情況適用。  例如︰

1. 如果您沒有在您的複雜的資料類型一組靜態的欄位，沒有方法，將所有可能的類型對應到一個欄位。 
2. 更新巢狀的物件，您必須決定完全需要更新 Azure 搜尋索引中一些額外的工作

## <a name="sample-code"></a>程式碼範例

您可以看到範例 Azure 搜尋索引複雜 JSON 資料集及執行查詢的數字在此[GitHub repo](https://github.com/liamca/AzureSearchComplexTypes)此資料集的方式。

## <a name="next-step"></a>下一步

Azure 搜尋 UserVoice 上[的複雜的資料類型的原生支援投票](https://feedback.azure.com/forums/263029-azure-search)頁面，並輸入任何其他您想要我們可以考慮有關功能實作。 您可以也連絡我直接在 Twitter 上@liamca。


 