<properties
    pageTitle="Azure AD B2C︰ 使用 Node.js 安全網路 API |Microsoft Azure"
    description="如何建立 Node.js web API 可接受的 B2C 租用戶的權杖"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/30/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C︰ 使用 Node.js 安全網路 API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

與 Azure Active Directory (Azure AD) B2C，您可以使用 OAuth 2.0 存取權杖保護網路 API。 這些權杖可讓您使用 Azure AD B2C 驗證對 API 的用戶端應用程式。 本文將示範如何建立一個可讓使用者新增，並列出工作的 「 待辦事項清單 」 API。 網路 API 使用 Azure AD B2C 受到保護，只允許已驗證的使用者管理其待辦事項清單。

> [AZURE.NOTE]  此範例使用我們[iOS B2C 範例應用程式](active-directory-b2c-devquickstarts-ios.md)連線到撰寫。 首先，請執行目前的逐步解說，並依照以及的範例。

**密碼**會驗證介軟體的 Node.js。 彈性和模組化，密碼可以小心安裝在任何 Express 型或 Restify web 應用程式。 完整的策略支援驗證使用的使用者名稱和密碼、 Facebook、 Twitter 等。 我們的 Azure Active Directory (Azure AD) 開發策略。 您安裝此模組，然後再新增 Azure AD`passport-azure-ad`外掛程式。

若要執行這個範例中，您需要︰

1. 註冊 Azure AD 應用程式。
2. 設定您的應用程式，以使用密碼的`azure-ad-passport`外掛程式。
3. 若要打電話給 「 待辦事項清單 」 網路 API 用戶端應用程式設定。


## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄

您可以使用 Azure AD B2C 之前，您必須建立目錄，或租用戶。  目錄是所有使用者、 應用程式、 群組及更多的容器。  如果沒有帳戶，請[建立 B2C 目錄](active-directory-b2c-get-started.md)之前繼續。

## <a name="create-an-application"></a>建立應用程式

接下來，您需要建立 B2C 目錄提供 Azure AD 部分資訊，以便安全地與您的應用程式中的應用程式。 在此情況下，將用戶端應用程式與網路 API 會以單一**應用程式識別碼**，因為它們組成一個邏輯的應用程式。 若要建立應用程式，請遵循[下列指示進行](active-directory-b2c-app-registration.md)。 請務必︰

- 包含**web 應用程式/網頁 api**應用程式中
- 輸入`http://localhost/TodoListService`為**回覆 URL**。 則此程式碼範例的預設 URL。
- 建立應用程式的**應用程式密碼**並加以複製。 您可以稍後需要此資料。 請注意此值必須是您使用[XML 逸出字元](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)。
- 複製**應用程式識別碼**指派給您的應用程式。 您可以稍後需要此資料。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則

Azure AD B2C，在每個使用者體驗定義[原則](active-directory-b2c-reference-policies.md)。 此應用程式包含兩個身分識別體驗︰ 登入並登入。 您需要建立的每個類型，一個原則[原則參考本文](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。  當您建立您的三個原則時，請務必︰

- 在您註冊的原則，選擇**顯示名稱**] 和 [註冊的其他屬性。
- 選擇 [**顯示名稱**] 和 [**物件識別碼**的應用程式宣告，每個原則。  您可以選擇以及其他宣告。
- 建立後，請複製下的每個原則**名稱**。 應有前置詞`b2c_1_`。  您可以稍後需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則之後，您可以準備開始建立您的應用程式。

若要深入瞭解原則 Azure AD B2C 中的運作方式，請從[.NET web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="download-the-code"></a>下載的程式碼

此教學課程[GitHub 上維護](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS)的程式碼。 若要建置範例，您即可，您可以[下載基本架構專案另存為.zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip)。 您也可以複製基本架構︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

已完成的應用程式也是[使用 zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip)上或`complete`分支的相同存放庫。

## <a name="download-nodejs-for-your-platform"></a>下載 Node.js 適用於您的平台

若要順利使用這個範例，您需要 Node.js 的工作安裝。 

從[nodejs.org](http://nodejs.org)安裝 Node.js。

## <a name="install-mongodb-for-your-platform"></a>為您的平台安裝 MongoDB

若要順利使用這個範例，您需要 MongoDB 的工作安裝。 我們使用 MongoDB 您 REST API 常設所有伺服器執行個體。

從[mongodb.org](http://www.mongodb.org)安裝 MongoDB。

> [AZURE.NOTE] 此逐步解說假設預設安裝和 server 端點用於 MongoDB，即在此撰寫`mongodb://localhost`。

## <a name="install-the-restify-modules-in-your-web-api"></a>在您的網路 API 安裝 Restify 模組

我們來建立您的 REST API 使用 Restify。 Restify 衍生自 Express 的最小和彈性 Node.js 應用程式架構。 有功能來建立連線上方的 REST Api 的組。

### <a name="install-restify"></a>安裝 Restify

從命令列中，變更目錄以`azuread`。 如果`azuread`目錄不存在，建立。

`cd azuread`或`mkdir azuread;`

輸入下列命令︰

`npm install restify`

這個命令會安裝 Restify。

#### <a name="did-you-get-an-error"></a>時發生錯誤？

在某些作業系統中，當您使用`npm`，您可能會收到錯誤`Error: EPERM, chmod '/usr/local/bin/..'`和系統管理員身分執行帳戶的要求。 如果發生此問題，請使用`sudo`命令，以執行`npm`在較高的權限等級。

#### <a name="did-you-get-a-dtrace-error"></a>您是否收到 DTrace 錯誤？

當您安裝 Restify 時，您可能會看到類似此文字︰

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify 提供使用 DTrace 呼叫追蹤其餘的強大機制。 不過，許多作業系統不需要 DTrace 可用。 您可以忽略這些錯誤。

輸出的命令看起來應該類似此文字︰

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>安裝在您的網路 API 的密碼

從命令列中，變更目錄以`azuread`，如果找不到已。

安裝密碼使用下列命令︰

`npm install passport`

命令的輸出應該類似此文字︰

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>將密碼 azuread 新增至您的網路 API

接下來，藉由新增 OAuth 策略`passport-azuread`，Azure AD 聯繫密碼的策略套件。 使用這個策略 REST API 樣本中的承載者權杖。

> [AZURE.NOTE] 雖然 OAuth2 提供已知的權杖類型可以發出的架構，只是某些 token 類型得到廣泛使用。 保護結束點的權杖是承載者權杖。 這些類型的權杖是最廣發行中 OAuth2。 許多實作假設承載者權杖都是唯一的發出權杖類型。

從命令列中，變更目錄以`azuread`，如果找不到已。

安裝密碼`passport-azure-ad`模組使用下列命令︰

`npm install passport-azure-ad`

命令的輸出應該類似此文字︰

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>將 MongoDB 模組新增至您的網路 API

這個範例使用 MongoDB 為您的資料儲存區。 安裝 1，常用的外掛程式]，管理模型與結構描述。

* `npm install mongoose`

## <a name="install-additional-modules"></a>安裝的其他模組

接下來，請安裝所需的剩餘模組。

從命令列中，變更目錄以`azuread`，如果找不到已經︰

`cd azuread`

安裝模組中的您`node_modules`目錄︰

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`


## <a name="create-a-serverjs-file-with-your-dependencies"></a>建立 server.js 檔案與相依性

`server.js`檔案提供 Web API 伺服器大部分的功能。 

從命令列中，變更目錄以`azuread`，如果找不到已經︰

`cd azuread`

建立`server.js`編輯器中的檔案。 新增下列資訊︰

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

儲存檔案。 您於稍後返回。

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>建立 config.js 檔案以儲存您的 Azure AD 設定

此程式碼檔案從您的 Azure AD 入口網站，傳送設定參數`Passport.js`檔案。 當您加入網路 API 的逐步解說第一部分的入口網站，您可以建立下列設定值。 我們會說明內容的參數值之後複製程式碼。

從命令列中，變更目錄以`azuread`，如果找不到已經︰

`cd azuread`

建立`config.js`編輯器中的檔案。 新增下列資訊︰

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>必要的值

`clientID`︰ Web API 應用程式用戶端識別碼。

`IdentityMetadata`︰ 這是位置`passport-azure-ad`身分識別提供者為您設定的資料看起來。 它也會尋找按鍵驗證 JSON web 權杖。 

`audience`: 統一資源識別元 (URI) 從入口網站，識別您的應用程式。 

`tenantName`︰ 您的租用戶的名稱 (例如**contoso.onmicrosoft.com**)。

`policyName`︰ 您要驗證的權杖寄至您的伺服器原則。 此原則應為相同的原則所使用的用戶端應用程式登入。

> [AZURE.NOTE] 我們 B2C 預覽，請使用相同的原則，在用戶端與伺服器的設定。 如果您已經完成逐步解說，並建立這些原則，您不需要再次執行此作業。 因為您已完成的逐步解說，您應該不需要在網站上設定的用戶端逐步解說新原則。

## <a name="add-configuration-to-your-serverjs-file"></a>新增至 server.js 檔案的設定

若要閱讀的值`config.js`所建立的檔案，請新增`.config`為應用程式中所需的資源檔案，然後再設為中的 [全域變數`config.js`文件。

從命令列中，變更目錄以`azuread`，如果找不到已經︰

`cd azuread`

開啟`server.js`編輯器中的檔案。 新增下列資訊︰

```Javascript
var config = require('./config');
```
新增節至`server.js`包含下列程式碼︰

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

接下來，讓我們來新增一些版面配置區，我們會收到來自我們呼叫的應用程式的使用者。

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

現在讓我們並建立我們記錄器太。

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>使用 1 新增 MongoDB 模型和結構描述資訊

舊版的準備支付，當您將這些一起 REST API 服務中的三個檔案。

逐步解說，使用 MongoDB 儲存您的工作，先前所述。

在 [`config.js`檔案，稱為您的資料庫**工作清單**。 該名稱也是您放置在結尾`mongoose_auth_local`連線 URL。 您不需要事先 MongoDB 中建立此資料庫。 在第一個執行您的伺服器應用程式為您建立資料庫。

判斷要使用哪個 MongoDB 資料庫伺服器後，您必須撰寫來建立模型和結構描述您的伺服器工作的一些其他程式碼。

### <a name="expand-the-model"></a>展開模型

此結構描述模型很簡單。 您可以視需要將其展開。

`owner`︰ 誰被分派給任務。 這是**字串**。  

`Text`︰ 工作本身。 這是**字串**。

`date`︰ 工作到期的日期。 這個物件是**日期時間**。

`completed`︰ 如果任務已完成。 這個物件是**布林值**。

### <a name="create-the-schema-in-the-code"></a>在 [程式碼中建立結構描述

從命令列中，變更目錄以`azuread`，如果找不到已經︰

`cd azuread`

開啟`server.js`編輯器中的檔案。 新增設定項目] 下方的下列資訊︰

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
您第一次建立結構描述，然後再您建立您用來在定義您**路由**時儲存在整個資料模型物件。

## <a name="add-routes-for-your-rest-api-task-server"></a>新增您的 REST API 工作伺服器路由

現在，您有使用的資料庫模型，請新增您使用的 REST API 伺服器路由。

### <a name="about-routes-in-restify"></a>關於路由 Restify 中

而在 Restify 中使用快速堆疊時，其運作方式相同。 您可以使用 URI 預期要收到呼叫的用戶端應用程式的定義路由。 

一般的 Restify 路由模式是︰

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify 並快速提供多更深入的功能，例如定義應用程式類型和執行複雜路由跨不同的結束點。 在進行本教學課程中，我們將這些路由簡單。

#### <a name="add-default-routes-to-your-server"></a>新增預設路由至您的伺服器

您現在我們 REST API 新增基本 CRUD 而**建立**和**清單**。 其他路由，請參閱`complete`分支的範例。

從命令列中，變更目錄以`azuread`，如果找不到已經︰

`cd azuread`

開啟`server.js`編輯器中的檔案。 以下上方您所做的資料庫項目新增下列資訊︰

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>新增路由處理的錯誤

新增一些錯誤，處理，讓您能以無聲發生任何問題的方式，就可以了解用戶端。

新增下列程式碼︰

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>建立您的伺服器

您現在有定義您的資料庫，而且您路由置於位置。 您可以執行的最後一個項目是新增管理來電的伺服器執行個體。

Restify 及 Express 提供深自訂 REST API 伺服器，但我們以下使用最基本的設定。 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>新增路由至伺服器 （不含驗證）

```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>新增您的 REST API 伺服器驗證

您已經有執行 REST API 伺服器，讓它可以針對 Azure AD 實用。

從命令列中，變更目錄以`azuread`，如果找不到已經︰

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>使用隨附於密碼 azure ad OIDCBearerStrategy


> [AZURE.TIP]
當您撰寫 Api 時，您應該一律連結資料至唯一的項目從使用者無法詐騙的 token。 當伺服器儲存 ToDo 的項目時，它會根據**oid** （稱為透過 token.oid） 的權杖，使用者的進入 「 擁有者] 欄位中，因此。 此值，可確保只有該使用者可以存取自己 ToDo 項目。 讓外部使用者可以邀請其他人的 ToDo 項目，即使驗證的 「 擁有者 」 的 API 中沒有曝光度。

接下來，請使用 [隨附承載者策略`passport-azure-ad`。

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

密碼會使用其所有的策略相同的模式。 您將傳遞`function()`具有`token`和`done`做為參數。 策略恢復給您後，已完成所有的工作。 然後，您應該儲存使用者並儲存權杖，以便您不需要再次要求其。

> [AZURE.IMPORTANT]
上述的程式碼會驗證您的伺服器，會發生情況的任何人。 此程序稱為 autoregistration。 在生產伺服器，不讓任何使用者存取權的 API 不必第一個進行註冊程序瀏覽。 此程序通常是您在可讓您可以使用 Facebook 註冊，但再詢問您要填入其他資訊的消費者應用程式中看到的模式。 如果此程式不是命令列的程式，我們無法從權杖物件傳回，然後要求使用者填寫的其他資訊擷取有電子郵件。 這是樣本，因為我們將其新增至記憶體內資料庫。



## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>執行您確認其拒絕您的伺服器應用程式

您可以使用`curl`若要查看您現在是否 OAuth2 保護針對您結束點。 傳回的標頭應該可以告訴您的是正確的路徑。

請確定您 MongoDB 執行個體正在執行︰

    $sudo mongodb

變更目錄，並執行伺服器︰

    $ cd azuread
    $ node server.js

在新的終端機視窗中，執行`curl`

請嘗試基本的文章︰

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401 錯誤是您想要的回應。 表示密碼圖層嘗試重新導向至授權結束點。


## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>現在您已經有使用 OAuth2 REST API 服務

您已使用 Restify 和 OAuth 實作 REST API ！ 您現在有足夠的程式碼，以便您可以繼續開發您的服務，並在此範例中所建立。 您已經為您可以使用此伺服器，而不使用 OAuth2 相容的用戶端。 針對的下一個步驟使用其他的逐步解說，例如我們[連線到 web API 使用 iOS 與 B2C](active-directory-b2c-devquickstarts-ios.md)逐步解說。


## <a name="next-steps"></a>後續步驟

您可以立即移至更進階的主題，例如︰

[連線至網路 API 使用 B2C iOS](active-directory-b2c-devquickstarts-ios.md)