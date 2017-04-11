<properties
    pageTitle="Azure 行動互動 Android SDK 整合"
    description="最新的更新和 Android SDK Azure 行動互動的程序"
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
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>如何將整合 GCM 與行動裝置的互動

> [AZURE.IMPORTANT] 您必須遵循整合述的程序整合互動如何在 Android 的文件之前本指南。
>
> 這份文件是已整合達到模組和計劃以推入 Google Play 裝置時，才有幫助的。 若要整合應用程式中的範圍行銷活動，請先閱讀整合連絡在 Android 上的互動方式。

##<a name="introduction"></a>簡介

整合 GCM 可讓您的應用程式會被。

GCM 裝載 sdk 的變更一律推入包含`azme`資料物件中的索引鍵。 因此如果您使用的其他用途的 GCM 應用程式中，您可以篩選依據這個索引鍵的推入。

> [AZURE.IMPORTANT] 只有執行 Android 2.2 的裝置或上方有 [Google Play 安裝，而且無法 Google 啟用背景連線可推入使用 GCM;不過，您可以整合安全地在不受支援的裝置 （它只會使用對應方式） 這段程式碼。

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>使用 API 金鑰建立 Google 雲端訊息的專案

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>SDK 整合

### <a name="managing-device-registrations"></a>管理裝置註冊

每個裝置必須將登錄命令傳送至 Google 伺服器，否則他們無法存取。

在裝置也可取消訂閱 GCM 通知 （裝置的自動註冊解除安裝應用程式時）。

如果您不是使用[Google 播放 SDK]或您沒有已傳送註冊用途自己，您可以進行互動自動註冊使用您的裝置。

若要啟用此，新增下列您`AndroidManifest.xml`內部檔案`<application/>`標記︰

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>溝通註冊 id 互動推入服務，並會收到通知

通訊的互動推入服務的裝置註冊識別碼會收到通知，才能將下列文字新增至您`AndroidManifest.xml`內部檔案`<application/>`標記 （即使您自行管理裝置註冊）︰

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

確認您擁有下列權限您`AndroidManifest.xml`(之後`</application>`標籤)。

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>行動參與權限授予您 GCM API 金鑰

依照[本指南](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key)行動參與權限授予您 GCM API 金鑰。

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
