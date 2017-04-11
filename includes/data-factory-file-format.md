## <a name="specifying-formats"></a>指定的格式

Azure 資料 Factory 支援下列格式類型︰ 

- [文字格式](#specifying-textformat)
- [JSON 格式](#specifying-jsonformat)
- [Avro 格式](#specifying-avroformat)
- [ORC 格式](#specifying-orcformat)
- [Parquet 格式](#specifying-parquetformat)

### <a name="specifying-textformat"></a>指定 TextFormat

如果**TextFormat**設定格式，您可以在 [**格式**] 區段中指定下列**選用**的屬性。

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | 用來分隔檔案中的資料行的字元。 | 允許只有一個字元。 預設值是逗點 （「，」）。 <br/><br/>若要使用 Unicode 字元，請參閱取得對應的程式碼的[Unicode 字元](https://en.wikipedia.org/wiki/List_of_Unicode_characters)。 例如，您可以考慮使用少見的無法列印 char 可能不在您的資料中存在︰ 指定 「 \u0001 」 代表開始的標題 (SOH)。 | 無 |
| rowDelimiter | 用來分隔各列檔案中的字元。 | 允許只有一個字元。 預設值為 「 任何 」 以下的值的讀取: [」 \r\n 」、 「 \r 」、 「 \n 」] 和寫入的 「 \r\n 」。 | 無 |
| escapeChar | 特殊字元使用逸出資料行分隔符號中輸入檔案的內容。 <br/><br/>您無法指定 escapeChar 和 quoteChar 表格。 | 允許只有一個字元。 無預設值。 <br/><br/>範例︰ 如果您有逗號 （「，」） 的資料行分隔符號，但是您想要的文字中有逗號字元 (範例:"Hello，world 」)，您可以定義 '$' 做為逸出字元，並使用字串 「 認識 $，全球 」 來源中。 | 無 | 
| quoteChar | 用來報價的字串值的字元。 欄和列內的報價字元分隔符號會被視為字串值的一部分。 這個屬性會用於輸入與輸出資料集。<br/><br/>您無法指定 escapeChar 和 quoteChar 表格。 | 允許只有一個字元。 無預設值。 <br/><br/>比方說，如果您有逗號 （「，」） 的資料行分隔符號，但是您想要的文字中有逗號字元 (範例︰ < Hello，world >)，您可以定義 」 （雙引號） 為報價字元，並使用字串"Hello，world 」 來源中。 | 無 |
| nullValue | 用來代表 null 值的一個或多個字元。 | 一或多個字元。 預設值都是 「 \N 」 和 「 NULL 「 讀取 」 和 「 \N 」 寫入。 | 否 |
| encodingName | 指定編碼的名稱。 | 有效的編碼名稱。 請參閱[Encoding.EncodingName 屬性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。 範例︰ windows 1250 或 shift_jis。 預設值是 utf-8。 | 否 | 
| firstRowAsHeader | 指定是否要考慮為頁首的第一列。 輸入資料集，如資料工廠會為頁首讀取第一列。 輸出資料集資料工廠會將第一列作為標題。 <br/><br/>範例案例，請參閱[使用**firstRowAsHeader**和**skipLineCount**的案例](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 | 為 true<br/>False （預設值） | 否 |
| skipLineCount | 指出略過讀取資料輸入的檔案時的資料列數目。 如果未指定 skipLineCount 和 firstRowAsHeader，線條會被略過第一次，然後標頭資訊讀取輸入檔案。 <br/><br/>範例案例，請參閱[使用 firstRowAsHeader 和 skipLineCount 的案例](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 | 整數 | 無 | 
| treatEmptyAsNull | 指定是否要讀取輸入檔案中的資料時 null 或空字串視為 null 值。 | True （預設值）<br/>False | 無 |  

#### <a name="textformat-example"></a>TextFormat 範例
下列範例會顯示部分的格式屬性的 TextFormat。

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

若要使用 escapeChar，而不是 quoteChar，取代與下列 escapeChar quoteChar 行︰

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>使用 firstRowAsHeader 和 skipLineCount 的案例

- 您所複製投影片的非檔案來源為文字檔，並且想要新增標題行包含結構描述中繼資料 (例如︰ SQL 結構描述)。 指定**firstRowAsHeader**做為 true，則在此案例輸出資料集。 
- 您所複製投影片文字檔案包含的非檔案接收到的標題列，並且想要捨棄的線條。 指定**firstRowAsHeader**做為 true，則在輸入的資料集。
- 您所複製投影片文字檔案，並想要略過幾行的開頭，不包含資料或標題資訊。 指定**skipLineCount**表示略過的行數。 如果檔案的其餘部分包含標題列，您也可以指定**firstRowAsHeader**。 如果指定**skipLineCount** ] 與 [ **firstRowAsHeader** ，線條會被略過第一次，然後標頭資訊讀取輸入檔案

### <a name="specifying-avroformat"></a>指定 AvroFormat
如果 AvroFormat 設定格式，您不需要指定 typeProperties 節中的 [格式] 區段中的任何內容。 範例︰

    "format":
    {
        "type": "AvroFormat",
    }

若要使用 Avro 格式登錄區資料表中，您可以參考[Apache 登錄區的教學課程](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)。

請注意下列重點︰  

- 不支援的[複雜的資料類型](http://avro.apache.org/docs/current/spec.html#schema_complex)（列舉陣列、 地圖、 [聯集記錄及固定）

### <a name="specifying-jsonformat"></a>指定 JsonFormat

如果**JsonFormat**設定格式，您可以在 [**格式**] 區段中指定下列**選用**的屬性。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| filePattern | 代表每個 JSON 檔案中儲存的資料的模式。 允許的值︰ **setOfObjects**和**arrayOfObjects**。 **預設**值是︰ **setOfObjects**。 請參閱下節的詳細資料這些模式。| 無 |
| encodingName | 指定編碼的名稱。 有效的編碼名稱清單，請參閱︰ [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx)屬性。 例如︰ windows 1250 或 shift_jis。 **預設**值是︰ **utf-8**。 | 無 | 
| nestingSeparator | 用於分隔巢狀層級的字元。 預設值為 「。 」（點）。 | 無 | 


#### <a name="setofobjects-file-pattern"></a>setOfObjects 檔案模式

每個檔案都包含單一物件或線條-分隔/串連的多個物件。 輸出資料集中選擇這個選項，複製活動會產生單一 JSON 檔案與每行 （線條分隔） 每個物件。

**單一物件** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**線條分隔 JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**串連的 JSON**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>arrayOfObjects 檔案模式。 

每個檔案包含陣列的物件。 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>JsonFormat 範例

如果您有 JSON 檔案以下列內容︰  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

而您想要將它複製到 SQL Azure 資料表以下列格式︰ 

識別碼  | Name.First | Name.Middle | Name.Last | 標記
--- | ---------- | ----------- | --------- | ----
1 | 名字 | null | 為何 | [「 資料工廠 「，」 Azure 」]

輸入資料集與 JsonFormat 類型定義如下: （僅限相關的組件的部分定義）

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

如果未定義的結構，複製活動簡維預設的結構，並複製每個項目。 

#### <a name="supported-json-structure"></a>支援的 JSON 結構
請注意下列重點︰ 

- 每個物件的名稱/值組集合] 已對應至一列的資料以表格格式。 物件可以巢狀結構，您可以定義簡中資料集的結構的巢狀分隔字元 （.） 維預設的方式。 請參閱前一節範例[JsonFormat 範例](#jsonformat-example)。  
- 如果資料工廠資料集內不定義結構，複製活動偵測到從第一個物件的結構描述，然後簡維完整的物件。 
- 如果 JSON 輸入陣列，複製活動會將整個陣列值轉換為字串。 您可以選擇使用[資料行對應或篩選](#column-mapping-with-translator-rules)跳過這個步驟。
- 如果同一層級，有重複的名稱，請複製活動會挑選的最後一個項目。
- 屬性名稱會區分大小寫。 具有相同的名稱，但用法的兩個屬性會被視為兩個不同的屬性。 

### <a name="specifying-orcformat"></a>指定 OrcFormat
如果 OrcFormat 設定格式，您不需要指定 typeProperties 節中的 [格式] 區段中的任何內容。 範例︰

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] 如果您不複製 ORC 檔案**為-是**內部部署與雲端之間資料存放區，您需要在閘道器電腦上安裝 JRE 8 （Java 執行階段環境）。 64 位元的閘道器需要 JRE 64 位元與 32 位元閘道器需要 32 位元 JRE。 您可以找到從[以下](http://go.microsoft.com/fwlink/?LinkId=808605)兩個版本。 選擇適當的項目。

請注意下列重點︰

-   複雜的資料類型不支援 （結構、 地圖、 清單、 聯集）
-   ORC 檔案有三個[壓縮相關的選項](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)︰ 無 ZLIB，SNAPPY。 資料工廠支援讀取中任何一種壓縮格式的 ORC 檔案中的資料。 使用壓縮轉碼器讀取資料是在 [中繼資料。 不過，當撰寫 ORC 檔案，資料工廠選擇 ZLIB，這是 ORC 的預設值。 目前，是沒有選項可覆寫此行為。 

### <a name="specifying-parquetformat"></a>指定 ParquetFormat
如果 ParquetFormat 設定格式，您不需要指定 typeProperties 節中的 [格式] 區段中的任何內容。 範例︰

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] 如果您不複製 Parquet 檔案**為-是**內部部署與雲端之間資料存放區，您需要在閘道器電腦上安裝 JRE 8 （Java 執行階段環境）。 64 位元的閘道器需要 JRE 64 位元與 32 位元閘道器需要 32 位元 JRE。 您可以找到從[以下](http://go.microsoft.com/fwlink/?LinkId=808605)兩個版本。 選擇適當的項目。

請注意下列重點︰

-   複雜的資料類型不支援 （對應表，清單）
-   Parquet 檔案有下列壓縮相關選項︰ 無、 SNAPPY GZIP，與 LZO。 資料工廠支援讀取中任何一種壓縮格式的 ORC 檔案中的資料。 使用壓縮轉碼器中繼資料中就能閱讀資料。 不過，當寫入 Parquet 檔案，資料工廠選擇 SNAPPY，這是 Parquet 格式的預設值。 目前，是沒有選項可覆寫此行為。 
