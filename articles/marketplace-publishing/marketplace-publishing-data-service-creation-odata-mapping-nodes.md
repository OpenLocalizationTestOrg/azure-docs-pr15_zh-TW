<properties
   pageTitle="若要建立 Marketplace 資料服務的指南 |Microsoft Azure"
   description="如何建立、 認證及部署資料服務的詳細的指示購買 Azure marketplace。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>瞭解將現有的 web 服務對應至 CSDL 到 OData 的節點結構描述

>[AZURE.IMPORTANT] **現在我們已不再登入任何新的資料服務的發行者。新的 dataservices 非取得已核准的清單。** 如果您有您想要發佈上 AppSource SaaS 商務應用程式，您就可以找到更多資訊[以下](https://appsource.microsoft.com/partners)。 如果您有 IaaS 應用程式，或您想要發佈 Azure 服務商場的開發人員服務可以找到更多資訊[以下](https://azure.microsoft.com/marketplace/programs/certified/)。

這份文件可協助釐清對應 CSDL OData 通訊協定節點結構。 請注意，請務必節點結構是好格式的 XML。 因此根父項與子項結構描述適用於設計您 OData 對應時。

## <a name="ignored-elements"></a>忽略的項目
以下是高層級 CSDL 的項目 （XML 節點） 不打算使用 web 服務的中繼資料匯入期間 Azure Marketplace 後端。 他們可以簡報，但也會忽略。

| 項目 | 範圍 |
|----|----|
| 使用項目 | 節點子節點，所有的屬性 |
| 文件項目 | 節點子節點，所有的屬性 |
| ComplexType | 節點子節點，所有的屬性 |
| 關聯的項目 | 節點子節點，所有的屬性 |
| 擴充的屬性 | 節點子節點，所有的屬性 |
| EntityContainer | 下列的屬性會略過︰*延伸*並*AssociationSet* |
| 結構描述 | 下列的屬性會略過︰*命名空間* |
| FunctionImport | 下列的屬性會略過︰*模式*（假設 ln 的預設值） |
| EntityType | 下列子節點會被略過︰*索引鍵*和*PropertyRef* |

下列說明各種 CSDL XML 節點的詳細資料 （新增和略過的項目） 的變更。

## <a name="functionimport-node"></a>FunctionImport 節點
FunctionImport 節點表示公開給使用者服務的一個 URL （進入點）。 節點可以讓說明 URL 收件者的參數被提供給使用者，如何使用這些參數會提供。

在 [這裡] [MSDNFunctionImportLink] 找到此節點的詳細資料

[MSDNFunctionImportLink]: (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

以下是其他屬性 （或 [新增屬性]） 的所公開 FunctionImport 節點︰

**d:BaseUri** -
URI 其餘資源公開給服務商場的範本。 服務商場會使用範本建立 REST web service 對查詢。 URI 範本包含版面配置區的 {parameterName}，表單中的參數 parameterName 哪裡參數的名稱。 例如 apiVersion = {apiVersion}。
顯示為 URI 參數或部分的 URI 路徑允許參數。 若是在路徑的外觀，一律是強制 （無法標記為 null）。 *範例︰*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**名稱**-匯入的函數名稱。  不可以與其他 CSDL 中的已定義之名稱相同。  例如 名稱 = 「 GetModelUsageFile 」

**EntitySet***（選擇性）* -如果此函數會傳回一系列的實體類型， **EntitySet**的值必須是設定為 [集合所屬的實體。 否則，必須不使用**EntitySet**屬性。 *範例︰*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType***（選擇性）* -指定 URI 所傳回的項目類型。  如果此函數不會傳回一個值，則不要使用這個屬性。 以下是支援的類型︰

 - **集合 (<Entity type name>)**︰ 指定定義的實體類型的集合。 會出現在名稱屬性 EntityType 節點的名稱。 例如，集合 (WXC。HourlyResult)。
 - **Raw (<mime type>)**︰ 指定未經處理的文件/blob 傳回給使用者。 範例是 Raw(image/jpeg) 其他範例︰

  - ReturnType="Raw(text/plain) 」
  - ReturnType = 」 集合 （鼠尾草。DeleteAllUsageFilesEntity) 「 *

**d:Paging** -指定分頁其餘資源的處理方式。 會使用參數值內大 braches，例如頁面 = {$page} & itemsperpage = {$size} 是可用的選項︰

- **無︰**有不分頁
- **略過︰**分頁透過邏輯 「 略過 」 和 「 記錄 」 （上方） 來表示。 略過跳過 M 項目，並記錄，則會傳回的下一個 N 項目。 參數值︰ $skip
- **採取︰**會傳回下一個 N 項目。 參數值︰ $take
- **PageSize:**分頁透過邏輯頁面和大小 （每頁的項目） 來表示。 頁面代表會傳回目前頁面。 參數值︰ $page
- **大小︰**大小代表數字傳回每一個頁面上的項目。 參數值︰ $size

**d:AllowedHttpMethods***（選擇性）* -指定哪些動詞由其他資源。 此外，會接受的動詞限制為指定的值。  預設 = 文章。  *範例︰*`d:AllowedHttpMethods="GET"`可用的選項︰

- **取得︰**通常用來傳回的資料
- **文章︰**通常用來插入新的資料
- **放置︰**通常用來更新資料
- **刪除︰**用來刪除資料

子節點 （未涵蓋的 CSDL 文件） 中 FunctionImport 節點是︰

**d:RequestBody***（選擇性）*的要求主體用來表示要求預期會傳送本文。 參數可接受邀請本文中。 他們會中表示大括弧括住，例如: {parameterName}。 這些參數對應使用者輸入內容提供者的服務傳送本文中。 RequestBody 項目有名稱 httpMethod 具有的屬性。 屬性可讓兩個值︰

- **文章︰**使用如果邀請 HTTP 文章
- **取得︰**如果 HTTP GET 要求，使用

    範例︰

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces**和**d:Namespace** -這個節點說明 XML 所傳回函數匯入 （URI 結束點） 中定義的命名空間。 XML 所傳回的後端服務可能包含命名空間來區分的內容，則會傳回任何數的字。 **這些命名空間，如果在 d:Map 或 d:Match XPath 查詢中使用的所有需要列。** D:Namespaces 節點包含 d:Namespace 節點設定/清單。 每個列出一個後端服務回應中使用的命名空間。 以下是 d:Namespace 節點的屬性︰

-   **d:Prefix:**命名空間，XML 結果所傳回的服務，例如 f:FirstName、 f:LastName，其中 f 是前置詞中所見前置字元。
- **d:Uri:**使用結果文件中的命名空間完整 URI。 它會表示前置詞，會在執行階段解析為的值。

**d:ErrorHandling***（選用）*此節點含有錯誤處理的條件。 每個條件是根據結果傳回的內容提供者的服務進行驗證。 如果條件符合建議的 HTTP 錯誤碼錯誤訊息會傳回給使用者。

**d:ErrorHandling***（選擇性）*和**d:Condition** *（可省略）*條件節點會保留已由內容提供者的服務傳回結果中的一個條件。 以下是**必要**的屬性︰

- **d:Match:**驗證是否指定的節點/值存在於內容提供者的輸出 XML XPath 運算式。 XPath 會針對輸出執行，並應傳回 true，如果條件符合的項目或 false 否則。
- **d:HttpStatusCode:**HTTP 狀態碼應傳回之服務商場的大小寫在條件符合時。 服務商場 signalizes 使用者透過 HTTP 狀態碼錯誤。 在 http://en.wikipedia.org/wiki/HTTP_status_code 可 HTTP 狀態代碼清單
- **d:ErrorMessage:**錯誤訊息，會傳回-HTTP 狀態碼 – 給使用者。 應該是不含任何機密資料的好記的錯誤訊息。

**d:Title***（選擇性）* -可讓說明函數的標題。 標題的值來自

- 指定要在回應中傳回的服務要求中尋找標題選擇性地圖屬性 (xpath)。
- -或者-節點的值所指定的標題。

**d:Rights***（選擇性）* -函數與相關聯的權限 （例如著作權）。 來自權限] 的值︰

- 選用的地圖的屬性 (xpath) 指定要在回應中傳回的服務要求中尋找權限。
-   -或者-節點的值為指定的權限。

**d:Description***（選擇性）* -函數的簡短描述。 描述] 的值來自

- 指定要在回應中傳回的服務要求中尋找描述選擇性地圖屬性 (xpath)。
- -或者 – 描述指定為節點的值。

**d:EmitSelfLink** - *查看上述範例 「 '分頁 」 的 FunctionImport 傳回資料 」*

**d:EncodeParameterValue**到 OData 的選用副檔名

**d:QueryResourceCost**到 OData 的選用副檔名

**d:Map**到 OData 的選用副檔名

**d:Headers**到 OData 的選用副檔名

**d:Headers**到 OData 的選用副檔名

**d:Value**到 OData 的選用副檔名

**d:HttpStatusCode**到 OData 的選用副檔名

**d:ErrorMessage**到 OData 的選用副檔名

## <a name="parameter-node"></a>參數節點

此節點代表一個參數，公開為 URI 範本的一部分 / 要求已指定 FunctionImport 節點中的內文。

在[以下](http://msdn.microsoft.com/library/ee473431.aspx)（若要選取不同的版本，如有必要，若要檢視的文件使用的**其他版本**的下拉式清單） 找到很有幫助的詳細資料 」 參數項目 」 節點的相關文件頁面。 *範例︰*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| 參數屬性 | 需要 | 值 |
|----|----|----|
| 名稱 | [是] | 參數名稱。 大小寫視為相異 ！  符合 BaseUri 大小寫。 **範例︰**`<Property Name="IsDormant" Type="Byte" />` |
| 類型 | [是] | 參數類型。 值必須是模型的**EDMSimpleType**或複雜型別的範圍內。 如需詳細資訊，請參閱 [6 支援參數/內容類型]。  （區分大小寫 ！ 第一個字元是大寫，其餘的大小寫）。 請參閱，[概念性模型類型 (CSDL)] [MSDNParameterLink]。 **範例︰**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| 模式 | 無 | **在**、，或進出參數是輸入、 輸出或輸入輸出參數而定。 （僅限 」 在 「 都提供 Azure Marketplace。）**範例︰**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | 無 | 參數的長度上限。 **範例︰**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| 精確度 | 無 | 參數的精確度。 **範例︰**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| 縮放比例 | 無 | 參數的小數位數。 **範例︰**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

以下是 CSDL 規格已經新增的屬性︰

| 參數屬性 | 描述 |
|----|----|
| **d:Regex***（選用）* | 用來驗證輸入參數值 regex 陳述式。 如果輸入的值不符合陳述式被拒絕的值。 這個選項可讓指定也的一組可能的值，例如 ^ [0-9] +？ $ 只允許數字。 **範例︰** ' < 參數名稱 = 「 name 」 模式 」 在 「 類型 = = 」 字串 「 d: Nullable ="false"d:Regex = 「 ^ [是-A-Z] * $」 d:Description = 」 的名稱，不能包含任何空格或非字母的非英文字元 」 d:SampleValues ="喬治|名字|邁可|James 」 / > 」 |
| **d:Enum***（選用）* | 管道分隔有效參數值的清單。 類型的值必須符合已定義之的參數的類型。 範例: 「 英文|公制|原始`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< 參數名稱 = 「 期間 」 類型 ="字串 「 模式 」 在 「 Null = ="true"d:Enum = 「 1 年|5years|10years 」 / > 」 |
| **d: Nullable***（選用）* | 可讓定義參數是否為 null。 預設值是︰ true。 不過，公開為部分的路徑 URI 範本中的參數不得為空白。 當屬性設定為下列參數 – false 會覆寫使用者輸入。 **範例︰**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue***（選用）* | 若要以用戶端使用者介面中將筆記顯示範例值。  可以使用管道分隔清單，也就新增數個值 」|b|c` **Example:** `< 參數名稱 = 「 BikeOwner 」 類型 ="字串 「 模式 」 在 「 d:SampleValues = ="喬治|名字|邁可|James 」 / > 」 |

## <a name="entitytype-node"></a>EntityType 節點

這個節點表示其中一個使用者從 Marketplace 所傳回的類型。 同時也包含輸出傳回內容提供者的服務的使用者會傳回的值從的對應。

這個節點的詳細資料是位於[以下](http://msdn.microsoft.com/library/bb399206.aspx)（如有必要，若要檢視的文件，請選取在不同版本的使用的**其他版本**的下拉式清單）。

| 屬性名稱 | 需要 | 值 |
|----|----|----|
| 名稱 | [是] | 實體類型的名稱。 **範例︰**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | 無 | 其他實體類型所定義的實體類型的基底類型的名稱。 **範例︰**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

以下是 CSDL 規格已經新增的屬性︰

**d:Map** -執行服務輸出的 XPath 運算式。 以下假設是 ATOM 摘要位置等服務輸出，包含一組重複的項目有一組重複的項目節點。 每個重複節點包含一筆記錄。 若要保留的個別記錄的值的內容提供者的服務結果中的個別重複節點指向然後指定 XPath。 從服務輸出的範例︰

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

XPath 運算式會被 /foo/列因為每一列的為重複節點輸出中的和其包含的實際的內容，傳回給使用者。

服務商場**鍵**這個屬性會忽略。 其他 web 服務，請在 [一般不會揭露主索引鍵。


## <a name="property-node"></a>屬性節點

這個節點包含筆記錄的屬性。

這個節點的詳細資料是位於[http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) （如有必要，若要檢視的文件，請選取在不同版本的使用的**其他版本**的下拉式清單）。*範例︰*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | 所需 | 值 |
|----|----|----|
| 名稱 | [是] | 屬性名稱。 |
| 類型 | [是] | 屬性值的類型。 屬性值類型必須**EDMSimpleType**或複雜型別 （以完整名稱） 的模型範圍內。 如需詳細資訊，請參閱概念性模型類型 (CSDL)。 |
| Nullable | 無 | **為 true**（預設值） 或**False**根據屬性是否可以有 null 值。 附註︰ CSDL [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm)命名空間指示的版本，在複雜的類型] 屬性必須 Null ="False"。 |
| [預設值 | 無 | 屬性的預設值。 |
|MaxLength | 無 | 屬性值的最大的長度。 |
| FixedLength | 無 | **True**或**False**根據是否屬性值會儲存為 fiexed 長度字串。 |
| 精確度 | 無 | 指向要保留數值中的數字的最大數目。 |
| 縮放比例 | 無 | 若要保留數值中的小數位數的最大數目。 |
| Unicode | 無 | **True**或**False**根據屬性值是否會儲存成 Unicode 字串。 |
| 定序 | 無 | 字串，指定要使用的資料來源中的排序順序。 |
| ConcurrencyMode | 無 | **無**（預設值） 或**固定**。 如果值設定為 [**固定**，屬性值將用於樂觀檢查項目。 |

以下是 CSDL 規格已經新增的其他屬性︰

**d:Map**服務所執行的 XPath 運算式輸出，並擷取輸出的屬性。 指定的 XPath 是 EntityType 節點的 XPath 中已選取重複節點的相對路徑。 您也可指定絕對 XPath 允許包括靜態資源的每一個輸出節點，例如，例如著作權陳述式，只原始服務中一次找到輸出但應該出現在每一個 OData 輸出中的資料列。 從服務的範例︰

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

XPath 運算式會從內容提供者的服務取得 baz0 節點./bar/baz0。

**d:CharMaxLength** -字串類型，您可以指定最大的長度。 請參閱 DataService CSDL 範例

**d:IsPrimaryKey** -表示的資料行是在表格檢視中的主索引鍵。 請參閱 DataService CSDL 範例。

如果要公開顯示的表格結構描述會決定**d:isExposed** -(通常是 true)。 請參閱 DataService CSDL 範例

**d:IsView***（選擇性）* -如果這根據檢視，而不是資料表則為 true。  請參閱 DataService CSDL 範例

**d:Tableschema** ，請參閱 DataService CSDL 範例

**d:ColumnName** -是在表格檢視中的資料行的名稱。  請參閱 DataService CSDL 範例

**d:IsReturned** -是布林值會決定服務是否公開此值，用戶端。  請參閱 DataService CSDL 範例

**d:IsQueryable** -會決定是否可以資料庫查詢中使用的資料行的布林值。   請參閱 DataService CSDL 範例

**d:OrdinalPosition** -是外觀的欄的數字的位置，x、 表格或檢視，其中 x 是外觀的介於 1 到表格中的欄數。  請參閱 DataService CSDL 範例

**d:DatabaseDataType** -是在資料庫中的資料行的資料型別 SQL 資料類型的即。 請參閱 DataService CSDL 範例

## <a name="supported-parametersproperty-types"></a>支援的參數內容類型
以下是支援的參數和內容類型。 （區分大小寫）

| 基本的類型 | 描述 |
|----|----|
| Null | 表示沒有值 |
| 布林值 | 代表數學二進位值的邏輯的概念|
| 位元組 | 正負號的 8 位元整數值|
|日期時間| 表示日期和時間範圍是從第午夜 12:00:00 西元年 1 月 1，1753 透過 11:59:59 下午，12 月 9999 A.D.|
|小數位數 | 代表數字值使用固定的精確度和小數位數。 此類型可以用來說明數值範圍從負 10 ^255 + 1 到正 10 ^255-1|
| 點兩下 | 代表浮點數具有 15 位數精確度的可以代表值大約範圍它 2.23 e-308 到它 1.79 e + 308。 **使用小，因為 Exel 匯出問題**|
| 單一 | 代表浮點數，可以代表大約範圍它 1.18 e-38 到它 3.40 e 值的 7 個位數精確地 +38|
|Guid |代表 16 位元 （128 位元） 唯一識別碼值 |
|Int16|代表帶正負號的 16 位元整數值 |
|Int32|代表簽署的 32 位元整數值 |
|Int64|代表帶正負號的 64 位元整數值 |
|字串 | 代表固定或變數-長度字元的資料 |


## <a name="see-also"></a>另請參閱
- 如果您有興趣瞭解的整體 OData 對應程序和用途，請閱讀本文[資料服務 OData 對應](marketplace-publishing-data-service-creation-odata-mapping.md)檢閱定義、 結構和指示。
- 如果您想要檢閱範例，請閱讀本文以查看範例程式碼，並瞭解程式碼語法和內容的[資料服務 OData 對應的範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)。
- 若要返回發佈 Azure Marketplace 資料服務的相關路徑，請閱讀本文[資料服務發佈指南](marketplace-publishing-data-service-creation.md)。
