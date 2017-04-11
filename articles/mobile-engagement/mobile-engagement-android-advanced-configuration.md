<properties
    pageTitle="Azure 行動互動 Android SDK 的進階的組態"
    description="說明的進階的組態選項，包括使用 Azure 行動互動 Android SDK Android 資訊清單"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Azure 行動互動 Android SDK 的進階的組態

> [AZURE.SELECTOR]
- [通用 Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

此程序說明如何設定 Azure 行動互動 Android 應用程式的不同設定選項。

## <a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>權限需求
某些選項需要特定權限，均會列於此處的參照，並在列中的特定功能。 立即之前或之後，這些權限新增至您的專案 AndroidManifest.xml`<application>`標籤。

權限的程式碼，必須如下所示，填入適當的權限，從下表的位置。

    <uses-permission android:name="android.permission.[specific permission]"/>


| 權限 | 使用時 |
| ---------- | --------- |
| 網際網路 | 所需。 基本報表 |
| ACCESS_NETWORK_STATE | 所需。 基本報表 |
| RECEIVE_BOOT_COMPLETED | 所需。 顯示通知中心之後的裝置 |
| WAKE_LOCK | 建議。 可讓使用 WiFi 時，或螢幕關閉時收集資料 |
| 震動 | 選用。 啟用時收到通知的震動 |
| DOWNLOAD_WITHOUT_NOTIFICATION | 選用。 可讓 Android 一窺全貌通知 |
| WRITE_EXTERNAL_STORAGE | 選用。 可讓 Android 一窺全貌通知 |
| ACCESS_COARSE_LOCATION | 選用。 啟用即時位置報告 |
| ACCESS_FINE_LOCATION | 選用。 啟用報告 GPS 為基礎的位置 |

開始使用 Android M，[某些權限管理在執行階段](mobile-engagement-android-location-reporting.md#Android-M-Permissions)。

如果您已經在使用``ACCESS_FINE_LOCATION``，然後您不需要也使用``ACCESS_COARSE_LOCATION``。

## <a name="android-manifest-configuration-options"></a>Android 的顯示設定選項

### <a name="crash-report"></a>當機報表

若要停用當機報表，將此程式碼之間新增`<application>`和`</application>`標籤︰

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>尖峰閥值

根據預設，互動服務報表中進行即時記錄。 如果您的應用程式報表記錄經常不同，最好緩衝記錄，以及報告，一次在一般時間基底 （稱為 「 尖峰模式 」）。 若要這麼做，請將此程式碼之間新增`<application>`和`</application>`標籤︰

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

尖峰模式稍微增加電池，但會影響互動監視器︰ 所有的工作階段及工作期間會四捨五入至突發臨界值 （因此，工作階段及工作短比突發閥值可能無法看到）。 您的突發閥值應該不能超過 30000 （30 秒）。

### <a name="session-timeout"></a>工作階段逾時

 您可以藉由設定閒置電話或跳到另一個應用程式，請按下 [**家用版**或**上一步**] 鍵，最後活動。 根據預設，結束工作階段是 10 秒結束之後，它的最後一個活動。 如此可避免工作階段分割每次使用者結束，並傳回應用程式快速，其中可以發生當使用者挑選圖像，請檢查有通知等。若要修改此參數。 若要這麼做，請將此程式碼之間新增`<application>`和`</application>`標籤︰

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>停用日誌報告

### <a name="using-a-method-call"></a>使用方法呼叫

如果您想要停止傳送記錄檔的互動，您可以撥打︰

    EngagementAgent.getInstance(context).setEnabled(false);

這個呼叫是持續︰ 使用共用的偏好設定檔案。

如果互動是作用中，當您撥打此函數時，可能需要一分鐘，若要停止服務。 不過，就不會啟動服務在下次啟動應用程式。

您可以啟用記錄報告再次呼叫的同一個函數`true`。

### <a name="integration-in-your-own-preferenceactivity"></a>在您自己的整合`PreferenceActivity`

而非呼叫此函數，您也可以整合這項設定直接在現有的`PreferenceActivity`。

您可以設定以使用您的喜好設定檔案 （含所需的模式） 互動`AndroidManifest.xml`檔案與`application meta-data`:

-   `engagement:agent:settings:name`索引鍵用來定義共用的喜好設定檔的名稱。
-   `engagement:agent:settings:mode`索引鍵用來定義共用的喜好設定檔的模式。 使用相同的模式中為您`PreferenceActivity`。 必須為的數字傳遞模式︰ 如果您在程式碼中使用常數旗標組合，檢查總計的值。

永遠互動使用`engagement:key`布林值的索引鍵，在 [管理此設定的喜好設定檔中。

下列範例`AndroidManifest.xml`顯示的預設值︰

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

然後您可以新增`CheckBoxPreference`下列所示的喜好設定版面配置︰

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
