<properties
    pageTitle="Azure 行動互動 Android SDK 整合"
    description="最新的更新和 Android SDK Azure 行動互動的程序"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>版本資訊

## <a name="423-08102016"></a>4.2.3 (08/10/2016)

- 沒有更多的 WIFI 鎖定。
- 呼叫 getDeviceId 之前初始化 (bug 4.2.0 中推出) 時，請修正死結。

## <a name="422-05172016"></a>4.2.2 (05/17/2016)

- 穩定性改良項目。

## <a name="421-05102016"></a>4.2.1 (05/10/2016)

- 安全性︰ 停用網頁檢視本機檔案存取。
- 安全性︰ 移除`EngagementPreferenceActivity`擴充過時和不安全的類別`PreferenceActivity`類別。
- 安全性︰ 使用立即記錄活動達到`exported="false"`，這個旗標也可用於舊版 SDK。

## <a name="420-03112016"></a>4.2.0 (03/11/2016)

- SDK 現在已在 MIT 授權。
- 允許 SDK 初始化次指定自訂裝置識別碼。

## <a name="415-02012016"></a>4.1.5 (02/01/2016)

- 穩定性改良項目。

## <a name="414-01262016"></a>4.1.4 (2016/01/26)

- 穩定性改良項目。

## <a name="413-1292015"></a>4.1.3 (12/9/2015)

- 穩定性改良項目。

## <a name="412-11252015"></a>4.1.2 (11/25/2015)

- 穩定性改良項目。

## <a name="411-11042015"></a>4.1.1 (11/04/2015)

- 穩定性改良項目。

## <a name="410-08252015"></a>4.1.0 (08/25/2015)

- Android m 處理新的權限模型。
- 現在，可以設定位置功能，而不是使用執行階段`AndroidManifest.xml`。
- 修正權限錯誤︰ 如果您是使用`ACCESS_FINE_LOCATION`，然後`ACCESS_COARSE_LOCATION`，不需要再。
- 穩定性改良項目。

## <a name="400-07062015"></a>4.0.0 (07/06/2015)

-   內部通訊協定進行分析及推入可靠的變更。
-   原生推入 (GCM/ADM) 現在也適用於應用程式通知中，您必須設定任何類型的推入行銷活動的原生推入認證。
-   修正一窺全貌通知︰ 結果顯示只 10s 之後被推入。
-   在 [網頁檢視修正錯誤︰ 按一下 [連結上也執行預設動作 URL。
-   修正本機存放裝置管理相關少見當機。
-   修正動態設定字串的管理。
-   更新使用者授權合約。

## <a name="300-02172015"></a>3.0.0 (02/17/2015)

-   初始版本的 Azure 行動互動
-   連線字串設定會取代 appId 設定。
-   移除 [傳送及接收任意 XMPP 郵件任意 XMPP 實體 API。
-   移除 [傳送及接收郵件裝置之間的 API。
-   安全性的改良功能。
-   Google Play] 和 [SmartAd 追蹤移除。
