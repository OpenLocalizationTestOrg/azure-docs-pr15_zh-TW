###<a name="server-auth"></a>如何︰ 驗證提供者 （伺服器流量）

若要管理您的應用程式中的驗證程序的行動應用程式，您必須註冊您的應用程式的身分識別提供者。 然後您 Azure 應用程式服務中，您需要設定應用程式識別碼與您的提供者所提供的密碼。
如需詳細資訊，請參閱教學課程 [新增驗證您的應用程式]。

當您註冊您的身分識別提供者之後時，只要呼叫.login() 方法與您的提供者的名稱。 例如，若要登入 Facebook 使用下列程式碼。

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

如果您使用的 Facebook 以外的身分識別提供者，變更傳遞給登入上述方法將下列的值︰ `microsoftaccount`， `facebook`， `twitter`， `google`，或`aad`。

在此情況下，Azure 應用程式服務會顯示選取的提供者的登入頁面，並產生的應用程式服務驗證的權杖之後成功的登入身分識別提供者管理 OAuth 2.0 驗證流程。 登入函數，完成時，會傳回 JSON 物件 （使用者），公開使用者識別碼及應用程式服務驗證權杖使用者識別碼和 authenticationToken 欄位中，分別。 此 token 加可以快取，並重複使用，直到到期。

###<a name="client-auth"></a>如何︰ 驗證提供者 （用戶端流量）

您的應用程式可以也獨立連絡 [身分識別提供者，然後提供驗證您的應用程式服務將傳回的權杖。 這個用戶端流量可讓您為使用者提供單一登入體驗，或從身分識別提供者取得其他使用者資料。

#### <a name="social-authentication-basic-example"></a>社交驗證基本範例

此範例使用 Facebook 用戶端 SDK 驗證︰

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
假設 SDK 的個別提供者所提供的憑證，會儲存在 token 的變數。

#### <a name="microsoft-account-example"></a>Microsoft 帳戶範例

下列範例會使用 Live sdk，您可以使用 Microsoft 帳戶支援單一登入 Windows 市集應用程式︰

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

此範例會從 Live 連線，其中會提供您的應用程式服務呼叫登入函數來取得權杖。

###<a name="auth-getinfo"></a>如何︰ 取得已驗證的使用者資訊

驗證擷取的資訊為目前的使用者可以從`/.auth/me`使用任何 AJAX 方法的結束點。  請確定您設定`X-ZUMO-AUTH`您驗證的權杖的標題。  驗證權杖會儲存在`client.currentUser.mobileServiceAuthenticationToken`。  例如，若要使用擷取 API:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

為 npm 套件，或從 CDNJS 的瀏覽器下載使用擷取。 您也可以使用 jQuery 或其他 AJAX API 來擷取資訊。  資料將會收到 JSON 物件。
