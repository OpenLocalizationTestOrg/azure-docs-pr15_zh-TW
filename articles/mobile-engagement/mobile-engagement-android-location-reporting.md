<properties
    pageTitle="針對 Azure 行動互動 Android SDK 報告的位置"
    description="說明如何設定報表的 Azure 行動互動 Android SDK 的位置"
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>針對 Azure 行動互動 Android SDK 報告的位置

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

本主題說明如何在 Android 應用程式的報告的位置。

## <a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>位置報告

如果您想要報告的位置，您需要新增幾行程式設定 (之間`<application>`和`</application>`標籤)。

### <a name="lazy-area-location-reporting"></a>延遲區域位置報告

延遲區域位置報告可讓報表國家/地區、 區域和裝置相關聯的位置。 此類型的位置報告只會使用網路位置 （根據儲存格 ID 或 WIFI）。 一次最多每個工作階段報告 [裝置] 區域。 GPS 從未使用過，，因此此類型的位置報表在電池中有低的影響。

報告的區域用來計算有關使用者、 工作階段、 活動和錯誤地理統計資料。 他們也可做為準則範圍行銷活動中。

您啟用報告使用此程序上述設定的麻煩區域位置︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您也需要指定位置權限。 此程式碼使用``COARSE``權限︰

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

如果您的應用程式時有此需要，您可以使用``ACCESS_FINE_LOCATION``改為。

### <a name="real-time-location-reporting"></a>即時位置報告

即時報告可讓報表緯度和經度裝置相關聯的位置。 根據預設，此類型的位置報告只會使用依據儲存格 ID 或 WIFI 的網路位置。 報告只有作用中時，應用程式在前景執行時 （例如，在工作階段中）。

即時位置都*無法*用來計算統計資料。 其唯一的目的是要允許即時地理圍欄使用\<抵達-對象-geofencing\>達到行銷活動中的準則。

若要啟用即時報告的位置，請將程式碼，啟動器活動設定互動連線字串。 結果看起來如下所示︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS 基礎報告

根據預設，即時位置報告只會使用網路位置。 若要啟用 GPS 為基礎的位置，是最更精確地，使用 [設定] 物件︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您也需要新增下列權限，如果遺失︰

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>背景報告

根據預設，即時位置報告只有作用中時，應用程式在前景執行時 （例如，在工作階段中）。 若要啟用報告也在背景，請使用此設定物件︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] 應用程式在背景中執行時報告只網路位置，即使您啟用 GPS。

如果使用者重新啟動裝置，並停止背景位置報表。 若要使其自動重新啟動在啟動時，新增將此程式碼。

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

您也需要新增下列權限，如果遺失︰

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M 權限

開始使用 Android M，某些與權限在執行階段管理需要使用者核准。

如果您要在 Android API 層級 23，runtime 權限會關閉新的應用程式安裝的預設值。 否則它們是預設為開啟。

您可以啟用/停用裝置的 [設定] 功能表這些權限。 關閉 [系統] 功能表上的權限刪除的應用程式，系統行為，並在背景中接收推入功能已不會影響背景處理程序。

在 Mobile 互動位置報告的內容，需要在執行階段核准的權限是︰

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

要求使用標準系統] 對話方塊的使用者權限。 如果使用者核准，告訴``EngagementAgent``才能即時入的帳戶的變更。 否則，變更處理使用者啟動的應用程式在下一次。

以下是使用應用程式的活動要求權限及轉寄結果，如果是正數至程式碼範例``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
