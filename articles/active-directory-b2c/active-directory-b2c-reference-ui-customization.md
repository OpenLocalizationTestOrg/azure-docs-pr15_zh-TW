<properties
    pageTitle="Azure Active Directory B2C︰ 使用者介面 (UI) 自訂 |Microsoft Azure"
    description="在使用者介面 (UI) 的自訂功能的 Azure Active Directory B2C 主題"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C︰ 自訂 Azure AD B2C 使用者介面 (UI)

使用者體驗是最重要的消費者具應用程式中。 則良好的應用程式和很棒的項目，以及之間只是作用中的使用者能與真正嚙合的項目之間的差異。 Azure Active Directory (Azure AD) B2C 可讓您自訂消費者註冊，登入 （*請參閱下方的註解*），編輯設定檔和 [重設密碼以像素完美控制項的頁面。

> [AZURE.NOTE]
目前本機帳戶登入頁面，其 accompaning 密碼重設頁面，您可以自訂驗證電子郵件，僅使用[公司商標功能](../active-directory/active-directory-add-company-branding.md)而不是本文所述的機制。

本文中，您將瞭解︰

- 頁面使用者介面 (UI) 的自訂功能。
- 工具，可協助您測試使用我們的範例內容的頁面 UI 自訂功能。
- 每個類型的頁面中的核心使用者介面項目。
- 執行這項功能時的最佳作法。

## <a name="the-page-ui-customization-feature"></a>頁面 UI 自訂功能

使用頁面 UI 自訂功能，您可以自訂消費者註冊、 登入的外觀與風格，重設密碼，然後編輯設定檔頁面 （藉由設定[原則](active-directory-b2c-reference-policies.md)）]。 您的使用者皆會有順暢的體驗，當您的應用程式和頁面之間瀏覽由 Azure AD B2C。

Azure AD B2C 則不同使用者介面選項限於的位置，或僅可透過 Api 其他服務，使用自訂頁面 UI 的新式 （和簡單） 方法。

運作方式如下︰ Azure AD B2C 在您的消費者瀏覽器中執行的程式碼，並使用稱為[十字形，共用資源 (CORS)](http://www.w3.org/TR/cors/)的新方法載入內容從原則中所指定的 URL。 您可以指定不同的不同頁面的 Url。 程式碼會從您的 URL，載入的內容合併從 Azure AD B2C UI 項目，並顯示您的消費者頁面。 您要做為︰

1. 建立內容與格式正確的 HTML5`<div id="api"></div>`元素 （必須是空的項目） 的某處中`<body>`。 插入 Azure AD B2C 內容的位置此項目符號。
2. （使用 CORS 允許) 來裝載您的內容上 HTTPS 端點。
3. Azure AD B2C 中插入的使用者介面元素的樣式。

## <a name="test-out-the-ui-customization-feature"></a>測試 UI 自訂功能

如果您想要嘗試使用我們的範例 HTML 和 CSS 內容的 UI 自訂功能，我們提供了您上傳，且您 Azure Blob 儲存體設定範例內容[的簡單的協助工具](active-directory-b2c-reference-ui-customization-helper-tool.md)。

> [AZURE.NOTE]
您也可以裝載您的使用者介面內容任何位置︰ 網頁伺服器，Cdn、 AWS S3，共用的檔案系統等。只要內容由可公開使用 HTTPS 端點上 （CORS 允許)，您是就緒。 我們使用僅供說明 Azure Blob 儲存體。

### <a name="the-most-basic-html-content-for-testing"></a>最基本的 HTML 內容，以進行測試

以下是最基本的 HTML 內容，您可以使用測試此功能。 使用相同的協助工具提供更舊版本上傳並設定您的 Azure Blob 儲存體這個內容。 然後，您可以驗證基本、 非稍許按鈕與每個頁面上的表單欄位會顯示和功能。

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>核心使用者介面元素中每個類型的頁面

在下列區段中，您會發現的 Azure AD B2C 合併至 html5 的功能完整的範例`<div id="api"></div>`項目位於您的內容。 **您不在 HTML 5 內容中插入這些片段。** Azure AD B2C 服務會在執行階段中插入。 您可以使用這些範例，設計您自己的樣式表。

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Azure AD B2C 內容頁面中插入 「 身分識別提供者選取範圍]

此頁面包含的使用者註冊或登入期間，可以選擇從身分識別提供者的清單。 這些是 [社交的身分識別提供者，例如 Facebook 和 Google + 或本機帳戶 （根據電子郵件地址或使用者名稱）。

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Azure AD B2C 內容頁面中插入 「 本機帳戶註冊 」

此頁面包含註冊表單使用者已註冊為基礎的電子郵件地址或使用者名稱的本機帳戶時，在 [填滿。 表單可以包含不同的輸入的控制項，例如文字輸入的方塊、 密碼項目] 方塊、 選項按鈕、 單一選取下拉式方塊中，然後多重選取核取方塊。

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Azure AD B2C 內容頁面中插入 「 「 社交帳戶註冊 」

此頁面包含消費者時使用現有的帳戶，例如 Facebook 或 Google + 社交身分識別提供者註冊填入的註冊表單。 此頁面很類似本機帳戶註冊畫面 （顯示在前一節） 但密碼項目欄位。

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Azure AD B2C 內容頁面中插入 「 整合註冊或登入 」

此頁面控點的同時註冊連線與登入的使用者皆可使用社交的身分識別提供者，例如 Facebook 或 Google + 或本機帳戶的人員。

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>插入 「 多重因素驗證頁 」 的 azure AD B2C 內容

在此頁面上，使用者可以驗證在進行註冊或登入的電話號碼 （使用文字或語音）。

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Azure AD B2C 內容頁面中插入 「 」 錯誤 」

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>建立您自己的內容時的注意事項

如果您打算使用頁面 UI 自訂功能，請檢閱下列最佳作法︰

- 沒有複製 Azure AD B2C 預設內容，並嘗試加以修改。 最好建立從頭 HTML5 內容，以及如何使用參考的預設內容。
- 在所有頁面 （除了屬於錯誤頁面） 的登入，註冊及編輯設定檔原則方式，您所提供的樣式表必須覆寫我們將這些頁面中的預設樣式表<head>片段。 中的所有網頁由註冊或登入和密碼重設原則，以及錯誤頁面上所有的原則，則必須提供所有樣式設定您自己。
- 基於安全性理由，我們不允許您在您的內容中包含任何 JavaScript。 大部分的所需應該使用預設工作。 如果不是，使用[使用者語音](http://feedback.azure.com/forums/169401-azure-active-directory)要求的新功能。
- 支援的瀏覽器版本︰
    - Internet Explorer 11
    - Internet Explorer 10
    - Internet Explorer 9 （有限制）
    - Internet Explorer 8 （有限制）
    - Google Chrome 43.0
    - Google Chrome 42.0
    - Mozilla Firefox 38.0
    - Mozilla Firefox 37.0
