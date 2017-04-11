<properties
    pageTitle="行動互動概念 |Microsoft Azure"
    description="Azure 行動互動的概念"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Azure 行動互動的概念

行動互動定義所有支援的平台幾個常見的概念。 本文簡單說明這些概念。

本文是很好的開始，如果您不熟悉行動互動。 也請確定它會修改以更多詳細資料和範例，以及可能的限制本文所述的概念，請參閱使用，在平台特定文件。

## <a name="devices-and-users"></a>裝置和使用者
行動互動識別使用者所產生的每個裝置的唯一識別碼。 此識別碼稱為裝置識別項 (或`deviceid`)。 它會產生的方式的同一個裝置的所有應用程式執行共用相同的裝置識別碼。

隱含地將其表示行動互動根據裝置屬於一位使用者，因此，若使用者和裝置都相同的概念。

## <a name="sessions-and-activities"></a>工作階段和活動
工作階段是由使用者的應用程式使用，從使用者開始使用，直到使用者停駐點。

活動是指定子組件執行一位使用者的應用程式的使用 （通常是一個畫面中，但很適合應用程式的任何項目）。

使用者一次只能執行一個活動。

活動由 （64 個字元限制） 的名稱，並可以選擇性地使用內嵌一些額外的資料 （1024 個位元組的上限）。

從使用者所執行的活動的順序，會自動計算工作階段。 在使用者啟動他的第一個活動，並停駐點，他完成他的最後一個活動時，就會啟動工作階段。 這表示的工作階段不需要明確啟動或停止。 不過，活動明確啟動或停止。 如果沒有活動報告，報告工作階段。

## <a name="events"></a>事件
事件用來報告立即動作 （例如，按下按鈕或使用者讀取文件）。

可以與事件相關目前的工作階段，若要執行的工作，或在獨立的事件。

事件由 （64 個字元限制） 的名稱，並可以選擇性地使用內嵌一些額外的資料 （1024 個位元組的上限）。

## <a name="error"></a>錯誤
會使用錯誤報告正確偵測到的應用程式 （例如不正確的使用者動作] 或 API 通話失敗） 的問題。

在目前的工作階段，若要執行的工作，與相關錯誤，或是可以是獨立錯誤。

錯誤由 （64 個字元限制） 的名稱，並可以選擇性地使用內嵌一些額外的資料 （1024 個位元組的上限）。

## <a name="job"></a>工作
工作會用來報告有工期的動作 （例如 API 通話期間，顯示時間的廣告、 背景任務的工期或工期的使用者動作）。

工作不相關的工作階段，因為在背景中沒有任何使用者互動來執行任務。

工作由 （64 個字元限制） 的名稱，並可以選擇性地使用內嵌一些額外的資料 （1024 個位元組的上限）。

## <a name="crash"></a>當機。
當機是由行動互動 SDK 損毀回報應用程式失敗未偵測到的應用程式的問題，使其自動發行。

## <a name="application-information"></a>應用程式的資訊
應用程式的資訊 （或應用程式資訊） 用於標記的使用者，也就是建立關聯的使用者 （這是 web cookie 類似，但應用程式的資訊儲存在伺服器端 Azure 行動互動平台上） 應用程式的一些資料。

使用行動互動 SDK API 或使用行動互動的平台裝置 API 註冊應用程式的資訊。

應用程式的資訊會與裝置相關聯的金鑰/值組。 索引鍵是 （僅限於 64 ASCII 字元 [是-A-Z]，[0-9] 的數字和底線 [_]） 的應用程式資訊的名稱。 任何字串、 整數、 日期 (yyyy-MM-dd) 或布林值 （true 或 false），可以是值 （限制為 1024年個字元）。

任何數目的應用程式的資訊可關聯到裝置、 內行動互動費用規定所定義的限制。 為其中一個指定金鑰行動互動只會持續追蹤的最新設定的值 （沒有歷程記錄）。 設定或變更應用程式資訊的值會強制重新評估表示該應用程式資訊的準則設定此應用程式的資訊 （如果有的話） 上可以用來觸發即時推入的對象的行動互動。

## <a name="extra-data"></a>額外的資料
額外的資料 （或 extras]） 是一些可附加至事件、 錯誤、 活動和工作的任何資料。

JSON 物件的 「 extras] 結構化同樣︰ 組成的金鑰/值組樹狀結構。 索引鍵是限於 64 ASCII 字元 [是-A-Z]、 [0-9] 的數字和底線 [_]），而且額外的總大小限制為 1024年個字元 （一次在 JSON 由編碼行動互動 SDK）。

索引鍵/值組的整個樹狀結構會儲存成 JSON 物件。 不過，機碼/值的第一層會直接存取某些進階的函數，例如區段分解 （例如，您可輕鬆地定義稱為 「 SciFi 風扇 」 的所有使用者無法傳送至少 10 次事件名為 「 content_viewed 」 都由區段額外鍵 」 content_type] 設定為 「 scifi 」 上個月中的值）。 因此強烈建議傳送只 extras 所做的金鑰/值組使用純量值 （例如，字串、 日期、 整數或布林值） 的簡單的清單。

## <a name="next-steps"></a>後續步驟

- [Azure 行動互動的 Windows 通用 SDK 概觀](mobile-engagement-windows-store-sdk-overview.md)
- [Azure 行動互動的 Windows Phone Silverlight SDK 概觀](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK Azure 行動互動](mobile-engagement-ios-sdk-overview.md)
- [Android SDK Azure 行動互動](mobile-engagement-android-sdk-overview.md)
