<properties
    pageTitle="快速入門 Azure AD 登入與登出使用 node.js"
    description="如何建立 node.js Express MVC Web 應用程式的整合 Azure AD 登入。"
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="brandwe"/>

# <a name="nodejs-web-app-sign-in--sign-out-with-azure-ad"></a>NodeJS Web 應用程式登入與登入與 Azure AD 出


以下我們將使用密碼來︰

- 登入使用 Azure AD 應用程式的使用者。
- 顯示使用者的部分資訊。
- 登入登出應用程式的使用者。

**密碼**會驗證介軟體的 Node.js。 非常有彈性和模組化、 密碼小心卸除任何快速計算或 Resitify web 應用程式。 完整的策略支援驗證使用的使用者名稱和密碼、 Facebook、 Twitter 等。 我們適用於 Microsoft Azure Active Directory 開發策略。 我們會安裝此模組，然後將 [Microsoft Azure Active Directory`passport-azure-ad`外掛程式。

若要這麼做，您必須︰

1. 登錄應用程式。
2. 若要使用的密碼 azure ad 策略設定您的應用程式。
3. 使用密碼來登入及教具借出要求發給 Azure AD。
4. 列印之使用者的相關資料。

本教學課程中的程式碼是來維護[GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS)。  若要遵循，您可以[下載應用程式的基本架構為.zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)或複製基本架構︰

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

完成的應用程式提供在本教學課程結尾處。

## <a name="1-register-an-app"></a>1.註冊應用程式
- 登入 Azure 管理入口網站。
- 在左側導覽中，按一下 [ **Active Directory**。
- 選取您想要註冊的應用程式的租用戶。
- 按一下 [**應用程式**] 索引標籤，底部抽屜中按一下 [新增]。
- 依照提示操作，並建立新**Web 應用程式及/或 WebAPI**。
    - 應用程式的**名稱**會說明您的使用者的應用程式
    -   **登入 URL**是您的應用程式的基本的 URL。  基本架構的預設值是 「 http://localhost:3000/驗證/openid/傳回 '。
    - **應用程式識別碼 URI**是您的應用程式的唯一識別碼。  慣例是使用`https://<tenant-domain>/<app-name>`，例如︰`https://contoso.onmicrosoft.com/my-first-aad-app`
- 當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。  此值必須在下一個區段中，因此複製的 [設定] 索引標籤。

## <a name="2-add-pre-requisities-to-your-directory"></a>2.前 requisities 新增至您的目錄

從命令列，那里變更至您的根資料夾如果不是已的目錄，然後執行下列命令︰

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- 此外，您需要我們`passport-azure-ad`以及︰

- `npm install passport-azure-ad`

這將會安裝文件庫的密碼 azure ad 而定。

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3.設定您的應用程式使用密碼-節點-js 策略
在這裡，我們會設定為使用 OpenID 連線驗證通訊協定 Express 介軟體。  密碼會用來發出登入並教具借出要求與管理使用者的工作階段，取得使用者，內的其他項目之間的相關資訊。

-   若要開始，請開啟`config.js`檔案根目錄中的專案，並輸入您的應用程式中的設定值`exports.creds`一節。
    -   `clientID:`指派給您的應用程式，請在註冊入口網站的**應用程式識別碼**。
    -   `returnURL` **重新導向 Uri**您輸入在入口網站。
    - `clientSecret`為您產生入口網站中的密碼

- 接下來開啟`app.js`檔案中的專案的根目錄，並將叫用下列電話`OIDCStrategy`隨附的策略`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- 之後，請使用我們只我們登入要求參照的策略

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
密碼會使用類似模式，所有是所有的策略作者依循的策略 （Twitter、 Facebook 等）。 您看到我們傳遞具有權杖，完成的參數為 function() 位於策略。 策略會盡責回到我們之後會所有的工作。 後會我們會想要儲存使用者及藏匿權杖，因此我們不需要再次要求其。


> [AZURE.IMPORTANT] 
上述的程式碼會為我們的伺服器驗證發生任何使用者。 這就是自動註冊。 生產伺服器中，您不想要讓任何人不必第一個其可幫助您決定註冊程序。 這通常是您在 [可以讓您與 Facebook 註冊，但再詢問您要填入其他資訊的消費者應用程式中看到的模式。 如果這不是範例應用程式，我們可能有只擷取電子郵件從權杖物件傳回，然後要求他們填入其他資訊。 這是測試伺服器因為我們只將其新增至記憶體中資料庫。

- 接下來，現在就讓我們新增的方法，讓我們來追蹤已登入所需的密碼的使用者。 這包含序列化和還原序列化使用者的資訊︰

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- 接下來，新增要載入快速引擎的程式碼。 以下您會看到我們使用預設 /views 並表達的 /routes 圖樣提供。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後，讓我們來新增會交出實際的登入要求路由`passport-azure-ad`引擎︰

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4.使用密碼來登入及教具借出要求發給 Azure AD

通訊與使用 OpenID 連線驗證通訊協定 v2.0 端點現在正確地設定您的應用程式。  `passport-azure-ad`具有付的所有醜陋製作驗證訊息、 驗證權杖從 Azure AD，及維護使用者工作階段的詳細資料。  剩下的就是為您的使用者登入與登出，收集在登入的使用者的其他資訊的方法。

- 首先，可讓新增至預設、 登入、 帳戶及登出方法我們`app.js`檔案︰

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   讓我們來檢視的詳細資料︰
    -   `/`路由會重新導向至 index.ejs 檢視傳送邀請中的使用者 （若存在）
    - `/account`路由將第一個***確保我們會驗證***(我們實作的下方)，然後將在邀請中的使用者，讓我們可以取得其他使用者的相關資訊。
    - `/login`傳送者將撥號從我們 azuread openidconnect 驗證`passport-azuread`，如果的並不成功會將使用者導向至 /login
    - `/logout`會只要來電 logout.ejs （和路由） 的清除 cookie，然後返回使用者回 index.ejs


- 最後一個部分`app.js`，讓我們來加入 EnsureAuthenticated 方法所使用的`/account`上方。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- 最後，讓我們來實際建立伺服器本身中`app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5。 中建立的檢視和路由快速] 可顯示在網站中的使用者

我們還有我們`app.js`完成。 現在我們只需要新增路由和會顯示使用者以及控點到資訊的檢視`/logout`和`/login`路由我們建立了。

- 建立`/routes/index.js`下的根目錄的路徑。

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 建立`/routes/user.js`底下的根目錄的路徑

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

這些簡單路由會只是為了沿著要求我們的檢視，包括使用者，如果有的話。

- 建立`/views/index.ejs`檢視] 下的根目錄。 這是簡單的頁面會呼叫我們登入和登出方法，讓我們抓取帳戶資訊。 請注意，我們可以使用條件`if (!user)`使用者通過在邀請中是證據我們有使用者登入。

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- 建立`/views/account.ejs`檢視下的根目錄，因此我們可以檢視其他資訊的`passport-azuread`具有保留在使用者邀請。

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- 最後，讓我們來看起來很增加讓版面配置。 建立 「 / 根目錄] 下的 views/layout.ejs 的檢視

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/account">Account</a> | 
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

最後，建立並執行您的應用程式 ！ 

執行`node app.js`並瀏覽至`http://localhost:3000`


使用個人 Microsoft 帳戶或公司或學校帳戶登入，並注意如何 /account 清單中反映使用者的身分識別。  現在，您可以使用其個人及工作/學校的帳戶的使用者驗證的業界標準通訊協定保護的 web 應用程式。

參照的已完成的範例 （不含您設定的值） [，提供為的.zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip)，或者您可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


您現在可以移至 [更多進階的主題。  若要嘗試︰

[安全的 Web 與 Azure AD API >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
