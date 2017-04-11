<properties
 pageTitle="開發人員指南-查詢語言 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-查詢語言用來擷取盡相同、 方法及工作的相關資訊，從您 IoT 中心的描述"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>參照-查詢語言盡相同和工作

## <a name="overview"></a>概觀

IoT 中心提供功能強大的類似 SQL 語言來擷取[裝置盡相同]的相關資訊[lnk-twins]和[工作][lnk-jobs]。 本文說明︰

* 主要的搜尋功能簡介的 IoT 中樞的查詢語言，然後
* 語言的詳細的描述。

## <a name="getting-started-with-twin-queries"></a>快速入門雙生查詢

[裝置盡相同][lnk-twins]為標籤和內容最多可以包含任意 JSON 物件。 IoT 中心可為單一 JSON 文件包含所有雙生資訊讓查詢裝置盡相同。
例如，假設您 IoT 中心盡相同，有下列結構︰

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT 中心公開裝置盡相同，為文件集合稱為**裝置**。
因此，下列查詢會擷取整組裝置盡相同︰

        SELECT * FROM devices

> [AZURE.NOTE] [IoT 中心 Sdk] [lnk-hub-sdks]支援分頁的較大的結果。

IoT 中心可讓擷取盡相同任意條件的篩選。 例如，

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

設定為**我們**的**location.region**標記擷取盡相同。
布林值運算子和算術比較支援，例如︰

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

擷取位於美國設定不常超過每個分鐘傳送遙測所有盡相同。 為了方便，您也可使用常數陣列**中**和**‧ 尼恩之**（不在） 運算子。 例如，

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

擷取所有盡相同的報告 wifi 或有線連線。 請參閱[WHERE 子句][lnk-query-where]篩選功能的完整參照一節。

同時支援組成群組和彙總。 例如，

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

傳回每一個遙測設定狀態裝置的計數。

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

上述範例說明位置三個裝置報告成功設定兩個仍會套用設定]，然後一個發生錯誤的情況。

### <a name="c-example"></a>C# 範例

查詢功能由[C# 服務 SDK]公開[lnk-hub-sdks]中**RegistryManager**類別。
以下是簡單查詢範例︰

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

請注意 [**查詢**物件如何產生頁面大小 （最多 1000)，然後多個頁面可以擷取多次呼叫**GetNextAsTwinAsync**方法。
請務必注意查詢物件公開多**下一步\***、 根據還原序列化選項所需的查詢，也就是 twin 或物件或使用預測時所要使用一般 Json 工作。

### <a name="node-example"></a>節點範例

查詢功能由[節點服務 SDK]公開[lnk-hub-sdks]中**登錄**物件。
以下是簡單查詢範例︰

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

請注意 [**查詢**物件如何產生頁面大小 （最多 1000)，然後多個頁面可以擷取多次呼叫**nextAsTwin**方法。
請務必注意查詢物件公開多**下一步\***、 根據還原序列化選項所需的查詢，也就是 twin 或物件或使用預測時所要使用一般 Json 工作。

### <a name="limitations"></a>限制

目前使用的彙總時，才支援計帳、 非彙總查詢即可以只使用`SELECT *`。 此外，配合分組只支援彙總。

## <a name="getting-started-with-jobs-queries"></a>快速入門工作查詢

[工作][lnk-jobs]提供執行作業的裝置上。 每個裝置雙生包含的資訊，這是組件稱為**工作**集合中的工作。
邏輯，

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

目前，此集合是 queriable 為**devices.jobs** IoT 中樞的查詢語言。

例如，若要取得影響單一裝置的所有工作 （過去及排程），您可以使用下列查詢︰

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

請注意此查詢會如何提供特定裝置的狀態 （與可能的直接方法回應） 傳回每項工作。
您也可與任意，則為 True 的條件**devices.jobs**集合中的物件的所有內容篩選。
例如，下列查詢︰

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

擷取完成的雙生更新的所有工作裝置**myDeviceId**年 9 月 2016 建立的。

您也可擷取單一工作的每個裝置結果。

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>限制
目前不支援**devices.jobs**的查詢︰

* 預測，也就是只`SELECT *`可以執行的作業。
* 參照裝置雙生，除了工作屬性; 如上所示的條件
* Peforming 彙總，例如計算平均、 群組依據]。

## <a name="basics-of-an-iot-hub-query"></a>IoT 中心查詢的基本概念

每個 IoT 中心查詢包含選取的 FROM 子句和選擇性的位置，以及群組依據的子句。 每個查詢執行 JSON 文件，例如裝置盡相同的集合。 FROM 子句會指出要反覆查看 （**裝置**或**devices.jobs**） 上的文件集合。 然後，在 WHERE 子句中的篩選會套用。 若是彙總，這個步驟的結果會分組為指定 GROUP BY 子句中，而且每個群組，請一列會產生如同在 SELECT 子句中指定。

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>FROM 子句

**< From_specification > FROM**子句可以假設只有兩個值︰**從裝置**，查詢裝置盡相同，或**從 devices.jobs**，查詢工作每個裝置詳細資料。

## <a name="where-clause"></a>WHERE 子句

**位置 < filter_condition >**子句為選用步驟。 它會指定寄集合中的 JSON 文件必須符合才會包含結果的條件。 任何 JSON 文件都必須評估為 「 true 」 會包含在結果中指定的條件。

允許的條件一節所述[的運算式與條件][lnk-query-expressions]。

## <a name="select-clause"></a>SELECT 子句

SELECT 子句 （**選取 < select_list >**） 是必要欄位，並指定哪些值會從查詢中擷取。 它會指定 JSON 值用來產生新的 JSON 物件的每個項目從集合篩選 （與您也可以選擇群組） 子集的計畫階段產生新的 JSON 物件、 建構 SELECT 子句中指定的值。

這是 SELECT 子句的文法︰

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

其中**attribute_name**指的是 JSON 文件從集合中的任何屬性。 SELECT 子句的部分範例，請參閱[快速入門雙生查詢][lnk-query-getstarted]一節。

不同於目前選取範圍子句**選取\***只能在彙總盡相同的查詢。

## <a name="group-by-clause"></a>GROUP BY 子句

**GROUP BY < group_specification >**子句是選擇性的步驟，可以執行之後指定在 WHERE 子句，和指定在 [選取預測之前的篩選器。 該群組的屬性值為基礎的文件。 這些群組用來產生彙總 SELECT 子句中所指定的值。

使用群組依據查詢的範例是︰

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

群組依據的正式語法是︰

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

其中**attribute_name**指的是 JSON 文件從集合中的任何屬性。 

目前，GROUP BY 子句，查詢盡相同時才支援。

## <a name="expressions-and-conditions"></a>運算式與條件

在高層級，*運算式*︰

* 結果為 （也就是布林值、 數字、 字串、 陣列或物件），JSON 類型執行個體和
* 管理資料來自裝置 JSON 文件和常數使用內建的運算子和函數所定義。

*條件式*為布林值，也就是任何常數不同，則為 True **，則為 true**會被視為**false** （內含**null**、**未定義**、 任何物件或矩陣的執行個體，任何字串和明確，則為 True **，則為 false**） 會評估的運算式。

運算式的語法是︰

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

位置︰

| 符號 | 定義 |
| -------------- | -----------------|
| attribute_name | 從集合中 JSON 文件的任何內容。 |
| unary_operator | 依照 [運算子] 區段任何一元運算子。 |
| binary_operator | 依照 [運算子] 區段任何二進位運算子。 |
| decimal_literal | 以小數點標記浮點數。 |
| hexadecimal_literal | 數字字串 「 0 x 」，後面接著十六進位數字的字串表示。 |
| 字串文字 | 字串串連是零或多個 Unicode 字元的一系列或逸出序列所代表的 Unicode 字串。 以單引號括住字串串連 (單引號: ') 或按兩下引號 (引號: 」)。 允許逸出字元︰ `\'`， `\"`， `\\`， `\uXXXX` 4 的十六進位數字所定義的 Unicode 字元。 |

### <a name="operators"></a>運算子

支援下列運算子︰

| 家庭 | 運算子 |
| -------------- | -----------------|
| 算術 | +,-,*,/,% |
| 邏輯 | 或不 |
| 比較 |  =、 ！ =，<>，、 < =、 > =、 <> |

## <a name="next-steps"></a>後續步驟

瞭解如何在您使用[IoT 中心 Sdk]的應用程式中執行查詢[lnk-hub-sdks]。

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md