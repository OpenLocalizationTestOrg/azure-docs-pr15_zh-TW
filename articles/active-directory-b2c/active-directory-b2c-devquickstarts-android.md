<properties
    pageTitle="Azure Active Directory B2C: Android 應用程式呼叫網路 API |Microsoft Azure"
    description="本文會告訴您如何建立 「 待辦事項清單 」 在 Android 電話 Node.js 網路 API 使用 OAuth 2.0 承載者權杖的應用程式。 Android 應用程式，然後網路 API 使用 Azure Active Directory B2C 來管理使用者身分識別與驗證的使用者。"
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: Android 應用程式呼叫網路 API

> [AZURE.WARNING] 本教學課程需要一些重要的更新，以便於移除 B2C ADAL 在 Android 上的使用。  我們要發佈的 Android 應用程式中使用 Azure AD B2C 下, 一週，全新的相關指示，我們建議您之前，按住。  但如果您只是要嘗試取出的項目，隨意繼續進行下列文件。



藉由使用 Azure Active Directory (Azure AD) B2C，您可以新增強大的自助身分識別管理功能，您的 Android 應用程式和網站的 Api 簡短的幾個步驟。 本文會告訴您如何建立 「 待辦事項清單 」 在 Android 電話 Node.js 網路 API 使用 OAuth 2.0 承載者權杖的應用程式。 Android 應用程式，然後網路 API 使用 Azure AD B2C 來管理使用者身分識別與驗證的使用者。

此快速入門必須受到 Azure AD B2C 完全搭配使用的 API 的 web。 我們建立一個.NET 和供您使用的 Node.js 兩者均適用。 此逐步解說假設經 Node.js 網路 API 範例。 如需詳細資訊，請參閱[Azure AD B2C 網路 API Node.js 教學課程](active-directory-b2c-devquickstarts-api-node.md)。

適用於 Android 用戶端需要存取受保護的資源，Azure AD 提供 Active Directory 驗證文件庫 (ADAL)。 對於 ADAL 的用途，讓您的應用程式，以取得存取權杖。 若要示範如何輕鬆，本指南中，我們將建立 Android 待辦事項清單應用程式的︰

- 取得使用[OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)呼叫待辦事項清單 API 的存取權杖。
- 取得使用者的待辦事項清單。
- 使用者符號。

> [AZURE.NOTE] 本文不使用 Azure AD B2C 涵蓋如何實作登入，註冊，以及管理設定檔。 旨在說明如何呼叫 web Api 之後驗證使用者。 如果您尚未選取，您應該開頭的[.NET web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)，瞭解如何 Azure AD B2C 的基本概念。

## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄

您可以使用 Azure AD B2C 之前，您必須建立目錄，或租用戶。 目錄是針對您所有的使用者、 應用程式、 群組及更多的容器。 如果您沒有，[建立 B2C 目錄](active-directory-b2c-get-started.md)之前繼續執行本指南中。

## <a name="create-an-application"></a>建立應用程式

接下來，您需要建立 B2C 目錄中的應用程式。 這會提供 Azure AD 資訊，以便與您的應用程式安全地溝通。 將應用程式與網路 API 會以單一**應用程式識別碼**在此情況下，因為它們組成一個邏輯的應用程式。 若要建立應用程式，請遵循[下列指示進行](active-directory-b2c-app-registration.md)。 請務必︰

- 包含**web 應用程式**/**網路 API**應用程式中。
- 輸入`urn:ietf:wg:oauth:2.0:oob`為**回覆 URL**。 則此程式碼範例的預設 URL。
- 建立應用程式的**應用程式密碼**並加以複製。 您會用得到。 請注意此值必須是您使用[XML 逸出字元](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)。
- 複製**應用程式識別碼**指派給您的應用程式。 您也必須此更新版本。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Azure AD B2C，在每個使用者體驗定義[原則](active-directory-b2c-reference-policies.md)。 此應用程式包含三個身分識別體驗︰ 註冊、 登入，並使用 Facebook 登入。  您需要建立的每個類型，一個原則[原則參考本文](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。 當您建立您的三個原則時，請務必︰

- 在您註冊的原則，選擇**顯示名稱**] 和 [註冊的其他屬性。
- 選擇 [**顯示名稱**] 和 [**物件識別碼**的應用程式宣告，每個原則。 您可以選擇以及其他宣告。
- 建立後，請複製每個原則**名稱**。 應有前置詞`b2c_1_`。  您需要下列原則名稱後面。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立三個原則之後，您可以準備開始建立您的應用程式。

請注意本文並未涵蓋如何使用您剛剛建立的原則。 若要深入瞭解原則 Azure AD B2C 中的運作方式，請從[.NET web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="download-the-code"></a>下載的程式碼

此教學課程[GitHub 上維護](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android)的程式碼。 若要建置範例，您即可，您可以[下載 zip 基本架構的專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)。 您也可以複製基本架構︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **您必須下載完成本教學課程中的基本架構。** 實作完整功能的 Android 應用程式的複雜，因為基本架構有 UX 程式碼會執行完成之後，您在此教學課程。 這是適用於開發人員節省時間量值。 無法將如何新增 B2C Android 應用程式的主題密切關係 UX 程式碼。

已完成的應用程式也是[使用 zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)上或`complete`分支的相同存放庫。

若要建立具有 Maven，您可以使用`pom.xml`最上層。

  1. 請遵循[設定 Maven Android 適用的先決條件一節](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)中的步驟進行。
  2. 設定與 SDK 21 模擬器。
  3. 移至根資料夾複製 repo 的位置。
  4. 執行命令`mvn clean install`。
  5. 切換至 [快速入門範例`cd samples\hello`。
  6. 執行命令`mvn android:deploy android:run`。

您應該會看到 [啟動應用程式。 輸入測試使用者認證，試試看。

Java 封存 (JAR) 套件會也送出，Android 封存 (AAR) 套件旁邊。

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>下載 Android ADAL，並將其新增至您的 Android Studio 工作區

您有要如何使用此文件庫 Android 專案中的選項︰

* 您可以使用的程式碼將蝕和連結的文件庫匯入您的應用程式。
* 如果您使用 Android Studio，您可以使用 AAR 套件格式，並參考的二進位檔案。

### <a name="option-1-binaries-via-gradle-recommended"></a>選項 1︰ 透過 Gradle （建議使用） 的二進位檔案

您可以從 Maven 中央 repo 取得二進位檔案。 在 Android Studio 專案中可包含 AAR 套件 (例如，在`build.gradle`) 這種方式︰

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>選項 2: AAR 透過 Maven

如果您是使用`m2e`外掛程式蝕中，您可以指定中的相依性您`pom.xml`檔案︰

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>選項 [3︰ 來源透過給 （最後一個步驟）

若要取得透過給 SDK 的原始碼，請輸入︰

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

使用分支**交集。**

## <a name="set-up-your-configuration-file"></a>設定您的設定檔

使用您設定的設定舊版 B2C 入口網站設定 Android 專案中。

開啟`helpes/Constants.java`並填入下列的值︰

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`︰ 您傳送至您想要從伺服器當使用者要求的伺服器範圍登入。 B2C 預覽，在您傳送`client_id`。 不過，若要變更為預期這`read scopes`未來。 發生時，就會更新這份文件。
- `ADDITIONAL_SCOPES`︰ 您可能會想要使用您的應用程式的其他範圍。 這些都必須在未來使用。
- `CLIENT_ID`︰ 您有從入口網站應用程式識別碼。
- `REDIRECT_URL`︰ 您所預期回傳權杖重新導向。
- `EXTRA_QP`︰ 任何其他您想要傳送至伺服器的 URL 編碼的格式。
- `FB_POLICY`︰ 您所叫用原則。 這是最重要的部分逐步解說。
- `EMAIL_SIGNIN_POLICY`︰ 您所叫用原則。 這是最重要的部分逐步解說。
- `EMAIL_SIGNUP_POLICY`︰ 您所叫用原則。 這是最重要的部分逐步解說。

## <a name="add-references-to-android-adal-to-your-project"></a>新增至 Android ADAL 參照至專案

> [AZURE.NOTE]  Android 版 ADAL 叫用驗證使用用途型模型。 對應方式 」 區內 「 工作的應用程式。 此完整的範例和 for Android 中，所有 ADAL 中心如何管理方式，並將它們之間的資訊。

首先，告訴 Android 應用程式，包括您想要使用的對應方式的版面配置。 稍後在本教學課程，這些對應方式將詳細說明。

更新您的專案`AndroidManifest.xml`檔案包含所有您對應方式︰

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

如您所見，您可以定義五個活動。 您會使用這些。

- `AuthenticationActivity`︰ 這是來自 ADAL，並提供登入網頁檢視。
- `LoginActivity`︰ 這會顯示您的登入原則] 和 [針對每個原則] 按鈕。
- `SettingsActivity`︰ 您可以使用此選項變更在執行階段的應用程式設定。
- `AddTaskActivity`︰ 您可以使用此選項將任務新增至您的 REST API 受到 Azure AD。
- `ToDoActivity`︰ 這是顯示任務的主要活動。

## <a name="create-the-sign-in-activity"></a>建立登入活動

建立主活動，然後呼叫該`LoginActivity`。

建立檔案稱為`LoginActivity.java`。

您需要初始化活動，並新增一些會控制您的使用者介面的按鈕。 如果您編寫 Android 之前的程式碼，這是您熟悉。

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
您已經建立的通話] 按鈕您`ToDoActivity`用途 （這需要權杖時，請打電話給 ADAL）。 他們使用您的活動和額外的參數為執行這個動作。 這個額外的參數傳遞時`intent.putExtra()`方法。 您可以在定義`"thePolicy"`使用您在中指定`Constants.java`。 這會告訴目的叫用驗證期間的原則。

## <a name="create-the-settings-activity"></a>建立設定活動

這是會填入您的設定使用者介面的活動。

建立檔案稱為`SettingsActivity.java`簡單的建立、 讀取、 更新及刪除 (CRUD) 作業。

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>建立新增工作活動

若要將任務新增至您的 REST API 端點，您可以使用此活動。

建立檔案稱為`AddTaskActivity.java`和寫入下列。

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>建立待辦事項清單活動

這是最重要的活動。 您可以採用它從 Azure AD 原則，取得權杖，並再使用該權杖撥工作 REST API 伺服器。

建立檔案稱為`ToDoActivity.java`和寫入下列。 （來電會解釋稍後。）

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 您可能已經注意到此依賴尚未被撰寫的方法。 這些工作包括`updateLoggedInUser()`， `clearSessionCookie()`，及`getTasks()`。 您所撰寫本指南中的更新版本。 您可以忽略的錯誤，在 Android Studio 中現在。

參數的說明︰

  - `SCOPES`︰ 必要，請您嘗試要求存取權的範圍。 這是相同 B2C 預覽版`client_id`，但在未來變更正常運作。
  - `POLICY`︰ 當您想要使用者進行驗證，原則。
  - `CLIENT_ID`: 必要、 來自 Azure AD 入口網站。
  - `redirectUri`︰ 可以將設定為您套件的名稱。 不需要的`acquireToken`來電。
  - `getUserInfo()`︰ 查閱使用者是否已快取中方式。 參數也會說明如何將提示使用者，如果找不到該使用者或使用者的存取權杖為無效。 本指南稍後的寫入這個方法。
  - `PromptBehavior.always`︰ 詢問您的認證，即可略過的快取及 cookie 幫助。
  - `Callback`︰ 稱為之後的權杖交換驗證碼。 會有物件`AuthenticationResult`，其中包含存取權杖、 到期日及 ID token 資訊。

> [AZURE.NOTE]  Microsoft Intune 公司入口網站應用程式提供的代理人元件，您可能會在使用者的裝置上安裝。 應用程式提供在裝置上的所有應用程式的單一登入 (SSO) 存取。 開發人員應準備允許 Intune。 Android 版 ADAL 驗證所建立的一個使用者帳戶時，會使用代理人帳戶。 若要使用的代理人，開發人員需要註冊特殊`redirectUri`使用代理人的。 `redirectUri`位於 msauth://packagename/Base64UrlencodedSignature 的格式。 您可以取得`redirectUri`應用程式使用指令碼`brokerRedirectPrint.ps1`，或使用 API 呼叫`mContext.getBrokerRedirectUri()`。 從 Google Play 商店簽章相關您簽章的憑證。

 您可以使用代理人使用者略過︰

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] 若要降低這個 B2C 快速入門的複雜性，我們選擇略過的代理人，在我們的範例。

接下來，建立協助方法，取得驗證的來電至] 任務 API 期間的時還大的權杖。

在同一個`ToDoActivity.java`檔案、 撰寫下列。

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

新增將 「 設定 」 和 「 取得 」 的方法`AuthenticationResult`（擁有您的憑證） 至通用`Constants`。 即使`ToDoActivity.java`使用`sResult`其書寫，您需要新增這些方法。 如果沒有的話，您其他活動無法存取工作的權杖 (例如新增中的任務`AddTaskActivity.java`)。

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>建立要傳回的使用者識別碼的方法

ADAL 的 Android 代表在表單中的使用者`UserIdentifier`物件。 管理使用者。 您可以使用物件在通話中使用相同的使用者。 藉由使用這項資訊，您可以使用快取，而不是撥打電話新增至伺服器。 若要讓您更容易，我們建立`getUserInfo()`傳回方法`UserIdentifier`。 您可以使用這個與`acquireToken()`。 我們也建立`getUniqueId()`會傳回的識別碼方法`UserIdentifier`快取中。

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>撰寫協助方法

接下來，撰寫可協助您清除 cookie，並提供一些協助方法`AuthenticationCallback`。 這些方法用只提供範例，請確定您是在初始狀態，當您撥打時您`ToDo`活動。

在相同的檔名為`ToDoActivity.java`，撰寫下列。

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>打電話給任務的 API

您的活動，吸引權杖準備好之後，您可以撰寫您的 API 存取工作伺服器。

`getTasks`提供陣列，表示您的伺服器中的工作。

開始`getTasks`。

在相同的檔名為`ToDoActivity.java`，撰寫下列。

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

也撰寫的方法，將初始化表格第一次執行。

在相同的檔名為`ToDoActivity.java`，撰寫下列。

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

您可以看到這個程式碼需要執行工作的一些其他方法。 撰寫這些下一步]。

### <a name="create-an-endpoint-url-generator"></a>建立的端點 URL 產生器

您需要產生的您會連線到端點 URL。 在相同的類別檔案中執行這項作業。

**在相同的檔**名為`ToDoActivity.java`，撰寫下列。

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


請注意您在邀請中的下一節所述的程式碼新增存取權杖。

## <a name="write-some-ux-methods"></a>撰寫某些 UX 方法

Android 需要您處理某些回撥運作的應用程式。 這些是`createAndShowDialog`和`onResume()`。 如果您編寫 Android 之前的程式碼，這是您熟悉。

在相同的檔名為`ToDoActivity.java`，撰寫下列。

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

接下來，[管理您的對話方塊回撥]。

在相同的檔名為`ToDoActivity.java`，撰寫下列。

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

您現在應該`ToDoActivity.java`編譯的檔案。 此時也應該編譯整個專案。

## <a name="run-the-sample-app"></a>執行範例應用程式

最後，建立並執行應用程式在 Android Studio 或蝕。 註冊或登入應用程式。 建立登入使用者的工作。 登出並再次登入以不同的使用者，然後建立該使用者的工作。

請注意工作會儲存每位使用者在 API，因為 API 的存取權杖，就會收到算起的使用者身分識別。

參照的已完成的範例[，提供為.zip 檔案](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)。 您也可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>重要資訊


### <a name="encryption"></a>加密

ADAL 加密的權杖及儲存在`SharedPreferences`預設。 您可以查看`StorageHelper`類別，以查看詳細資料。 Android 推出**的 4.3(API18) AndroidKeyStore**私密金鑰的安全儲存。 ADAL 使用的 API18 和上方。 如果您想要使用 ADAL 較低 SDK 版本，您需要提供私密金鑰在`AuthenticationSettings.INSTANCE.setSecretKey`。

### <a name="session-cookies-in-web-view"></a>在網頁檢視中的工作階段 cookie

關閉應用程式之後，android web 檢視並不會清除工作階段 cookie。 您可以使用下列程式碼來處理。
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[深入瞭解 cookie](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)。
