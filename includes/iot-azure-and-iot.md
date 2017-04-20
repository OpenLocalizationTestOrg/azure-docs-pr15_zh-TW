
# <a name="azure-and-internet-of-things"></a>Azure 及網際網路的事項

歡迎使用 Microsoft Azure 和項目皆 (IoT) 網際網路。 本文介紹說明您可能會部署使用 Azure services IoT 解決方案的一般特性 IoT 解決方案架構。 IoT 解決方案需要安全、 裝置，還號數百萬和解決方案的後端之間的雙向通訊。 例如解決方案後端可能會使用自動化、 預測性分析從抽出前瞻從裝置-雲端事件資料流。

Azure IoT Hub 實作使用 Azure services 此 IoT 解決方案架構時重要的建置區塊。 IoT 套件提供完整、 端對端的特定 IoT 案例此架構的實作。 例如： 

- *遠端監視*解決方案可讓您監視裝置如 vending 機器的狀態。 
- *預測性維護*解決方案可協助您預期的裝置例如遠端幫浦總機系統中的幫浦維護需求，以及以避免未預期的停機。

## <a name="iot-solution-architecture"></a>IoT 解決方案架構

下圖顯示典型的 IoT 解決方案架構。 圖表不包括任何特定的 Azure 服務的名稱，但說明一般 IoT 解決方案架構中的關鍵元素。 在這種架構、 IoT 裝置收集傳送至雲端閘道的資料。 雲端閘道資料可讓其他從資料傳送至其他企業營運系統應用程式或透過儀表板或其他簡報裝置人工運算子的後端服務的處理。

![IoT 解決方案架構][img-solution-architecture]

> [AZURE.NOTE] 如 IoT 架構深入資訊，請參閱[Microsoft Azure IoT 參考架構][lnk-refarch]。

### <a name="device-connectivity"></a>裝置連線

在此 IoT 解決方案架構，裝置會傳送遙測，例如接收器讀取從幫浦站，以儲存與處理雲端端點。 預測性維護案例、 後端可能會使用接收器資料流的決定特定的幫浦時需要維護。 裝置也可以接收和讀取郵件從雲端端點雲端-裝置命令以回應。 例如，預測性維護的案例中解決方案的後端可能會傳送命令給在開始之前維護的到期日開始以確定維護工程師可以開始她送達時重新路由流程幫浦站其他幫浦。

其中一個對向 IoT 專案的最大挑戰在於如何可靠且安全地裝置連線到方案後端。 IoT 裝置具有不同的特性與其他用戶端如瀏覽器與行動應用程式。 IoT 裝置：

- 通常是內嵌的系統連結起來不總機。
- 可以部署在遠端位置，其中是昂貴實體的存取。
- 僅能透過解決方案後端連線受到存取。 沒有其他方法與裝置進行互動。
- 可能會有限制能力及處理的資源。
- 可能會有間歇性、 很慢，或昂貴網路連線。
- 可能需要使用專屬、 自訂、 或業界特定應用程式通訊協定。
- 可以建立大型常用的硬體和軟體平台的集合。

除了上述之外的任何 IoT 方案也必須傳送規模、 安全性及可靠性。 所產生的連線需求集合是實作使用傳統等 web 容器的技術及通訊經紀人不僅耗時且硬碟。 Azure IoT 中樞與 IoT 裝置 Sdk 容易實作解決方案符合這些需求。

可以直接與雲端閘道終點通訊裝置或如果裝置不能使用任何雲端閘道支援通訊協定，可以連線到中繼閘道。 例如， [Azure IoT 通訊協定閘道][lnk-protocol-gateway]可以執行通訊協定轉譯如果裝置不能使用任何 IoT 集線器支援的通訊協定。

### <a name="data-processing-and-analytics"></a>資料處理與分析

在雲端解決方案 back end IoT 會是其中大部分的資料處理發生時，例如篩選和彙總遙測及路由傳送至其他服務。 IoT 解決方案 back end:

- 從您的裝置接收遙測向外延展，並決定如何處理，並儲存該資料。 
- 可能會讓您從雲端傳送命令到特定的裝置。
- 提供裝置註冊功能可讓您佈建裝置並控制哪些裝置可連線到您的基礎結構。
- 可讓您追蹤您的裝置的狀態及監視他們的活動。

預測性維護案例中，在解決方案後端儲存歷程記錄的遙測資料。 後端可以使用此資料可使用識別表示上特定幫浦期限維護模式。

IoT 解決方案可以包含自動意見反應循環。 例如，可以從特定裝置溫度超出正常作業層級的遙測找出分析中的模組後端。 解決方案然後可以傳送命令到裝置，指示要採取更正動作。

### <a name="presentation-and-business-connectivity"></a>簡報與 business connectivity

簡報與 business connectivity 層可讓使用者互動 IoT 解決方案和裝置。 它可讓使用者檢視及分析從其裝置收集資料。 這些檢視可採取的儀表板或 BI 報告，可以顯示這兩個歷程資料或附近即時資料的表單。 例如，運算子可以檢查特定幫浦總機的狀態並查看任何系統所引發的提醒。 此圖層也可讓 IoT 解決方案後端與現有的企業營運系統應用程式繫結至企業商務程序或工作流程的整合。 例如，預測維護解決方案可與線上叢書以造訪幫浦總機時解決方案識別需要維護幫浦工程師排程系統整合。

![IoT 解決方案儀表板][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
