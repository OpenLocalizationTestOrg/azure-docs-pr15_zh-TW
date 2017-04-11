<properties
    pageTitle="Microsoft Azure IoT 套件概觀 |Microsoft Azure"
    description="如何概觀 Azure IoT 套件提供的項目預先設定的解決方案的網際網路來收集、 分析和儲存資料、 提供視覺效果，並與其他系統整合。"
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>什麼是 Azure IoT 套件？

Azure 項目 (IoT) 服務的網際網路提供各式各樣的功能。 這些企業成績服務可讓您︰

- 從裝置收集資料
- 分析資料流中的影片
- 儲存及查詢的大型資料集
- 以視覺化方式呈現資料即時和歷程記錄
- 返回 office 系統整合

提供這些功能、 Azure IoT 套件套件搭配多個 Azure 服務自訂副檔名為*預先設定的解決方案*。 這些預先設定的解決方案是基底實作常見 IoT 解決方案模式，以減少您需要發表您 IoT 解決方案的時間。 使用[IoT 軟體開發套件][lnk-sdks]，您可以自訂及擴充這些解決方案，以符合您自己的需求。 您也可以使用這些解決方案範例或範本時開發新 IoT 解決方案。

以下影片介紹 Azure IoT 套件︰

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT 套件的 azure IoT 服務

預先設定的解決方案通常會使用下列服務︰

- Azure IoT 套件的核心是[Azure IoT 中心][lnk-iot-hub]服務。 這項服務會提供的裝置至雲端和雲端至裝置的簡訊功能，並做為閘道器至雲端和其他按鍵 IoT 套件服務。 服務可讓您接收郵件從您的裝置在 [縮放]，並將命令傳送至您的裝置。

- [Azure 資料流分析][lnk-asa]提供中移動資料分析。 IoT 套件會使用這項服務傳入遙測 [處理程序、 執行彙總，以及偵測事件。 預先設定的解決方案也會使用資料流分析資訊訊息含有資料，例如從裝置的中繼資料] 或 [命令回應的處理程序。 解決方案會使用資料流分析處理您的裝置中的郵件，以及將這些郵件傳遞到其他服務。

- [Azure 儲存體][lnk-azure-storage]和[Azure DocumentDB] [lnk-document-db]提供資料儲存功能。 預先設定的解決方案使用 blob 儲存體儲存遙測並使其可供分析。 解決方案使用 DocumentDB 存放裝置中繼資料，並啟用裝置管理功能的解決方案。

- [Azure Web 應用程式][lnk-web-apps]和[Microsoft Power BI] [lnk-power-bi]提供資料視覺效果功能。 Power BI 的彈性，可讓您快速建立您自己使用 IoT 套件資料的互動式儀表板。

如需一般 IoT 解決方案架構的概觀，請參閱[Microsoft Azure 和網際網路的項目 (IoT)][iot-suite-what-is-azure-iot]。

## <a name="preconfigured-solutions"></a>預先設定的解決方案

IoT 套件包含預先設定的解決方案，讓您快速開始使用，並瀏覽常見 IoT 案例中的，例如*遠端監視*及*預測進行的維修作業*] 的 Azure IoT 套件可讓。 您可以將這些解決方案部署至 Azure 訂閱，然後再執行，端對端 IoT 情況。

## <a name="next-steps"></a>後續步驟

您已經有的概觀 IoT 套件能做什麼，以及哪些其主要元件，您可以進一步瞭解預先設定的解決方案的 IoT 套件，請參閱[什麼是 Azure IoT 預先設定的方案嗎？][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
