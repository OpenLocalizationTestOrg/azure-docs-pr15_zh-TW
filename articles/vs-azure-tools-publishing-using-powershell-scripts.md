<properties
   pageTitle="發佈至開發和測試環境中使用 Windows PowerShell 指令碼 |Microsoft Azure"
   description="瞭解如何使用 Windows PowerShell 指令碼，從 Visual Studio 開發發佈並測試環境。"
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

# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>若要發佈至開發和測試環境中使用 Windows PowerShell 指令碼

當您在 Visual Studio 建立 web 應用程式時，您可以產生的 Windows PowerShell 指令碼，您可以稍後使用自動化的發佈您的網站以 Azure 為 Azure 應用程式服務或虛擬機器中的 Web 應用程式。 您可以編輯和延伸以符合您的需求，Visual Studio 編輯器中的 Windows PowerShell 指令碼或指令碼整合現有的建立、 測試和發佈指令碼。

使用這些指令碼，您可以提供自訂暫時使用網站的版本 （又稱為開發和測試環境）。 例如，您可能會設定特定網站或執行測試套件、 重現問題、 測試修正程式，試用版提議的變更，或設定自訂的環境，示範或簡報的網站上的暫存位置 Azure 虛擬機器上的版本。 建立發佈專案的指令碼之後，您可以重新執行指令碼，視需要重新建立相同的環境，或使用您自己建立的 web 應用程式來建立自訂的環境，以進行測試執行指令碼。

## <a name="what-you-need"></a>您的需要

- Azure SDK 2.3 或更新版本。 如需詳細資訊，請參閱[Visual Studio 下載](http://go.microsoft.com/fwlink/?LinkID=624384)。

您不需要 Azure SDK 產生 web 專案中的指令碼。 此功能的 web 專案，不 web 雲端服務中的角色。

- Azure PowerShell 0.7.4 或更新版本。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md) 。

- [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175)或更新版本。

## <a name="additional-tools"></a>其他工具

其他工具和資源的使用 Visual Studio 中的 PowerShell 的 Azure 開發可供使用。 請參閱[Visual Studio PowerShell 工具](http://go.microsoft.com/fwlink/?LinkId=404012)。

## <a name="generating-the-publish-scripts"></a>產生發佈指令碼

您可以產生下列[這些指示](./virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md)建立新專案時裝載您網站的虛擬機器發佈指令碼。 您也可以[產生 Azure 應用程式服務中的 [發佈 web 應用程式的指令碼](./app-service-web/web-sites-dotnet-get-started.md)。

## <a name="scripts-that-visual-studio-generates"></a>Visual Studio 會產生的指令碼

Visual Studio 會產生一個稱為**PublishScripts**包含兩個 Windows PowerShell 檔案、 發佈指令碼虛擬機器或網站，並包含函數，您可以使用中的指令碼的模組的解決方案層級資料夾。 Visual Studio 也會產生 JSON 格式，指定您要部署的專案的詳細資料的檔案。

### <a name="windows-powershell-publish-script"></a>Windows PowerShell 發佈指令碼

發佈指令碼包含特定發佈部署至網站或虛擬機器的步驟。 Visual Studio 會提供語法標示的 Windows PowerShell 開發的色彩。 說明函數可用，但您可以自由編輯以符合您的變更要求的指令碼功能]。

### <a name="windows-powershell-module"></a>Windows PowerShell 模組

Windows PowerShell 模組的 Visual Studio 會產生包含發佈指令碼使用的函數。 這些是 PowerShell 的 Azure 函數，並不是要修改。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md) 。

### <a name="json-configuration-file"></a>JSON 設定檔

JSON 檔案會建立在 [**設定**] 資料夾，並包含指定完全要部署至此 Azure 的資源設定資料。 Visual Studio 會產生名稱是檔案的專案的名稱-WAWS-dev.json 如果您建立的網站或專案名稱 VM dev.json 如果您建立虛擬機器。 以下是當您建立網站時，會產生 JSON 設定檔的範例。 大部分的值是自我闡明。 網站名稱會產生 Azure，因此可能不符合您的專案名稱。

```
{
"environmentSettings": {
"webSite": {
"name": "WebApplication26632",
"location": "West US"
},
"databases": [
{
"connectionStringName": "DefaultConnection",
"databaseName": "WebApplication26632_db",
"serverName": "YourDatabaseServerName",
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
```
當您建立虛擬機器時，看起來類似下列 JSON 設定檔。 請注意，為容器的虛擬機器建立雲端服務。 虛擬機器包含透過 HTTP 和 HTTPS 的 web access 的主要結束點，以及網頁部署，可讓您從您的本機電腦、 遠端桌面]、 和 Windows PowerShell 發佈網站的端點。

```
{
"environmentSettings": {
"cloudService": {
"name": "myusernamevm1",
"affinityGroup": "",
"location": "West US",
"virtualNetwork": "",
"subnet": "",
"availabilitySet": "",
"virtualMachine": {
"name": "myusernamevm1",
"vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
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

您可以編輯 JSON 設定，若要變更當您執行的發佈指令碼時，會發生什麼情況。 `cloudService`和`virtualMachine`節必要，但您可以刪除`databases`區段，如果您不需要它。 在 Visual Studio 會產生的預設設定檔案是空白的屬性是選擇性;具有預設設定檔中的值所需。

如果您有多個部署環境 （亦稱為位置） 的網站，而不是 Azure 中的單一生產網站時，您可以包含在網站名稱的位置名稱 JSON 設定檔中。 例如，如果您有網站的已命名**我的網站**和為其命名為**測試**然後 URI 位置是我的網站 test.cloudapp.net，但若要使用的設定檔中正確的名稱會 mysite(test)。 您只能執行此 if 網站，並在您的訂閱已經存在的位置。 如果不存在，而不用指定位置上，執行指令碼來建立網站，然後[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)中建立的位置並之後執行指令碼修改過的網站名稱。 如需有關部署位置的 web 應用程式的詳細資訊，請參閱[設定臨時環境中 Azure 應用程式服務的 web 應用程式](./app-service-web/web-sites-staged-publishing.md)。

## <a name="how-to-run-the-publish-scripts"></a>如何執行發佈指令碼

如果您永遠不會執行 Windows PowerShell 指令碼之前，您必須先設定執行原則，才能執行指令碼。 這是安全性功能，以防止使用者很容易惡意程式碼或病毒涉及執行指令碼時，請執行 Windows PowerShell 指令碼。

### <a name="run-the-script"></a>執行指令碼

1. 建立專案的 Web 部署套件。 Web 部署套件已包含您要複製到您的網站或虛擬機器中的檔案壓縮的封存 （.zip 檔案）。 任何 web 應用程式，您可以在 Visual Studio 建立 Web 部署套件。

![建立部署套件](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

如需詳細資訊，請參閱[如何︰ 在 Visual Studio 建立 Web 部署套件](https://msdn.microsoft.com/library/dd465323.aspx)。 本主題稍後的區段**自訂及擴充發佈指令碼**所述，也可以將 Web 部署套件的建立。

1. 在**方案總管]**中，開啟指令碼，操作功能表，然後選擇**使用 PowerShell ise [以系統開啟**。

1. 如果這是您已在這台電腦執行 Windows PowerShell 指令碼的第一次，以系統管理員權限開啟命令提示字元視窗，並輸入以下命令︰

`Set-ExecutionPolicy RemoteSigned`

1. 使用下列命令登入 Azure。

`Add-AzureAccount`

出現提示時，請提供您的使用者名稱和密碼。

請注意，當您自動執行指令碼，無法使用這個方法提供 Azure 認證。 不過，您應該使用.publishsettings 檔案提供的認證。 一次，您使用命令**取得 AzurePublishSettingsFile**下載檔案從 Azure，但之後**匯入 AzurePublishSettingsFile**匯入的檔案。 如需詳細指示，請參閱[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md)。

1. （選用）如果您想要建立 Azure 的資源，例如虛擬機器、 資料庫及網站，而不發佈 web 應用程式，使用 [**發佈 WebApplication.ps1**命令與**-設定**引數設定為 JSON 設定檔。 這個命令列使用 JSON 設定檔，來判斷哪些資源以建立。 因為它的其他命令列引數使用的預設設定，建立資源，但無法發佈 web 應用程式。 – 的詳細資訊] 選項可讓您深入瞭解有什麼新鮮事。

`Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. 使用 [**發佈 WebApplication.ps1**命令在其中一個下列範例所示，叫用的指令碼及發佈 web 應用程式。 如果您要覆寫預設設定的任何其他引數，例如訂閱名稱、 發佈套件名稱、 虛擬機器認證或資料庫伺服器認證，您可以指定參數。 使用**– 詳細資訊**選項以查看進度發佈程序的詳細資訊。

```
Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
–SubscriptionName Contoso `
-WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
-DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
-Verbose
```

如果您要建立虛擬機器，命令的外觀如下所示。 此範例中也會顯示如何指定多個資料庫的認證。 這些指令碼建立虛擬機器，SSL 憑證不是從信任的根授權單位。 因此，您需要使用**– AllowUntrusted**選項。

```
Publish-WebApplication.ps1 `
-Configuration C:\Path\ADVM-VM-test.json `
-SubscriptionName Contoso `
-WebDeployPackage C:\Path\ADVM.zip `
-AllowUntrusted `
-VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
-DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
-Verbose
```

指令碼可以建立資料庫，但就不會建立資料庫伺服器。 如果您想要建立資料庫伺服器，您可以使用 [**新增 AzureSqlDatabaseServer**函數 Azure 模組中。

## <a name="customizing-and-extending-the-publish-scripts"></a>自訂及擴充發佈指令碼

您可以自訂的發佈指令碼及 JSON 設定檔。 在 Windows PowerShell 模組**AzureWebAppPublishModule.psm1**函數不打算修改。 如果您只是要指定不同的資料庫，或變更的部分虛擬機器的摘要，請編輯 [JSON 設定檔]。 如果您想要擴充功能的指令碼來自動化建置及測試您的專案，您可以**發佈 WebApplication.ps1**實作函數 stub。

若要自動建立您的專案，將會撥打到 MSBuild 的程式碼新增`New-WebDeployPackage`這個程式碼範例所示。 MSBuild] 命令的路徑是您已安裝的 Visual Studio 的版本而有所不同。 若要取得正確的路徑，您可以使用**取得 MSBuildCmd**，此函數，在此範例中所示。

### <a name="to-automate-building-your-project"></a>自動建立專案

1. 新增`$ProjectFile`全域參數] 區段中的參數。

```
[Parameter(Mandatory = $false)]
  [ValidateScript({Test-Path $_ -PathType Leaf})]
  [String]
  $ProjectFile,
```

1. 複製函數`Get-MSBuildCmd`到指令碼檔案。

```
function Get-MSBuildCmd
{
        process
{

             $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                   Sort-Object {[double]$_.PSChildName} -Descending |
                                   Select-Object -First 1 |
                                   Get-ItemProperty -Name MSBuildToolsPath |
                                   Select -ExpandProperty MSBuildToolsPath
       
            $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

        return Get-Item $path
    }
}
```

1. 取代`New-WebDeployPackage`與下列程式碼，取代預留位置線條建構`$msbuildCmd`。 將此程式碼是 Visual Studio 2015。 如果您使用 Visual Studio 2013，變更**VisualStudioVersion**屬性下方以`12.0`。

```
function New-WebDeployPackage
{
    #Write a function to build and package your web application
      
#To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
      
Write-VerboseWithTime 'Build-WebDeployPackage: Start'
      
$msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
      
Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
      
#Start execution of the build command
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
      
if ($job.ExitCode -ne 0)
{
throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName
      
#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
      
      
#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
      
Write-VerboseWithTime 'Build-WebDeployPackage: End'
      
return $WebDeployPackage
}
```

1. 通話`New-WebDeployPackage`此行之前的函數︰ `$Config = Read-ConfigFile $Configuration` web 應用程式或`$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)`虛擬機器。

```
if($ProjectFile)
{
$WebDeployPackage = New-WebDeployPackage
}
```

1. 呼叫 [自訂指令碼，從命令列使用傳遞`$Project`引數，如下列範例命令列所示。

```
.\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
-ProjectFile ..\WebApplication5\WebApplication5.csproj `
-VMPassword @{Name="VMUser";Password="Test.123"} `
-AllowUntrusted `
-Verbose
```

若要自動化測試您的應用程式，加入 [程式碼， `Test-WebApplication`。 請務必取消註解中**發佈 WebApplication.ps1**稱為這些函數的線條。 如果您不提供實作，您可以手動建立 Visual Studio 中，在專案，然後再執行 [發佈至 Azure 的發佈指令碼。

## <a name="publishing-function-summary"></a>發佈函數摘要

您可以使用 Windows PowerShell 命令提示字元的函數的相關說明，請使用命令`Get-Help function-name`。 說明包含參數說明及範例。 相同的說明文字也會在指令碼來源檔案， **AzureWebAppPublishModule.psm1**及**發佈 WebApplication.ps1**。 指令碼與說明的本地化您的 Visual Studio 語言。

**AzureWebAppPublishModule**

|函數名稱|描述|
|---|---|
|新增 AzureSQLDatabase|建立新的 Azure SQL 資料庫。|
|新增 AzureSQLDatabases|Visual Studio 會產生 JSON 控制器檔案中的值建立 Azure SQL 資料庫。|
|新增 AzureVM|建立 Azure 虛擬機器並傳回部署 VM 的 URL。 函數為必要元件，然後呼叫該**新增 AzureVM**函數 （Azure 模組） 若要建立新的虛擬機器。|
|新增 AzureVMEndpoints|加入虛擬機器中的新輸入的結束點，並傳回新的結束點的虛擬機器。|
|新增 AzureVMStorage|在目前的訂閱中建立新的 Azure 儲存體帳戶。 帳戶名稱的開頭 「 devtest 」 後面的唯一英數字元的字串。 此函數會傳回新儲存體帳戶的名稱。 您必須指定 [位置] 或 [新的儲存空間帳戶相關性群組。|
|新增 AzureWebsite|建立網站，以指定的名稱和位置。 此函數通話 Azure 模組**新增 AzureWebsite**函式。 如果訂閱已不包含指定名稱的網站，此函數會建立網站，並傳回網站物件。 否則，它會傳回`$null`。|
|備份訂閱|儲存目前的 Azure 訂閱中`$Script:originalSubscription`指令碼範圍中的變數。此函數會將儲存目前的 Azure 訂閱 (以取得`Get-AzureSubscription -Current`) 和其儲存帳戶，而且會變更這個指令碼的訂閱 (儲存在變數`$UserSpecifiedSubscription`) 和指令碼範圍中的儲存空間帳戶。 儲存的值，您可以使用函數，例如`Restore-Subscription`、 目前狀態還原原始目前的訂閱，儲存的帳戶，如果已變更的目前狀態。|
|尋找 AzureVM|取得指定的 Azure 虛擬機器。|
|格式 DevTestMessageWithTime|預先規劃的日期和時間的訊息。 此函數的設計的錯誤和詳細資料流寫入的訊息。|
|取得 AzureSQLDatabaseConnectionString|組件連線到 Azure SQL 資料庫連線字串。|
|取得 AzureVMStorage|傳回指定的位置或相關性] 群組中的第一個儲存體帳戶名稱圖樣 」 devtest*」 名稱 （不區分大小寫）。如果 「 devtest*」 的位置或相關性] 群組中，不符合儲存帳戶，該函數會忽略該。 您必須指定位置或相關性群組。|
|取得 MSDeployCmd|傳回執行 MsDeploy.exe 工具命令。|
|新 AzureVMEnvironment|尋找或建立虛擬機器中訂閱符合 JSON 設定檔中的值。|
|發佈 WebPackage|使用 MsDeploy.exe 和 web 發佈套件。將資源部署網站的 zip 檔案。 此函數不會產生任何輸出。 如果 MSDeploy.exe 呼叫失敗，該函數會擲回例外狀況。 若要取得更詳細的輸出，使用**-詳細**選項。|
|發佈 WebPackageToVM|會驗證參數值，然後再呼叫**發佈 WebPackage**函數。|
|閱讀 ConfigFile|驗證 JSON 設定檔，並傳回雜湊資料表的所選的值。|
|還原訂閱|將目前的訂閱的重設為原始訂閱。|
|測試 AzureModule|傳回`$true`如果已安裝的 Azure 模組版本 0.7.4 或更新版本。 傳回`$false`如果沒有安裝模組或較舊的版本。 此函數不具有參數。|
|測試 AzureModuleVersion|傳回`$true`Azure 模組的版本是否 0.7.4 或更新版本。 傳回`$false`如果沒有安裝模組或較舊的版本。 此函數不具有參數。|
|測試 HttpsUrl|將輸入的 URL 轉換至 System.Uri 物件。 傳回`$True`如果 URL 是絕對，且其配置為 https。 傳回`$false`如果 URL 是相對、 其配置未 HTTPS，或輸入的字串無法轉換至 URL]。|
|測試成員|傳回`$true`如果屬性或方法是物件的成員。 否則，會傳回`$false`。|
|撰寫 ErrorWithTime|將錯誤訊息加上目前的時間。 此函數呼叫的**格式 DevTestMessageWithTime**函數，在前面加上之前錯誤資料流撰寫郵件的時間。|
|撰寫 HostWithTime|撰寫郵件主機程式 （**寫入主機**） 加上目前的時間。 主機程式寫入的效果不盡相同。 大部分的程式裝載的 Windows PowerShell 撰寫這些訊息數。|
|撰寫 VerboseWithTime|將詳細訊息加上目前的時間。 因為它會呼叫**寫入詳細資訊**，或指令碼執行的**詳細資訊**參數時，才**VerbosePreference**喜好設定為 [**繼續]**時，也會顯示訊息。|

**發佈 WebApplication**

|函數名稱|描述|
|---|---|
|新 AzureWebApplicationEnvironment|建立 Azure 的資源，例如網站或虛擬機器。|
|新 WebDeployPackage|此函數並未實作。 您可以在此函數來建立您的專案中新增命令。|
|發佈 AzureWebApplication|發佈 Azure 的 web 應用程式。|
|發佈 WebApplication|建立並部署 Web 應用程式與虛擬機器、 SQL 資料庫]，儲存帳戶 Visual Studio web 專案。|
|測試 WebApplication|此函數並未實作。 您可以在此函數來測試您的應用程式中新增命令。|

## <a name="next-steps"></a>後續步驟

深入了解閱讀[使用 Windows PowerShell 指令碼](https://technet.microsoft.com/library/bb978526.aspx)的 PowerShell 指令碼，請參閱其他 Azure PowerShell 指令碼，[指令碼中心](https://azure.microsoft.com/documentation/scripts/)。
