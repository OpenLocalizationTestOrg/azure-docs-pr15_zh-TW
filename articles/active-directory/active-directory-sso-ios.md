<properties
    pageTitle="如何啟用跨應用程式 SSO iOS 使用 ADAL 上的 |Microsoft Azure"
    description="如何使用 ADAL SDK 的功能來啟用單一登入您的應用程式。 "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>如何啟用跨應用程式 SSO iOS 使用 ADAL 上


提供單一登入 (SSO)，讓使用者就只需要輸入其認證，一次，並讓那些認證自動使用應用程式現在所預期的客戶。 在螢幕小，通常時間加上其他因素 (2FA)，例如電話或 texted 的程式碼中，輸入使用者名稱和密碼無法結果中快速滿意度如果使用者有執行此動作一個以上的時間，為您的產品。

此外，如果您使用其他應用程式可能會使用 Microsoft 帳戶或公司帳戶從 Office365 例如識別平台，客戶的預期那些認證可供使用無論供應商的其所有應用程式。

Microsoft 身分識別的平台我們 Microsoft 的身分識別 Sdk，以及所有努力完成的工作會為您及可讓您是在自己的應用程式的套件 delight 以 sso 進行客戶或，與我們的代理人功能和驗證應用程式至整個裝置。

此逐步解說會告訴您如何設定我們 SDK 提供這項優惠給客戶的應用程式中。

此逐步解說適用於︰

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory 條件的存取


請注意下列文件，假設您有瞭解如何[在舊版的 Azure Active Directory 入口網站佈建應用程式](./develop/active-directory-how-to-integrate.md)，以及已與[Microsoft 身分識別 iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc)整合應用程式。

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>在 Microsoft 身分識別平台的 SSO 概念

### <a name="microsoft-identity-brokers"></a>Microsoft 身分識別仲介

Microsoft 提供的每個行動裝置平台允許認證橋跨不同廠商的應用程式的應用程式，以及可讓特殊增強功能需要單一安全的地方，從 [驗證認證的位置。 我們呼叫這些**經紀人**。 在 iOS 和 Android 版這些透過提供可下載的應用程式客戶獨立安裝或可管理其員工部分或所有裝置的公司推入裝置。 這些經紀人支援管理安全性只適用於部分應用程式或整個根據 IT 系統管理員所需的裝置。 在 Windows 中所提供的內建於作業系統，技術上而言，為網頁驗證代理人已知帳戶選擇器的這項功能。

若要瞭解我們如何使用這些經紀人，以及如何客戶可能會看到他們在其登入 Microsoft 身分識別平台上閱讀，如需詳細資訊。

### <a name="patterns-for-logging-in-on-mobile-devices"></a>在行動裝置上登入的模式

存取在裝置上的認證，請依照 Microsoft 身分識別平台的兩個基本模式︰

* 非代理人協助登入
* 仲介處理協助登入

#### <a name="non-broker-assisted-logins"></a>非代理人協助登入

非代理人協助登入會發生這種情形的應用程式，在該應用程式的裝置上使用本機存放區登入體驗。 在應用程式都可以共用此儲存空間，但認證緊密結合的應用程式或使用該認證的應用程式套件。 這是您可在此輸入使用者名稱和密碼，應用程式本身的體驗，很可能會遭遇許多行動應用程式。

這些登入有下列優點︰

-  使用者體驗存在完全應用程式內。
-  認證可以共用相同的憑證，提供單一登入體驗︰ 您的應用程式套件簽署的應用程式。
-  周圍的登入經驗控制提供給之前和之後登入應用程式。

這些登入有下列缺點︰

- 使用者無法透過只會擁有您的應用程式，以及設定這些 Microsoft 身分識別全面使用 Microsoft 身分識別的所有應用程式體驗單一登入。
- 您的應用程式與更進階的商務功能，例如條件 Access 無法使用，或使用 InTune 套件的產品。
- 您的應用程式無法適用於商務使用者支援基礎的憑證驗證]。

以下是如何使用共用的儲存空間，您的應用程式，以啟用 SSO 的 Microsoft 身分識別 Sdk 的表示︰

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>仲介處理協助登入

代理人協助登入會出現在代理人應用程式中，使用的儲存空間及安全性代理人共用運用 Microsoft 身分識別的平台裝置上所有應用程式的認證登入體驗。 這表示，您的應用程式會依賴代理人才能登入的使用者。 在 iOS 和 Android 版這些透過提供可下載的應用程式客戶單獨安裝或可管理其使用者的裝置的公司推入裝置。 這種應用程式的範例是 Azure 驗證應用程式在 iOS 上。 在 Windows 中所提供的內建於作業系統，技術上而言，為網頁驗證代理人已知帳戶選擇器的這項功能。
體驗因平台和有時可能會干擾到使用者如果不是受管理的正確。 如果您有安裝 Facebook 應用程式，並使用另一個應用程式中的 Facebook 登入功能，您很可能最這個模式熟悉。 Microsoft 身分識別的平台會使用相同的模式。

這會導致 「 轉場效果 」 的 iOS 版應用程式背景 Azure 驗證應用程式時的傳送位置的動畫傳入前景的使用者，選取您想要登入的帳戶。  

Android 與 Windows 帳戶選擇器會顯示在上方會較少的影響使用者的應用程式。

#### <a name="how-the-broker-gets-invoked"></a>如何取得叫用代理人

如果相容的代理人已安裝在裝置上，Azure 驗證應用程式，例如 Microsoft 身分識別 Sdk 會自動執行叫用您的代理人，當使用者表示他們想要使用的 Microsoft 身分識別平台任何帳戶登入的工作。 這可能是個人 Microsoft 帳戶、 公司或學校帳戶，或所提供的帳戶和 Azure 使用 B2C 和 B2B 產品中的主機。 使用太安全演算法及加密我們確保認證會要求您提供，以安全的方式傳送至您的應用程式。 完全技術的詳細資料，這些機制未發佈，但進行共同作業開發的是所有蘋果樹和 Google。

**如果 Microsoft 身分識別 SDK 的通話代理人，或使用非代理人協助開發人員就會有的選項。** 但是如果開發人員選擇不使用代理人協助流程他們會遺失運用 SSO 認證使用者可能已經新增裝置上，以及可防止他們使用 Microsoft 提供條件的存取權，Intune 管理功能，例如客戶的商務功能的應用程式，以驗證憑證的優點。

這些登入有下列優點︰

-  使用者體驗 SSO 無論供應商的其所有應用程式。
-  您的應用程式可以利用更進階的商務功能，例如條件的存取或 InTune 套件的產品。
-  適用於商務使用者，應用程式可支援基礎的憑證驗證]。
- 為應用程式與一位使用者的身分識別的更加安全登入體驗是由代理人應用程式加密演算法額外的安全性與驗證。

這些登入有下列缺點︰

- Ios 已登出您的應用程式體驗轉換使用者時為所選的認證。
- [管理應用程式中的客戶的登入體驗功能遺失。



以下是 Microsoft 身分識別 Sdk 如何啟用 SSO 代理人應用程式搭配使用的表示︰

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

配備您應該可以進一步瞭解並使用 Microsoft 識別平台和 Sdk 的應用程式中實作 SSO 此背景資訊。


## <a name="enabling-cross-app-sso-using-adal"></a>啟用跨應用程式 SSO 使用 ADAL

以下我們將使用 ADAL iOS SDK 至︰

- 開啟非代理人協助 sso 用您的應用程式的套件
- 開啟 [代理人協助 SSO 支援

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>開啟 sso 非代理人協助 SSO

在應用程式的非代理人協助 sso Microsoft 身分識別 Sdk 的 SSO 的複雜性為您管理。 這包含快取中尋找正確的使用者和維護來查詢您的登入使用者清單。

若要在應用程式啟用 SSO 您擁有您需要執行下列動作︰

1. 請確定您所有應用程式使用者相同的用戶端識別碼或應用程式識別碼]。
* 確保所有應用程式共用相同的簽署憑證從 Apple，讓您可以共用 keychains
* 要求相同的鑰匙圈授權的每個應用程式。
* 告訴有關共用 keychain Microsoft 身分識別 Sdk，您希望我們為使用。

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>使用相同的用戶端識別碼 / 應用程式識別碼您應用程式組件中的所有應用程式

若要讓 Microsoft 身分識別平台知道的允許在共用您的應用程式的權杖，每個應用程式需要共用相同的用戶端識別碼或應用程式識別碼]。 這是您在入口網站中註冊您的第一個應用程式時，您所提供的唯一識別碼。

您可能會想要知道如何您會識別 Microsoft 身分識別服務的其他應用程式會使用相同的應用程式識別碼。 Answer （回應） 是使用**重新導向 Uri**。 每個應用程式可以有多個重新導向 Uri 註冊登入入口網站中。 在您的套件中的每一個應用程式會有不同的重新導向 URI。 這樣的外觀的範例如下︰

App1 重新導向 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 重新導向 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 重新導向 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

這些以巢狀底下相同的用戶端識別碼 / 應用程式識別碼與查閱根據 URI 回到我們 SDK 設定中的重新導向。

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*請注意下列重新導向 Uri 的格式，說明如下。您可以使用任何重新導向 URI 除非您希望支援代理人，在此情況下他們必須看起來像以上*



#### <a name="create-keychain-sharing-between-applications"></a>建立共用的應用程式之間的鑰匙圈

啟用 [鑰匙圈共用這份文件的範圍之內，而覆蓋 Apple 他們的文件[新增功能](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)。 重要的是您決定您想要呼叫的鑰匙圈和您的所有應用程式新增的功能。

當您需要權利設定正確，您應該會看到標題為您的專案目錄中的檔案`entitlements.plist`含有類似下列動作︰

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

一旦您有啟用在每一個應用程式中的鑰匙圈授權，您已準備好使用 SSO x 的鑰匙圈的資訊告知 Microsoft 身分識別 SDK 使用中的下列設定您`ADAuthenticationSettings`使用下列設定︰

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
當您在您的應用程式共用鑰匙圈任何應用程式可以刪除使用者或甚至刪除應用程式上的 [所有權杖。 這是如果您有依賴至背景工作權杖應用程式，特別是慘。 所代表的意義，您必須注意的任何及所有共用的鑰匙圈移除透過 Microsoft 身分識別 Sdk 的作業。

這樣就可以 ！ Microsoft 身分識別 SDK 現在會在所有應用程式共用認證。 使用者清單也會共用應用程式的執行個體。

### <a name="turning-on-sso-for-broker-assisted-sso"></a>開啟 sso 代理人協助 SSO

**預設關閉**已應用程式使用任何裝置已安裝的代理人的功能。 以您的應用程式使用代理人必須進行一些額外的設定，並將部分程式碼新增至您的應用程式。

若要追蹤的步驟如下︰

1. 啟用 MS sdk 的變更您的應用程式碼的通話代理人模式。
2. 建立新的重新導向 URI，並提供的應用程式和您的應用程式註冊]。
3. 註冊 URL 配置。
4. iOS9 支援︰ 新增至 info.plist 檔案的權限。


#### <a name="step-1-enable-broker-mode-in-your-application"></a>步驟 1︰ 啟用應用程式中的代理人模式
當您建立的 「 內容 」 或初始設定驗證物件時，應用程式使用代理人的功能已開啟。 您執行程式碼中設定您的認證的類型︰

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO`設定可讓 Microsoft 身分識別 SDK 呼叫代理人，請嘗試`AD_CREDENTIALS_EMBEDDED`會防止代理人呼叫 Microsoft 身分識別 SDK。

#### <a name="step-2-registering-a-url-scheme"></a>步驟 2︰ 註冊 URL 結構描述
Microsoft 身分識別的平台使用 Url 叫用代理人，然後返回回到您的應用程式的 [控制項。 若要完成的來回必須註冊為 Microsoft 身分識別的平台就會知道您的應用程式的 URL 結構描述。 這可能是除了您可能會有登錄應用程式使用任何其他應用程式配置。

> [AZURE.WARNING]
我們建議您進行 URL 配置相當唯一的另一個應用程式使用相同的 URL 配置機會最小化。 Apple 不強制登錄應用程式存放區中的 URL 結構描述的唯一性。

以下是範例此專案設定中的顯示方式。 您可能也採取 XCode 以及︰

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>步驟 3︰ 建立新的重新導向您的 URL 配置 URI

為了確保我們永遠傳回正確的應用程式的認證權杖，我們要確認我們撥] 以回您的應用程式，可以驗證 iOS 作業系統的方式。 IOS 作業系統報告 Microsoft 代理人應用程式套件識別碼呼叫的應用程式。 此無法詐騙惡意應用程式。 因此，我們利用這以及我們的代理人應用程式，以確保權杖會傳回正確的應用程式的 URI。 我們會要求您應用程式中建立兩個 URI 這個唯一重新導向，並設定為在我們的開發人員入口網站重新導向 URI。

您重新導向 URI 必須在適當的格式︰

`<app-scheme>://<your.bundle.id>`

例如︰ *x-msauth-mytestiosapp://com.myapp.mytestapp*

此重新導向 URI 必須在您的應用程式註冊使用[Azure 傳統入口網站](https://manage.windowsazure.com/)中加以指定。 如需有關 Azure AD 應用程式註冊的詳細資訊，請參閱[與 Azure Active Directory 的拷貝](./develop/active-directory-how-to-integrate.md)。


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>步驟 3a︰ 您為支援憑證驗證的應用程式和開發入口網站中新增重新導向 URI

若要支援憑證依據第二個 「 msauth 」 需要註冊您的應用程式和[Azure 傳統入口網站](https://manage.windowsazure.com/)，來處理憑證驗證]，如果您想要新增到應用程式的支援驗證。

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

例如︰ *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>步驟 4: iOS9︰ 將設定參數新增至您的應用程式

ADAL 使用 – canOpenURL︰ 若要檢查是否代理人已在裝置上安裝。 Ios 9 Apple 鎖定應用程式的配置可以查詢。 您需要新增 「 msauth 」 的 LSApplicationQueriesSchemes 區段您`info.plist file`。

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>您已設定讓 SSO ！

現在 Microsoft 身分識別 SDK 會自動同時共用您的應用程式的認證，然後呼叫代理人，如果其裝置上。
