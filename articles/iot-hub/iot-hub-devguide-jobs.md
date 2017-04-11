<properties
 pageTitle="開發人員指南-工作 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-排程工作執行多個裝置上連線到您的中心"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="schedule-jobs-on-multiple-devices-preview"></a>在多個裝置 （預覽版本） 的排程工作

## <a name="overview"></a>概觀

Azure IoT 中心依所述先前的文件，可讓建置組塊的數字 ([裝置雙生內容和標記][lnk-twin-devguide]和[雲端-裝置方法][lnk-dev-methods])。  一般而言，IoT 後端應用程式啟用裝置系統管理員和運算子更新並與其互動 IoT 裝置，大量和排程的時間。  工作封裝執行裝置雙生更新和 C2D 方法針對一組裝置的排程時間。  例如，運算子想要使用的後端應用程式會啟動並追蹤工作重新啟動裝置，並不會影響建置的作業一次建立 43 和底板 3 一組。

### <a name="when-to-use"></a>使用時機

請考慮使用工作的時機︰ 方案後端的排程與追蹤進度需求任何一組的裝置上的下列活動︰

- 更新裝置雙生所需內容
- 更新裝置雙生標籤
- 叫用 C2D 方法

## <a name="job-lifecycle"></a>工作生命週期

啟動由方案後端及維護 IoT 中樞的工作。  您可以啟動服務具 URI 透過工作 (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`)，然後查詢服務具 URI 執行工作進度 (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`)。  一旦工作啟動時，查詢工作會啟用重新整理的狀態執行工作的後端應用程式。

> [AZURE.NOTE] 當您啟動工作時，屬性名稱和值只能包含美國 ASCII 重點英數字元，除了任何在下列設定︰ ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``。

## <a name="reference-topics"></a>參考主題︰

下列主題提供給您使用的工作的相關資訊。

## <a name="jobs-to-execute-direct-methods"></a>若要執行直接方法的工作

以下是 HTTP 1.1 邀請詳細資料執行[直接方法][lnk-dev-methods]上一組裝置使用工作︰

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>若要更新裝置雙生內容的工作

以下是更新裝置雙生內容使用工作的 HTTP 1.1 邀請詳細資料︰

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>查詢工作進度

以下是[查詢工作]的 HTTP 1.1 邀請詳細資料[lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
ContinuationToken 提供的回應。  

## <a name="jobs-properties"></a>工作屬性

以下是屬性與相對應的描述，您可以使用查詢工作的工作結果清單。

| 屬性 | 描述 |
| -------------- | -----------------|
| **jobId** | 應用程式提供工作的識別碼。 |
| **開始時間** | 應用程式提供工作的開始時間 (ISO 8601)。 |
| **結束時間** | IoT 中心提供工作的完成的日期 (ISO 8601)。 只有在工作達到的 「 完成 」 的狀態後，才有效。 | 
| **類型** | 類型的工作︰ |
| | **scheduledUpdateTwin**︰ 用來更新一組雙生所需屬性或標籤的工作。 |
| | **scheduledDeviceMethod**︰ 用來叫用的裝置上的方法雙生一組工作。 |
| **狀態** | 工作的目前狀態。 狀態的可能值︰ |
| | **擱置**︰ 排程，並等待收取的工作服務。 |
| | **排程**︰ 排程為在未來的時間。 |
| | **執行**︰ 目前使用中工作。 |
| | **取消**︰ 工作已取消。 |
| | **失敗**︰ 作業失敗。 |
| | **完成**︰ 完成工作。 |
| **deviceJobStatistics** | 關於工作的執行的統計資料。 |

在預覽中，deviceJobStatistics 物件才可使用完成的工作。

| 屬性 | 描述 |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | 數字的工作中的裝置。 |
| **deviceJobStatistics.failedCount** | 數字的工作失敗，其中的裝置。 |
| **deviceJobStatistics.succeededCount** | 裝置作業成功的位置數目。 |
| **deviceJobStatistics.runningCount** | 目前正在執行作業的裝置的數目。 |
| **deviceJobStatistics.pendingCount** | 若要執行的工作擱置的裝置的數目。 |


### <a name="additional-reference-material"></a>其他參考資料

在 [開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。
- [節流和配額][lnk-quotas]說明適用於 IoT 中心服務及使用服務時的預期節流行為配額。
- [IoT 中心裝置和服務 Sdk] [lnk-sdks]列出各種語言 Sdk 您開發用的裝置和服務應用程式的互動 IoT 中心時使用。
- [查詢語言盡相同、 方法及工作][lnk-query]說明您可以用來擷取資訊 IoT 中心從您的裝置盡相同、 方法及工作查詢語言。
- [IoT 中心 MQTT 支援][ lnk-devguide-mqtt] MQTT 通訊協定提供有關 IoT 中心支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

如果您想要嘗試的一些本文所述的概念，您可能會感興趣的下列 IoT 中心教學課程︰

- [排程與廣播工作][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
