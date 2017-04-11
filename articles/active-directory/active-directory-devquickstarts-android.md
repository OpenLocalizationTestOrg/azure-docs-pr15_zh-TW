<properties
    pageTitle="快速入門 azure AD Android |Microsoft Azure"
    description="如何建立 Android 應用程式的整合 Azure AD 登入並通話 Azure AD 受到保護的 Api 使用 OAuth。"
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Azure AD 整合 Android 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

如果您開發桌面應用程式，Azure AD 很簡單且直接，以使用 Active Directory 帳戶驗證您的使用者。  同時也會啟用安全地使用任何受 Azure AD，例如 Office 365 Api 或 Azure API 的 API 的 web 應用程式。

適用於 Android 用戶端需要存取受保護的資源，Azure AD 提供的 Active Directory 驗證庫或 ADAL。  ADAL 的生活中唯一的目的是讓您的應用程式，以取得存取權杖能輕鬆地。  若要示範如何只要輕鬆，以下我們會建立一個 Android 待辦事項清單應用程式的︰

-   取得存取權杖撥使用[OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)待辦事項清單 API。
-   取得使用者的待辦事項清單
-   取出符號使用者。

若要開始，您必須 Azure AD 租用戶，您可以在其中建立使用者，並登錄應用程式。  如果您還沒有租用戶，[瞭解如何取得](active-directory-howto-tenant.md)。

> [AZURE.TIP] 請試試我們將協助您快速上手與 Azure Active Directory 中只需幾分鐘的新[開發人員入口網站](https://identity.microsoft.com/Docs/Android)的預覽 ！  開發人員入口網站會引導您執行登錄應用程式，並將 Azure AD 整合至您的程式碼的程序。  完成後，您會有一個簡單的應用程式，可以驗證您的租用戶和後端，可接受權杖，並執行驗證的使用者。 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>步驟 1︰ 下載並執行 Node.js REST API TODO 範例伺服器

此範例會寫入工時與現有的 Microsoft Azure Active Directory 中建立單一租用戶待辦事項 REST API 範例，以便於。 這是 [快速入門的必要條件。

如需如何設定的資訊，請造訪我們現有的範本︰

* [Microsoft Azure Active Directory 範例 REST API 服務 Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>步驟 2︰ 使用您的 Microsoft Azure AD 租用戶註冊您的網路 API

**我正在執行什麼動作？**

*Microsoft Active Directory 支援新增兩種類型的應用程式。Web 提供服務給使用者和應用程式 （無論是在 web 或裝置上執行的應用程式上） 存取這些網頁 Api 的 Api。在此步驟中您要註冊 Web API 您執行的本機以進行測試此範例。以正常方式此網頁 API 就會提供您要存取應用程式的功能的其餘服務。Microsoft Azure Active Directory 可以保護任何端點 ！*

*以下我們假設您要註冊參照上述 TODO REST API，但這適用於任何您想要保護的 Azure Active Directory 的 Web API。*

向 Microsoft Azure AD 註冊 Web API 的步驟

1. [Azure 管理入口網站](https://manage.windowsazure.com)登入。
2. 按一下 [在 Active Directory 中左側導覽。
3. 按一下您想要註冊範例應用程式目錄租用戶。
4. 按一下 [應用程式] 索引標籤。
5. 在抽屜，按一下 [新增]。
6. 按一下 [新增組織內部開發的應用程式]。
7. 輸入應用程式，例如 「 TodoListService，「 選取 「 Web 應用程式和/或 Web API 」，易記的名稱，然後按一下 [下一步]。
8. 登入 URL，請輸入基底 URL，此範例中，這是預設的`https://localhost:8080`。
9. 應用程式識別碼 URI 中，輸入`https://<your_tenant_name>/TodoListService`、 取代`<your_tenant_name>`Azure AD 租用戶的名稱。  按一下 [確定] 以完成註冊]。
10. 中 Azure 入口網站中，按一下 [應用程式的 [設定] 索引標籤。
11. **找不到用戶端識別碼值，並將其保留複製**，您會需要此更新版本設定您的應用程式時。

## <a name="step-3-register-the-sample-android-native-client-application"></a>步驟 3︰ 註冊範例 Android 的原生用戶端應用程式

註冊您的 web 應用程式是第一個步驟。 接下來，您需要您應用程式的資訊告知 Azure Active Directory。 這個選項可讓您只要註冊 Web API 與通訊的應用程式

**我正在執行什麼動作？**  

*如以上所述，Microsoft Azure Active Directory 支援新增這兩種應用程式。Web 提供服務給使用者和應用程式 （無論是在 web 或裝置上執行的應用程式上） 存取這些網頁 Api 的 Api。在此步驟中您要註冊此範例中的應用程式。能夠存取 Web API 邀請您註冊此應用程式的順序，您必須執行這項作業。Azure Active Directory 會拒絕甚至可讓您的應用程式要求的登入，除非已註冊 ！這是安全性模型的一部分。*

*以下我們假設您要註冊參照上方，此範例應用程式，但這適用於開發任何應用程式。*

**為什麼我我置於應用程式和 Web API 一個租用戶？**

*您可能已經猜到，您可以建置應用程式存取外部 API 註冊 Azure Active Directory 中的另一個租用戶。如果您執行這項作業時，會提示您的客戶同意應用程式中的 api 使用。部份的組件是，此為您同意負責 iOS 版的 Active Directory 驗證庫 ！我們在中取得更多進階功能，您看到這是很重要的一部分來存取 Microsoft Api 的套件從 Azure 和 Office，以及任何其他服務提供者所需的工作。現在，因為您註冊您 Web API 」 和 「 在相同的租用戶應用程式不會看到任何提示同意。如果您開發只使用公司的應用程式，這是通常大小寫。*

1. [Azure 管理入口網站](https://manage.windowsazure.com)登入。
2. 按一下 [在 Active Directory 中左側導覽。
3. 按一下您想要註冊範例應用程式目錄租用戶。
4. 按一下 [應用程式] 索引標籤。
5. 在抽屜中，按一下 [新增]。
6. 按一下 [新增組織內部開發的應用程式]。
7. 輸入好記的名稱，讓應用程式，例如 「 TodoListClient Android 」，請選取 [原生用戶端應用程式]，然後按一下 [下一步]。
8. 重新導向 URI 中，輸入`http://TodoListClient`。  按一下 [完成]。
9. 按一下 [應用程式的 [設定] 索引標籤。
10. 找出用戶端識別碼值，並將它複製後，您會需要此更新版本設定您的應用程式時。
11. 「 權限以其他應用程式中 」，按一下 [新增應用程式]。  在 [顯示] 下拉式清單中，選取 [其他]，然後按一下上方的核取記號。  尋找並按一下 TodoListService，並按一下底部的核取記號，若要新增應用程式。  從 [委派的權限] 下拉式清單中，選取 [存取 TodoListService 」，並儲存設定。



若要建立具有 Maven，您可以使用 pom.xml 最上層

  * 複製到您所選擇的目錄此 repo 中︰

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * 請遵循[設定您的 android maven Prerequests 節](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)中的步驟
  * 設定與 SDK 19 模擬器
  * 移至您複製而 repo 的根資料夾
  * 執行命令︰ mvn 全新安裝
  * 切換至 [快速入門的範例︰ cd samples\hello
  * 執行命令︰ mvn android︰ 部署 android︰ 執行
  * 您應該會看到 [啟動應用程式
  * 輸入測試使用者認證，以嘗試 ！

會在旁邊 aar 套件也送出 jar 套件。

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>步驟 4︰ 下載 Android ADAL，並將其新增至您的蝕工作區

我們已進行您要使用 Android 專案中的此文件庫的多個選項︰

* 若要匯入此文件庫蝕和連結至您的應用程式，您可以使用的程式碼。
* 如果使用 Android Studio，您可以使用*aar*套件格式，並參照的二進位檔案。

####<a name="option-1-source-zip"></a>選項 1︰ 來源 Zip

若要下載的程式碼的複本，請按一下 [右側的頁面中的 [「 下載 ZIP 」，或按一下[這裡](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz)。

####<a name="option-2-source-via-git"></a>選項 2︰ 來源透過給

若要取得透過給 SDK 的原始程式碼只輸入︰

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>透過 Gradle 選項 [3︰ 二進位檔案

您可以從 Maven 中央 repo 取得二進位檔案。 AAR 套件可以包含在 AndroidStudio 中您專案中，如下所示︰

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>透過 Maven 選擇 4: aar

如果您使用的在蝕 m2e 外掛程式，您可以指定相依性 pom.xml 檔案中︰

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>選項 5: jar 內程式庫] 資料夾的封裝
您可以從 maven repo 取得 jar 檔案拖放到 [*程式庫*] 資料夾在專案中。 您需要您的專案，以及複製所需的資源，因為 jar 封包不含這些。


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>步驟 5︰ 新增 Android ADAL 參照至專案


2. 新增至您的專案參照，並將其指定為 Android 的文件庫。 如果您不確定如何執行此動作，[按這裡如需詳細資訊] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. 新增至您的專案設定偵錯的專案相依性

4. 更新您的專案 AndroidManifest.xml 檔案包含︰

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. 建立您的主要活動 AuthenticationContext 的執行個體。 這個通話的詳細資料，超出此讀我檔案的範圍，但，就可以開始查看[Android 原生的用戶端範例](https://github.com/AzureADSamples/NativeClient-Android)。 以下是範例︰

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * 附註︰ mContext 是您的活動的欄位

8. 複製這個使用者輸入認證，並會接收授權程式碼後處理 AuthenticationActivity 結尾的程式碼區塊︰

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. 您可以要求權杖，定義回撥

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. 最後，要求使用回撥的權杖︰

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

參數的說明︰

  * 資源需要且您嘗試存取資源。
  * Clientid 很重要，來自 AzureAD 入口網站。
  * 您可以為您 packagename 設定 redirectUri。 您不需要的 acquireToken 通話。
  * PromptBehavior 可協助要求認證，即可略過快取及 cookie。
  * 權杖交換授權程式碼之後，就會呼叫回撥。

  回撥必須有 accesstoken AuthenticationResult 的物件、 到期日期，和 idtoken 資訊。

可省略︰ **acquireTokenSilent**

您可以呼叫**acquireTokenSilent**處理快取，與權杖重新整理。 它會提供以及同步處理版本。 將其做為參數接受使用者識別碼。

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **代理人**︰ Microsoft Intune 公司入口網站應用程式將會提供代理人元件。 ADAL 會的使用代理人的帳戶，如果有一個使用者帳戶會建立在此驗證者與開發人員選擇不跳過這個步驟。 開發人員可以略過的代理人使用者︰

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 需要註冊代理人使用量的特殊 redirectUri 開發人員。 RedirectUri 位於 msauth://packagename/Base64UrlencodedSignature 的格式。 您可以使用 「 brokerRedirectPrint.ps1 」 的指令碼應用程式取得您 redirecturi 或 API 通話 mContext.getBrokerRedirectUri。 簽章被與您簽署的憑證。

 目前的代理人模型為一位使用者。 AuthenticationContext 提供取得代理人使用者的 API 方法。

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 如果是有效的帳戶，就會傳回代理人使用者。

 您的應用程式資訊清單應有使用 AccountManager 帳戶權限︰ http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


使用此逐步解說，您應該要順利整合與 Azure Active Directory。 更多範例此工作的詳細資訊，請造訪 AzureADSamples / GitHub 上的儲存機制。

## <a name="important-information"></a>重要資訊

### <a name="customization"></a>自訂

文件庫專案資源可以覆寫應用程式資源。 會發生這種情況時建立您的應用程式。 因此，您可以自訂驗證活動版面配置以您希望的方式。 您需要確認該 ADAL uses(Webview) 保留控制項的識別碼。

### <a name="broker"></a>代理人

使用 Microsoft Intune 公司入口網站應用程式會傳遞代理人元件。 在 [帳戶管理員，會建立帳戶。 帳戶類型為 「 com.microsoft.workaccount 」。 只允許單一 SSO 帳戶。 完成裝置挑戰的其中一個應用程式後，就會建立 SSO cookie 此使用者。

### <a name="authority-url-and-adfs"></a>授權 Url] 與 [ADFS

ADFS 無法辨識為生產 STS，所以您要開啟的執行個體探索，並在 AuthenticationContext 建構函式傳遞 false。

授權 url 需要 STS 執行個體和租用戶名稱︰ https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>查詢快取的項目

ADAL 提供 SharedPreferences 中的一些簡單的快取的預設快取查詢函數。 您可以從使用 AuthenticationContext 取得目前快取︰
```Java
 ITokenCacheStore cache = mContext.getCache();
```
如果您想要加以自訂，您也可以提供您的快取實作。
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

ADAL 會提供選項，請指定提示行為。 如果重新整理權杖無效時需要使用者認證 UI PromptBehavior.Auto 就會出現。 PromptBehavior.Always 會略過快取的使用狀況，永遠顯示使用者介面。

### <a name="silent-token-request-from-cache-and-refresh"></a>從快取及重新整理無權杖要求

這個方法不使用 UI pop 設定並不需要的活動。 它會傳回 token 從快取如果有的話。 如果已過期權杖，它會嘗試重新整理。 如果重新整理權杖已到期或失敗，則會傳回 AuthenticationException。

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

您也可以讓這個方法包含通話的同步處理。 您可以設定以回撥 null，或者使用 acquireTokenSilentSync。

### <a name="diagnostics"></a>診斷程式

以下是資訊的主要來源的診斷問題:

+ 例外狀況
+ 記錄檔
+ 網路追蹤

此外，請注意相互關聯識別碼是在文件庫中的診斷中心。 您可以設定您的相互關聯識別碼，如果您想要建立關聯 ADAL 的每一個要求基準要求的程式碼中的其他操作。 如果您未設定的相互關聯識別碼，然後 ADAL 會產生隨機一個 and 所有記錄的郵件和網路通話會戳記的相互關聯識別碼。 自動產生的識別碼變更每個要求。

#### <a name="exceptions"></a>例外狀況

當然，這是第一份診斷。 我們試著提供有用的錯誤訊息。 如果您找到的不是很有幫助請檔案有問題，而讓我們知道。 請也提供裝置資訊，例如模型和 SDK #。

#### <a name="logs"></a>記錄檔

您可以設定文件庫產生記錄訊息，您可用來協助診斷問題。 您可以進行下列設定 ADAL 會使用要關閉每個記錄訊息時，會產生回撥來電設定記錄。


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
訊息可寫入自訂的記錄檔，如下所示。 很抱歉，沒有從裝置開始記錄的標準方法。 有某些這可協助您的服務。 您可以也設計您自己的圖片，例如傳送至伺服器的檔案。

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>記錄層級

+ Error(Exceptions)
+ Warn(Warning)
+ 資訊 （資訊的目的）
+ 詳細資訊 （更多詳細資料）

您設定記錄層級，像這樣︰
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 所有記錄訊息會都傳送到任何自訂的記錄檔回撥除了 logcat。
您可以為顯示 belog 取得檔案表單 logcat 記錄︰

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 更多關於 adb 命令的範例︰ https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>網路追蹤

您可以使用各種不同的工具來擷取 ADAL 產生 HTTP 流量。  如果您熟悉的 OAuth 通訊協定，或如果您需要提供診斷資訊給 Microsoft 或其他支援頻道，這是最有用。

Fiddler 是最簡單的 HTTP 追蹤工具。  使用下列連結將其設定進位到正確的記錄 ADAL 的網路流量。  才能實用就必須設定 fiddler 或任何其他工具，例如查理，錄製加密的 SSL 流量。  注意︰ 這種方式產生的追蹤可能包含具有高權限的資訊，例如存取權杖，使用者名稱和密碼。  如果您使用生產帳戶，請與第 3 的合作對象共用追蹤項目。  如果您需要提供給其他人追蹤，若要取得支援，重現問題與使用者名稱和密碼，您不介意共用以暫時帳戶。

+ [Android 版 Fiddler 設定](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [針對 ADAL 設定 Fiddler 規則](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>對話方塊模式
acquireToken 方法活動不支援對話方塊提示。

### <a name="encryption"></a>加密

ADAL 加密的權杖及 SharedPreferences 中預設的存放區。 您可以查看 StorageHelper 類別，查看詳細資料。 Android 推出 AndroidKeyStore 4.3(API18) 私密金鑰的安全儲存空間。 ADAL 使用的 API18 和上方。 如果您想要使用 ADAL 較低 SDK 版本，您需要提供在 AuthenticationSettings.INSTANCE.setSecretKey 私密金鑰

### <a name="oauth2-bearer-challenge"></a>Oauth2 承載者挑戰

AuthenticationParameters 類別提供從 Oauth2 承載者挑戰取得 authorization_uri 的功能。

### <a name="session-cookies-in-webview"></a>在網頁檢視中的工作階段 cookie

關閉應用程式之後，在 android 的網頁檢視並不會清除工作階段 cookie。 您可以使用以下範例程式碼來處理︰
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
深入瞭解 cookie: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>資源覆寫

ADAL 的文件庫包含兩個 ProgressDialog 郵件英文字串。

您的應用程式應該它們覆寫視本地化的字串。

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>NTLM] 對話方塊
Adal 版本 1.1.0 支援透過從 WebViewClient onReceivedHttpAuthRequest 事件處理 NTLM] 對話方塊。 您可以自訂對話方塊版面配置和字串。

### <a name="cross-app-sso"></a>跨應用程式 SSO
瞭解[如何啟用跨應用程式 SSO 使用 ADAL 的 Android 上](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
