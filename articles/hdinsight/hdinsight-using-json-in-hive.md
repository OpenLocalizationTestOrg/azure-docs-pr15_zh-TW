<properties
   pageTitle="分析及程序 JSON 文件中 HDInsight 的登錄區與 |Microsoft Azure"
   description="瞭解如何使用 JSON 文件和分析其使用登錄區中 HDInsight。"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>[處理程序及分析 JSON 使用登錄區 HDInsight 中的文件

瞭解如何處理程序及分析 JSON 檔案使用中 HDInsight 的登錄區。 將教學課程中使用下列 JSON 文件

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

可以在找到檔案wasbs://processjson@hditutorialdata.blob.core.windows.net/。 如需有關如何使用 HDInsight Azure Blob 儲存體的詳細資訊，請參閱[使用 Hadoop HDInsight 中的使用 HDFS 相容 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。 如果您想要您可以將檔案複製至叢集的預設容器。

在本教學課程中，您會使用登錄區主控台。  開啟登錄區主控台的指示，請參閱[使用登錄區與 Hadoop 上 HDInsight 與遠端桌面](hdinsight-hadoop-use-hive-remote-desktop.md)。

##<a name="flatten-json-documents"></a>簡維 JSON 文件

在下一節中所列的方法需要在單一列 JSON 文件。 因此，您必須簡維字串 JSON 文件。 如果已合併的 JSON 文件，您可以略過此步驟，並直接進入 [下一步] 區段的分析 JSON 資料。

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

原始的 JSON 檔案是位於**wasbs://processjson@hditutorialdata.blob.core.windows.net/**。 *StudentsRaw*登錄區資料表指向原始的未平面 JSON 文件。

*StudentsOneLine*登錄區資料表會在**/json/學生/路徑 HDInsight 預設檔案系統中儲存的資料。

插入陳述式填入 StudentOneLine 表格的平面 JSON 資料。

SELECT 陳述式應該只會傳回 1 的資料列。

以下是 SELECT 陳述式的輸出︰

![簡維 JSON 文件。][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>分析 JSON 登錄區中的文件

登錄區提供三個不同的機制 JSON 文件上執行的查詢︰

- 使用 [開始\_JSON\_物件 UDF （使用者定義函數）
- 使用 JSON_TUPLE UDF
- 使用自訂 SerDe
- 撰寫您擁有 UDF 使用 Python 或其他語言。 請參閱[本文][hdinsight-python]上執行登錄區 Python 程式碼。

### <a name="use-the-getjsonobject-udf"></a>使用 [開始\_JSON_OBJECT UDF
登錄區提供內建的 UDF 稱為[取得 json 物件](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)可以執行 JSON 查詢執行階段。 這個方法使用兩個引數 – 的表格名稱及方法名稱具有平面的 JSON 文件] 和 [需要剖析 JSON 欄位。 讓我們來看看範例，查看此 UDF 的運作方式。

取得名字和姓氏，每個學生

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

在 [主控台] 視窗中執行這個查詢時，以下是成果。

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

有一些限制，取得 json_object UDF。

- 在查詢中的每個欄位需要重新剖析查詢，因為它會影響效能。
- 取得\_JSON_OBJECT() 傳回陣列的字串表示。 若要轉換為登錄區陣列，您必須使用規則運算式來取代方括號 '[' 和']' 再也呼叫分割取得陣列。


這是為什麼登錄區 wiki 建議使用 json_tuple。  

### <a name="use-the-jsontuple-udf"></a>使用 JSON_TUPLE UDF

登錄區所提供的另一個 UDF 稱為[json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)比[get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)更好執行。 這個方法一組索引鍵和 JSON 字串，然後傳回的使用一個函數的值，表示 tuple。 下列查詢會傳回學生識別碼] 和 [成績 JSON 文件︰

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

登錄區主控台中檢視此指令碼的輸出︰

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLE 可讓 json 登錄區中使用[橫向檢視](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView)語法\_tuple 原始資料表的每一列套用 UDT 函數來建立虛擬資料表。  由於重複使用橫向檢視變得太雜亂無章的 JSONs 複雜。 此外，JSON_TUPLE 無法處理巢狀的 JSONs。


###<a name="use-custom-serde"></a>使用自訂 SerDe

SerDe 剖析巢狀的 JSON 文件的最佳選擇，可讓您定義 JSON 結構描述，及分析文件中使用結構描述。 在本教學課程中，您會使用其中一個具有開發的[rcongiu](https://github.com/rcongiu)更受歡迎 SerDe。

**若要使用自訂 SerDe:**

1. 安裝[Java SE 開發套件 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR)。 如果您要使用的 HDInsight Windows 部署，選擇 [Windows X64 版本的 JDK

    >[AZURE.WARNING] JDK 1.8 無法使用此 SerDe 而定。

    安裝完成後，新增新的使用者環境變數︰

    1. 開啟 [Windows] 畫面上的 [**檢視進階系統設定**]。
    2. 按一下 [**環境變數**]。  
    3. 新增新的**JAVA_HOME**環境變數指向**C:\Program Files\Java\jdk1.7.0_55**或您 JDK 已安裝的任何地方。

    ![針對 JDK 設定正確的設定值][image-hdi-hivejson-jdk]

2. 安裝[Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    [回收筒] 資料夾新增至您的路徑，移至 [控制項] 面板--> 編輯您的帳戶 Environment 變數系統的變數。 以下螢幕擷取畫面顯示如何進行此動作。

    ![設定 Maven][image-hdi-hivejson-maven]

3. 複製[區-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github 網站的專案。 您可以按一下 「 下載 Zip 」] 按鈕，在下面的螢幕擷取畫面所示。

    ![複製專案][image-hdi-hivejson-serde]

4︰ 移至您已下載此封裝並輸入 「 mvn 套件 」 的資料夾。 此應該建立必要 jar 檔案，然後複製透過叢集。

5︰ 移至您已下載套件根資料夾下的目標資料夾。 Json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar 檔案上傳您叢集不對-節點。 通常登錄區二進位資料夾中的 [將︰ C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin 或類似。

6︰ 在登錄區提示中，輸入 [新增 jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar]。 由於在這個案例中，糖 C:\apps\dist\hive-0.13.x\bin 資料夾中，可以直接新增名稱 jar 如下所示︰

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

現在，您已經準備好使用 SerDe 對 JSON 文件中執行查詢。

下列陳述式以定義的結構描述建立表格

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

名字和姓氏的學生清單

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

以下是從登錄區主控台的結果。

![SerDe 查詢 1][image-hdi-hivejson-serde_query1]

若要計算 JSON 文件中的成績的總和

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

上述查詢使用[橫向檢視分裂](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView)UDF 以展開成績的陣列，讓他們可以加總。

以下是從登錄區主控台輸出。

![SerDe 查詢 2][image-hdi-hivejson-serde_query2]

若要尋找選取指定的學生具有計分超過 80 點的主題  
      jt.從 StudentClassCollection.ClassId json_table jt 橫向檢視分裂 (jt.為分數 StudentClassCollection.Score) 集合位置分數 > 80;

上述查詢傳回的登錄區陣列與取得不同\_json\_傳回字串的物件。

![SerDe 查詢 3][image-hdi-hivejson-serde_query3]

如果您想要 skil 不正確的 JSON，然後在此 SerDe 的[wiki 頁面](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master)中所述可以獲得的輸入下列程式碼︰  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>摘要
總之，在您選擇的登錄區 JSON 運算子的類型而定的原因。 如果您有簡單 JSON 文件，且您只有一個欄位來查詢上 – 您可以選擇使用登錄區 UDF 取得\_json\_物件。 如果您有多個機碼，即可查看，您可以使用 json_tuple。 如果您有巢狀的文件時，您應該使用 JSON SerDe。

其他相關的文件，請參閱

- [使用登錄區及 HiveQL Hadoop HDInsight 中要分析的範例 Apache log4j 檔案](hdinsight-use-hive.md)
- [使用登錄區中 HDInsight 分析航班延遲情況資料](hdinsight-analyze-flight-delay-data.md)
- [Twitter 使用分析資料的 HDInsight 的登錄區](hdinsight-analyze-twitter-data.md)
- [執行使用 DocumentDB 和 HDInsight Hadoop 工作](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
