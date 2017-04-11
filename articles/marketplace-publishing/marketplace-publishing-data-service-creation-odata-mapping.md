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

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>將現有的 web 服務對應至 OData 透過 CSDL

>[AZURE.IMPORTANT] **現在我們已不再登入任何新的資料服務的發行者。新的 dataservices 非取得已核准的清單。** 如果您有您想要發佈上 AppSource SaaS 商務應用程式，您就可以找到更多資訊[以下](https://appsource.microsoft.com/partners)。 如果您有 IaaS 應用程式，或您想要發佈 Azure 服務商場的開發人員服務可以找到更多資訊[以下](https://azure.microsoft.com/marketplace/programs/certified/)。

本文提供有關如何使用 CSDL，將現有的服務對應到 OData 相容的服務的概觀。 說明如何建立對應文件 (CSDL) 的轉換輸入從用戶端透過服務通話要求，並返回摘要透過 OData 相容的用戶端的輸出 （資料）。 Microsoft Azure Marketplace 公開服務使用者使用 OData 通訊協定。 以不同的表單，例如其餘、 SOAP、 等公開內容提供者 （資料擁有者） 所公開的服務。

## <a name="what-is-a-csdl-and-its-structure"></a>什麼是 CSDL 和其結構？
CSDL （概念性結構描述定義語言） 是規格定義如何描述 web 服務或資料庫中常見的 XML 用語至 Azure Marketplace 的服務。

簡單概觀**要求流程︰**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

**資料流程**是相反的方向︰

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**圖 1**圖表如何用戶端會取得資料內容提供者 （您的服務） 透過 Azure Marketplace。  CSDL 對應/轉換元件來處理要求和資料和之間傳遞內容提供者的服務要求的用戶端。

*圖 1︰ 詳細的流程，從要求的用戶端透過 Azure Marketplace 的內容提供者*

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Atom 背景，如 Atom 發行及 OData 通訊協定結合 Azure Marketplace 副檔名建立，請檢視︰ [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

連結 from above 摘錄︰     *「 開放式資料通訊協定 （以下稱為 OData） 的目的是提供其餘為基礎的通訊協定的 crud 以樣式作業 （建立、 讀取、 更新和刪除） 公開為資料服務的資源。[資料服務] 是端點公開從一或多個 」 集合 「 每個或多個 」 項目 」，其中包含的資料輸入名稱 / 值組。OData 發佈 （可提升的結構化資訊標準的組織） OASIS 標準底下的 microsoft 讓伺服器、 用戶端或沒有權利金或限制的工具，想要的任何人都可以建立。 」*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>三個關鍵的部分，必須由 CSDL 定義為︰

- **結束點**的服務提供者網站位址 (URI) 的服務
- 傳遞做為服務提供者輸入的**資料參數**的參數定義傳送到下的資料類型的內容提供者的服務。
- 傳遞內容提供者的服務，包括容器、 集合/表格、 變數/欄及資料類型的資料結構描述要求服務所傳回的資料**結構描述**。

下圖顯示從用戶端輸入 OData 陳述式 （通話內容提供者的 web 服務） 來取得傳回的結果/資料的位置流程的概觀。

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>步驟︰

1. 用戶端，會傳送要求服務呼叫透過完整的 XML to Azure Marketplace 中定義的輸入參數
2. CSDL 會用於驗證服務通話。
    - 格式化服務來電會傳送到內容的提供者服務來 Azure Marketplace
3. Webservice 執行，並將 preforms Http 動作的動作 （亦即取得） 會傳回資料至 Azure Marketplace 要求的資料 （如果有的話） 的用戶端的 XML 格式公開的位置使用 CSDL 中定義的對應。
4. 用戶端會傳送至資料 （如果有的話） 中 XML 或 JSON 格式

## <a name="definitions"></a>定義

### <a name="odata-atom-pub"></a>OData ATOM 發行

每個項目代表結果的一列的位置 ATOM 發行的延伸設定。 包含值的資料列 – 做為索引鍵值組增強內容的組件的項目。 以下找到詳細的資訊︰ [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL-概念結構定義語言

可讓您定義函數 （預） 和透過資料庫公開的實體。 詳細資訊，在這裡找到︰ [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] 按一下**其他版本**的下拉式清單，然後選取版本，如果您沒有看到文件。

### <a name="edm---entry-data-model"></a>EDM-項目資料模型
- 概觀: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- 預覽: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink]: http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- 資料類型: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink]: http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

返回詳細從左到右流程從用戶端輸入 OData 陳述式 （通話內容提供者的 web 服務） 的位置，以取得結果/資料下列所示︰

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>CSDL 基本概念

CSDL （概念性結構描述定義語言） 是規格定義如何描述 web 服務或資料庫中常見的 XML 用語至 Azure Marketplace 的服務。 CSDL 說明重要 pieces 的**可讓您的資料傳送資料來源至 Azure Marketplace 可能。** 此處所述的主要部分︰

- 描述所有可公開使用函數 （FunctionImport 節點） 的介面資訊
- 所有郵件 requests(input) 和訊息 responses(outputs) （EntityContainer/EntitySet/EntityType 節點） 的資料類型資訊
- 繫結為傳輸通訊協定的相關資訊所用 （標頭節點）
- 地址資訊，以尋找指定的服務 （BaseURI 屬性）

簡而言之，CSDL 表示的平台和語言無關合約服務要求者之間的服務提供者。 使用 CSDL，用戶端可以找出 web 服務/資料庫服務，並啟動任何其可公開使用的功能。

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>建立資料庫或集合 CSDL
**CSDL 規格**

CSDL 是 XML 文法描述 web 服務。 本身的規格分成 4 的主要項目︰ EntitySet、 FunctionImport;命名空間，並在 EntityType。

若要讓您更容易瞭解可讓此抽象與 CSDL 產生關聯的資料表。

請記住;

  CSDL 代表**服務要求者**與**服務提供者**之間的平台和語言無關合約。 使用 CSDL，**用戶端**可以找出**web 服務/資料庫服務**，並啟動其可公開使用任何**函數。**

資料服務 CSDL 四個部分可以想像的資料庫、 表格、 欄及預存程序。

建立這些資料服務的如下所示︰

- EntityContainer ~ = 資料庫
- EntitySet ~ = 表格
- EntityType ~ = 資料行
- FunctionImport ~ = 預存程序

**允許的 HTTP 動作**
- 取得 – 傳回的值從資料庫 （傳回集合）
- 文章 – 用來將資料和選擇性的傳回值傳遞從 db （建立新的項目中的集合，傳回的識別碼 URI）
- 刪除] – [從資料庫 （刪除集合） 刪除資料
- 將-DB 將更新的資料 （取代集合，或建立）

## <a name="metadatamapping-document"></a>中繼資料/對應文件

中繼資料/對應文件用來對應內容提供者的現有的 web 服務，讓它可以 Azure Marketplace 系統公開當作 OData web 服務。 它會根據 CSDL 而實作 CSDL 以納入其間的其餘需求的幾個延伸透過 Azure Marketplace 的 web 服務。 [Http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04)命名空間中，找到副檔名。

CSDL 的範例如下: (複製與貼上下列範例 CSDL 到 XML 編輯器中，然後變更以符合您的服務。  然後貼到 CSDL 對應 DataService] 索引標籤下[Azure 服務商場發佈入口網站](https://publish.windowsazure.com)中建立您的服務時）。

**字詞︰**建立[發佈入口網站](https://publish.windowsazure.com)使用者介面 (PPUI) 字詞 CSDL 字詞。
- 提供 PPUI 中的 「 標題 」 與 MyWebOffer
- 在 PPUI MyCompany 與[Microsoft 開發人員中心](http://dev.windows.com/registration?accountprogram=azure)使用者介面中的**Publisher 顯示名稱**
- 您的 API 與 Web 或 Data Service （PPUI 的方案）

**階層︰** 
公司 （內容提供者） 擁有 Offer(s) 具有方案，也就是 service(s) API 設定的線條。

### <a name="webservice-csdl-example"></a>WebService CSDL 範例

連線到公開 web 應用程式 （例如 C# 應用程式） 端點的服務

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] [範例對應到 OData CSDLs 到現有的 web 服務](marketplace-publishing-data-service-creation-odata-mapping-examples.md)的文件中檢視更多 CSDL Web 服務的範例

###<a name="dataservice-csdl-example"></a>DataService CSDL 範例

連線至端點下列範例所示的基礎資料的兩個 Api 基礎 （可以使用檢視，而不是資料表） 的 API CSDL 資料庫資料表或檢視公開的服務。

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>另請參閱
- 如果您有興趣學習特定節點和其的參數，了解閱讀本文[資料服務 OData 對應節點](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)定義和說明，範例，並使用大小寫的內容。
- 如果您想要檢閱範例，請閱讀本文以查看範例程式碼，並瞭解程式碼語法和內容的[資料服務 OData 對應的範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)。
- 若要返回發佈 Azure Marketplace 資料服務的相關路徑，請閱讀本文[資料服務發佈指南](marketplace-publishing-data-service-creation.md)。
