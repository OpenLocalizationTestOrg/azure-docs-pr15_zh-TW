<properties
   pageTitle="發佈 WebApplicationWebSite （Windows PowerShell 指令碼） |Microsoft Azure"
   description="瞭解如何將 web 專案發佈至 Azure 的網站。 這個指令碼 Azure 訂閱中建立所需的資源，如果不存在。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>發佈 WebApplicationWebSite （Windows PowerShell 指令碼）

##<a name="syntax"></a>語法

將 web 專案發佈至 Azure 的網站。 指令碼 Azure 訂閱中建立所需的資源，如果不存在。

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>設定

說明部署的詳細資料的 JSON 設定檔案路徑。

|參數|預設值|
|---|---|
|別名|無|
|需要？|true|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

## <a name="subscriptionname"></a>SubscriptionName

您想要建立的網站中的 Azure 訂閱名稱。

|參數|預設值|
|---|---|
|別名|無|
|需要？|false|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

## <a name="webdeploypackage"></a>WebDeployPackage

要發佈至網站網頁部署套件的路徑。 您可以使用 [發佈精靈在 Visual Studio 中建立此套件。 如需詳細資訊，請參閱[Azure 雲端服務與 ASP.NET 快速入門](http://go.microsoft.com/fwlink/p/?LinkID=623089)。

|參數|預設值|
|---|---|
|別名|無|
|需要？|false|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

使用者名稱和密碼 Azure SQL 資料庫。

|參數|預設值|
|---|---|
|別名|無|
|需要？|false|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

如果為 true，列印郵件從指令碼輸出資料流。

|參數|預設值|
|---|---|
|別名|無|
|需要？|false|
|位置|名為|
|預設值|false|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

## <a name="remarks"></a>註解

如需如何建立使用指令碼的完整說明開發和測試環境中，請參閱[使用 Windows PowerShell 指令碼發佈至開發和測試環境](vs-azure-tools-publishing-using-powershell-scripts.md)。

JSON 設定檔中指定部署的詳細的資料。 其包含您指定當您建立專案，例如 [名稱] 和 [使用者名稱的網站的資訊。 如果有任何也包含佈建，資料庫。 下列程式碼會顯示的範例 JSON 設定檔︰

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

您可以編輯變更內容部署 JSON 設定檔。 [網站] 區段，但是選擇性的 [資料庫] 區段。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[發佈 WebApplicationVM （Windows PowerShell 指令碼）](vs-azure-tools-publish-webapplicationvm.md)
