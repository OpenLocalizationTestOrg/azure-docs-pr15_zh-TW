<properties
    pageTitle="Azure AD v2.0 NodeJS 網頁 API |Microsoft Azure"
    description="如何建立 NodeJS Web API 接受權杖從兩個個人的 Microsoft 帳戶與公司或學校帳戶。"
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
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="secure-a-web-api-using-nodejs"></a>安全使用 node.js Web API

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

Azure Active Directory v2.0 端點，您可以保護網頁的 API 使用[OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow)存取權杖，讓使用者同時使用個人 Microsoft 帳戶與工作或學校帳戶安全地存取您網站的 API。

**密碼**會驗證介軟體的 Node.js。 非常有彈性和模組化、 密碼小心卸除任何快速計算或 Resitify web 應用程式。 完整的策略支援驗證使用的使用者名稱和密碼、 Facebook、 Twitter 等。 我們適用於 Microsoft Azure Active Directory 開發策略。 我們會安裝此模組，然後將 [Microsoft Azure Active Directory`passport-azure-ad`外掛程式。

## <a name="download"></a>下載
本教學課程中的程式碼是來維護[GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs)。  若要遵循，您可以[下載應用程式的基本架構為.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip)或複製基本架構︰

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

完成的應用程式提供在本教學課程結尾處。


## <a name="1-register-an-app"></a>1.註冊應用程式
在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，建立新的應用程式，或依照這些[詳細的步驟](active-directory-v2-app-registration.md)。  請確定︰

- 複製下指派給您的應用程式的**應用程式識別碼**，您必須將其推出。
- 新增您的應用程式的**行動**平台。
- **重新導向 URI**下複製入口網站。 您必須使用的預設值`urn:ietf:wg:oauth:2.0:oob`。


## <a name="2-download-nodejs-for-your-platform"></a>2︰ 下載您的平台版的 node.js
若要順利使用這個範例，您必須 Node.js 的工作安裝。

從[http://nodejs.org](http://nodejs.org)安裝 Node.js。

## <a name="3-install-mongodb-on-to-your-platform"></a>3︰ 執行您的平台安裝 MongoDB

若要順利使用這個範例，您必須 MongoDB 的工作安裝。 我們會使用 MongoDB，讓我們 REST API persistant 所有伺服器執行個體。

從[http://mongodb.org](http://www.mongodb.org)安裝 MongoDB。

> [AZURE.NOTE] 這個逐步解說假設 MongoDB，即在此撰寫用於預設安裝和 server 端點︰ mongodb://localhost

## <a name="4-install-the-restify-modules-in-to-your-web-api"></a>4︰ 安裝您網站的 API 中的 Restify 模組

我們將會建立我們 REST API 使用 Resitfy。 Restify 最小和彈性 Node.js 應用程式架構衍生自 Express 含有功能強大的建置 REST Api 連線上方的功能集。

### <a name="install-restify"></a>安裝 Restify

從命令列，請 azuread 目錄變更的目錄。 如果沒有**azuread**目錄，建立它。

`cd azuread`-或者-`mkdir azuread;`

輸入下列命令︰

`npm install restify`

這個命令會安裝 Restify。

#### <a name="did-you-get-an-error"></a>時發生錯誤？

在某些作業系統上使用 npm，您可能會收到錯誤的錯誤︰ EPERM，chmod ' / usr/本機/bin /...」 請以系統管理員身分執行帳戶的要求。 如果這種情況，使用 [sudo] 命令來執行 npm 在較高的權限等級。

#### <a name="did-you-get-an-error-regarding-dtrace"></a>您並未取得關於 DTrace 錯誤？

安裝 Restify 時，您可能會看到像這樣︰

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


Restify 提供追蹤使用 DTrace 其餘通話的強大機制。 不過，許多作業系統不需要 DTrace 可用。 您可以忽略這些錯誤。


這個命令的輸出應如下︰


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
    └── bunyan@0.22.0(mv@0.0.5)


## <a name="5-install-passportjs-into-your-web-api"></a>5︰ 將您的網路 API 安裝 Passport.js

[密碼](http://passportjs.org/)會驗證介軟體的 Node.js。 非常有彈性和模組化、 密碼小心卸除任何快速計算或 Resitify web 應用程式。 完整的策略支援驗證使用的使用者名稱和密碼、 Facebook、 Twitter 等。 我們的 Azure Active Directory 開發策略。 我們會安裝此模組，然後再新增外掛程式的 Azure Active Directory 策略。

從命令列，請 azuread 目錄變更的目錄。

輸入下列命令以安裝 passport.js

`npm install passport`

命令的輸出應如下︰

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6︰ 將密碼 Azure AD 新增至您的網路 API

接下來，我們會將 OAuth 策略中，使用密碼 azuread，聯繫密碼的 Azure Active Directory 的策略套件。 我們會使用這個策略承載者權杖，在這個範例中 Rest API。

> [AZURE.NOTE] 雖然 OAuth2 提供已知的權杖類型可以發出的架構，只是某些 token 類型得到普遍使用。 保護已取出，承載者權杖的端點。 承載者權杖是最廣泛發行中 OAuth2，權杖類型和許多實作假設承載者權杖都是唯一的發出權杖類型。

從命令列，請變更目錄 azuread 目錄

輸入下列命令以安裝 Passport.js 密碼 azure ad 模組︰

`npm install passport-azure-ad`

命令的輸出應如下︰

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

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7︰ 將 MongoDB 模組新增至您網站的 API

我們會使用 MongoDB 為我們的資料存放區的原因，我們需要安裝兩個常用外掛程式管理模型與結構描述稱為的 1，以及 MongoDB 的資料庫驅動程式也稱為 MongoDB。


* `npm install mongoose`
* `npm install mongodb`

## <a name="8-install-additional-modules"></a>8︰ 安裝其他模組

接下來，我們將會安裝所需的剩餘模組。


從命令列，變更目錄的**azuread**資料夾如果不是已那里所示︰

`cd azuread`


輸入下列命令以安裝下列模組 node_modules 目錄中︰

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## <a name="9-create-a-serverjs-with-your-dependencies"></a>9︰ 建立 server.js 與相依性

Server.js 檔案會提供我們 Web API 伺服器大部分的我們功能。 將我們，大部分的程式碼加入此檔案。 生產您想要重構中較小的檔案，例如不同的路徑和控制站的功能。 針對此示範我們將使用 server.js 這項功能。

從命令列，變更目錄的**azuread**資料夾如果不是已那里所示︰

`cd azuread`

建立`server.js`我們最愛編輯器] 中 [檔案] 和 [新增下列資訊︰

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

儲存檔案。 我們將會回到它引進了。

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10︰ 建立以儲存您的 Azure AD 設定設定檔

此程式碼檔案會從您 Azure Active Directory 入口網站中設定參數傳遞給 Passport.js。 當您加入網頁 API 的逐步解說第一部分的入口網站，您可以建立下列設定值。 我們會說明內容的參數值之後您已複製程式碼。


從命令列，變更目錄的**azuread**資料夾如果不是已那里所示︰

`cd azuread`

建立`config.js`我們最愛編輯器] 中 [檔案] 和 [新增下列資訊︰

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### <a name="required-values"></a>必要的值

*IdentityMetadata*︰ 這是您的設定資料 IdP，以及按鍵以驗證 JWT 權杖何處尋找密碼 azure ad。 您可能不想要變更如果使用的 Azure Active Directory。

*對象*︰ 導向 URI 從入口網站。

> [AZURE.NOTE]
我們頻繁就緒我們鍵。 請確定您永遠抽取從 「 openid_keys 」 的 URL，應用程式，能存取網際網路。


## <a name="11-add-configuration-to-your-serverjs-file"></a>11︰ 新增至 server.js 檔案的設定

我們需要讀取您剛建立跨我們的應用程式的設定檔中的下列值。 若要這麼做，我們中，新增即可.config 檔案所需的資源為我們的應用程式，然後設定這些 config.js 文件中的 [全域變數

從命令列，變更目錄的**azuread**資料夾如果不是已那里所示︰

`cd azuread`

開啟您`server.js`我們最愛編輯器] 中 [檔案] 和 [新增下列資訊︰

```Javascript
var config = require('./config');
```
然後，新增至新的一節`server.js`下列程式碼︰

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="step-12-add-the-mongodb-model-and-schema-information-using-moongoose"></a>步驟 12︰ 新增 MongoDB 模型和使用 Moongoose 的結構描述資訊

現在所有準備都要開始付清我們風力這三個檔案放在一起的 REST API 服務。

在此逐步解說我們會使用 MongoDB 儲存我們的工作，在***步驟 4***中所述。

如果您還記得我們建立步驟 11 config.js 檔案，我們呼叫我們資料庫*工作清單*，是我們將我們 mogoose_auth_local 連線 URL 的結尾。 您不需要事先 MongoDB 中建立此資料庫，它會建立此為我們的伺服器應用程式 （假設不存在） 的第一次執行。

現在，我們已告訴我們想要使用哪些 MongoDB 資料庫伺服器，我們需要撰寫建立模型和結構描述，我們的伺服器工作的一些其他程式碼。

#### <a name="discussion-of-the-model"></a>模型的討論區

我們的結構描述模型很簡單，並將其展開所需。

名稱-誰分派給任務的名稱。 ***字串***

工作-任務本身。 ***字串***

日期-工作到期的日期。 ***日期時間***

-如果任務已完成或未完成。 ***布林值***

#### <a name="creating-the-schema-in-the-code"></a>在 [程式碼中建立結構描述


從命令列，變更目錄的**azuread**資料夾如果不是已那里所示︰

`cd azuread`

開啟您`server.js`我們最愛編輯器] 中 [檔案] 和 [新增設定項目] 下方的下列資訊︰

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
將連接到 MongoDB 伺服器並交回結構描述物件給我們。

#### <a name="using-the-schema-create-our-model-in-the-code"></a>使用結構描述建立我們的模型中的程式碼

程式碼，您所撰寫的上方、 下方新增下列程式碼︰

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
當您得知自己的程式碼，我們建立結構描述，然後建立我們會使用我們定義我們***路由***儲存在整個資料模型物件。

## <a name="step-13-add-our-routes-for-our-task-rest-api-server"></a>步驟 13︰ 新增為我們的任務 REST API 伺服器我們路由

現在，我們已使用的資料庫模型，讓我們來加入我們會使用我們 REST API 伺服器路由。

### <a name="about-routes-in-restify"></a>有關路由中 Restify

而在 Restify 他們執行使用 Express 堆疊完全相同的方式。 您定義路由使用 URI 預期呼叫的用戶端應用程式。 通常您會在另一個檔案中定義您路由。 我們的目的，我們會將我們路線放 server.js 檔案中。 我們建議您納入這些自己生產使用的檔案。

一般 Restify 路由模式是︰

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


這是最基本的層級的模式。 Resitfy （和 Express） 提供更深入 functionaltiy 定義應用程式類型等執行複雜路由跨不同的結束點。 針對我們的目的，我們會在 [保留這些路由非常簡單。

#### <a name="add-default-routes-to-our-server"></a>將預設路由加入我們的伺服器

現在，我們會新增基本 CRUD 路由的 [建立]，擷取，更新，並刪除。

從命令列，變更目錄的**azuread**資料夾如果不是已那里所示︰

`cd azuread`

開啟您`server.js`我們最愛編輯器] 中 [檔案] 和 [新增上面您所做的資料庫項目] 下方的下列資訊︰

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
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
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
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
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

### <a name="add-some-error-handling-for-the-routes"></a>新增一些錯誤處理路由

讓新增一些錯誤，處理，我們可以用戶端通訊方式我們發生的問題，讓它可以瞭解。

新增下列程式碼下方您寫上方的程式碼︰

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


## <a name="step-14-create-your-server"></a>步驟 14︰ 建立您的伺服器 ！

我們有定義我們資料庫、 還有我們路線的位置，以及最後一個就是新增將會管理我們來電我們伺服器執行個體。

Restify （和 Express） 有許多深自訂，您可以執行的 REST API 伺服器，，但我們會在一次用於我們使用最基本的設定。

```Javascript
/**
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
}));
```
## <a name="15-adding-the-routes-without-authentication-for-now"></a>15︰ 新增路由 （不含現在驗證）

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-before-we-add-oauth-support-lets-run-the-server"></a>16︰ 我們新增 OAuth 支援之前，請讓我們來執行伺服器。

測試您的伺服器我們新增驗證之前

若要執行此動作的最簡單方法是使用捲曲命令列中。 這樣做之前，我們需要讓我們來剖析 json 輸出的簡易公用程式。 若要執行這項作業，請安裝 json 工具，當所有下面的範例使用的。

`$npm install -g jsontool`

這是全域安裝 JSON 工具。 現在，我們已完成的 – 讓我們來播放與伺服器︰

首先，請確定您 monogoDB isntance 正在執行。

`$sudo mongod`

然後，變更的目錄，並開始 curling.

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

然後，我們可以新增工作這種方式︰

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

應該回應︰

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
我們可以列出工作 Brandon 這種方式︰

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

如果所有運作，我們已準備好新增 OAuth REST API 伺服器。

**您有使用 MongoDB REST API 伺服器 ！**

## <a name="17-add-authentication-to-our-rest-api-server"></a>17︰ 新增為我們的 REST API 伺服器驗證

現在，我們已執行的 REST API (備用，btw ！) 若要讓您針對 Azure AD 實用就讓我們開始。

從命令列，變更目錄的**azuread**資料夾如果不是已那里所示︰

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1︰ 使用隨附於密碼 azure ad oidcbearerstrategy

目前我們已經建立一般的其餘部分 TODO 伺服器沒有任何種類的授權。 這是我們啟動將會放在一起的位置。

首先，我們需要表示我們要使用密碼。 將此權限之後您其他的伺服器設定︰

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
撰寫 Api 時您應該一律連結資料至唯一的項目從使用者無法詐騙的 token。 當此伺服器會將儲存 TODO 項目時，它會儲存根據使用者在權杖 （稱為透過 token.sub） 我們將在 「 擁有者] 欄位中的訂閱 ID。 這可確保只有該使用者可以存取其待辦並沒有任何人可以存取輸入待辦。 讓外部使用者可以要求其他人待辦，即使驗證的 「 擁有者 」 的 API 中沒有揭露。

接下來，我們要使用隨附密碼 azure ad 開啟識別碼連線承載者策略。 只要查看目前的程式碼，我會解釋它。 將它放在您的項目之後 pated 上方︰

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

密碼會是策略 （Twitter、 Facebook 等） 的所有策略作者都遵守所有使用類似的模式。 您看到我們傳遞具有權杖，完成的參數為 function() 位於策略。 策略會盡責回來我們之後會所有的工作。 後會我們會想要儲存使用者及藏匿權杖，因此我們不需要再次要求其。

> [AZURE.IMPORTANT]
上述的程式碼會為我們的伺服器驗證發生任何使用者。 這就是自動註冊。 生產伺服器中，您不想要讓任何人不必第一個他們可幫助您決定註冊程序。 這通常是您在 [可以讓您與 Facebook 註冊，但再詢問您要填入其他資訊的消費者應用程式中看到的模式。 如果這不是命令列程式，我們可能有只擷取電子郵件從權杖物件傳回，然後要求他們填入其他資訊。 這是測試伺服器因為我們只將其新增至記憶體內資料庫。

### <a name="2-finally-protect-some-endpoints"></a>2.最後，保護一些端點

您可以指定您想要使用的通訊協定 passport.authenticate() 通話保護端點。

讓我們來編輯我們傳送我們進行操作變得更有趣的伺服器程式碼中︰

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again-and-ensure-it-rejects-you"></a>18︰ 執行一次伺服器應用程式，並確保其拒絕您

讓我們來使用`curl`即可查看我們現在是否 OAuth2 保護針對我們的結束點。 我們會執行此動作之前在執行中任何用戶端 Sdk 針對此端點。 傳回的標頭應該可以告訴我們我們是正確的路徑。

首先，請確定您 monogoDB isntance 正在執行。

    $sudo mongod

然後，變更的目錄，並開始 curling.

    $ cd azuread
    $ node server.js

請嘗試基本的文章︰

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401，表示在 [圖層密碼嘗試重新導向至授權的端點，而這正是您想要為您在這裡，尋找的回應。


## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>恭喜您 ！ 您有使用 OAuth2 REST API 服務 ！

您已發生時不使用 OAuth2 相容的用戶端可以使用此伺服器。 您需要進行額外的逐步解說。

如果您只尋找如何實作使用 Restify 與 OAuth2 REST API 的詳細資訊，請您有多個足夠的程式碼，若要保留開發您的服務，學習如何建立在此範例。

## <a name="next-steps"></a>後續步驟

參照的已完成的範例 （不含您設定的值） [，提供為的.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)，或者您可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

您現在可以移動到更進階的主題。  若要嘗試︰

[安全 Node.js web app 使用 v2.0 端點 >>](active-directory-v2-devquickstarts-node-web.md)

其他資源，請參閱︰
- [V2.0 開發人員指南 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow 「 azure-主動-目錄 」 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們建議您收到通知，造訪[本頁面](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
