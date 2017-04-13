<properties
   pageTitle="發佈 WebApplicationVM |Microsoft Azure"
   description="瞭解如何部署虛擬機器的 web 應用程式。 這個指令碼 Azure 訂閱中建立所需的資源，如果不存在。"
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

# <a name="publish-webapplicationvm-windows-powershell-script"></a>發佈 WebApplicationVM （Windows PowerShell 指令碼）

部署虛擬機器的 web 應用程式。 指令碼 Azure 訂閱中建立所需的資源，如果不存在。

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>設定

說明部署的詳細資料的 JSON 設定檔案路徑。

|別名|無|
|---|---|
|需要？|true|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

### <a name="subscriptionname"></a>SubscriptionName

您要在其中建立虛擬機器 Azure 訂閱的名稱。

|別名|無|
|---|---|
|需要？|false|
|位置|名為|
|預設值|在訂閱的檔案會使用第一個訂閱|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

### <a name="webdeploypackage"></a>WebDeployPackage

要發佈至虛擬機器網頁部署套件的路徑。 您可以使用 [發佈精靈在 Visual Studio 中建立此套件。 請參閱[如何︰ 在 Visual Studio 建立 Web 部署套件](https://msdn.microsoft.com/library/dd465323.aspx)。

|別名|無|
|---|---|
|需要？|false|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

### <a name="allowuntrusted"></a>AllowUntrusted

如果為 true，允許使用由信任的根授權未簽署的憑證。

|別名|無|
|---|---|
|需要？|false|
|位置|名為|
|預設值|false|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

### <a name="vmpassword"></a>VMPassword

虛擬機器帳戶認證。 範例︰ VMPassword @{Name = 「 系統 」;密碼 = 「 密碼 」}

|別名|無|
|---|---|
|需要？|false|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

Azure SQL 資料庫的認證。 範例︰ DatabaseServerPassword @{Name = 「 系統 」;密碼 = 「 密碼 」}

|別名|無|
|---|---|
|需要？|false|
|位置|名為|
|預設值|無|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

如果為 true，列印郵件從指令碼輸出資料流。

|別名|無|
|---|---|
|需要？|false|
|位置|名為|
|預設值|false|
|接受管線輸入嗎？|false|
|接受萬用字元嗎？|false|

## <a name="remarks"></a>註解

如需如何建立使用指令碼的完整說明開發和測試環境中，請參閱[使用 Windows PowerShell 指令碼發佈至開發和測試環境](vs-azure-tools-publishing-using-powershell-scripts.md)。

JSON 設定檔中指定部署的詳細的資料。 其包含的資訊，您指定在您建立專案，例如名稱、 相關性群組、 VHD 圖像和虛擬機器的大小。 也包含虛擬機器，佈建，資料庫上的端點，如果有的話，網頁部署參數。 下列程式碼會顯示的範例 JSON 設定檔︰

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

您可以編輯變更什麼佈建 JSON 設定檔。 虛擬機器和雲端服務必要，但是選擇性的 [資料庫] 區段。
