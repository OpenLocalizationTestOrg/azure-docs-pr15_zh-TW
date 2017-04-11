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
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-integrate-adm-with-engagement"></a>ADM 整合互動的方式

> [AZURE.IMPORTANT] 您必須遵循整合述的程序整合互動如何在 Android 的文件之前本指南。
>
> 這份文件是已整合達到模組和計劃以推播 Amazon 裝置時，才有幫助的。 若要整合應用程式中的範圍行銷活動，請先閱讀整合連絡在 Android 上的互動方式。

##<a name="introduction"></a>簡介

整合 ADM 可讓您的應用程式時選取目標 Amazon Android 裝置推入。

ADM 裝載 sdk 的變更一律推入包含`azme`資料物件中的索引鍵。 因此如果您使用的其他用途的 ADM 應用程式中，您可以篩選依據這個索引鍵的推入。

> [AZURE.IMPORTANT] 只有 Amazon Kindle 裝置執行 Android 4.0.3 上方所支援的裝置訊息 Amazon; 或不過，您可以整合安全地在其他裝置上的將此程式碼。

##<a name="sign-up-to-adm"></a>登入，ADM

如果尚未完成，您必須先啟用 ADM Amazon 帳戶上。

在詳細說明程序︰ [<https://developer.amazon.com/sdk/adm/credentials.html>]。

完成程序，您取得︰

-   OAuth 認證 （用戶端識別碼和用戶端私人） 會將您的裝置的互動。
-   必須整合到您的應用程式 API 金鑰。

##<a name="sdk-integration"></a>SDK 整合

### <a name="managing-device-registrations"></a>管理裝置註冊

每個裝置必須傳送註冊命令 ADM 伺服器，否則他們無法存取。

如果您已使用[ADM 用戶端文件庫]中，並已[整合式的 ADM]您可以直接前往 android-sdk-adm-接收。

如果您不具有尚未整合 ADM，互動具有更簡單的方法，在您的應用程式︰

編輯您`AndroidManifest.xml`檔案︰

-   新增 Amazon 命名空間檔案應該像這樣︰

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   內`<application/>`標記、 新增此區段︰

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   後新增 amazon 標籤，您可能需要建置錯誤，如果您的專案建立目標是 Android 2.1 下方。 您必須使用**Android 2.1 +**建立目標 (別擔心，您仍然可以讓`minSdkVersion`設定為 4)。
-   以下列[此程序]，為資產整合 ADM API 金鑰。

然後遵循下一節中的指示進行。

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>溝通註冊 id 互動推入服務，並會收到通知

通訊的互動推入服務的裝置註冊識別碼會收到通知，才能將下列文字新增至您`AndroidManifest.xml`內部檔案`<application/>`標記 （即使您使用的互動而 ADM）︰

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

確認您擁有下列權限您`AndroidManifest.xml`(之前`</application>`標籤)。

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##<a name="grant-engagement-oauth-credentials"></a>授與互動 OAuth 認證

送出 OAuth 認證 （用戶端識別碼和用戶端私人） 互動入口網站中。

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM 用戶端文件庫]:https://developer.amazon.com/sdk/adm/setup.html
[整合式的 ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[此程序]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
