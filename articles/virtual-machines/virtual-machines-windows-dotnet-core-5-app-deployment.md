<properties
   pageTitle="自動化虛擬機器副檔名為應用程式部署 |Microsoft Azure"
   description="Azure 虛擬機器 DotNet 核心教學課程"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本的應用程式部署

一旦識別及部署範本轉換成數所有 Azure infrastructural 需求，實際的應用程式部署需要解決。 在此應用程式部署參考安裝至 Azure 資源實際的應用程式的二進位檔案。 音樂市集範例，.Net 核心和 IIS 需要安裝和設定每個虛擬機器上。 音樂市集二進位檔案必須安裝至虛擬機器，和預先建立的音樂儲存庫資料庫。

這份文件詳細說明如何虛擬機器副檔名可以自動化應用程式部署和 Azure 虛擬機器的設定。 所有的相依性和唯一的設定會醒目提示。 為了獲得最佳體驗，預先部署至 Azure 訂閱及工作，以及 Azure 資源管理員範本解決方案的執行個體。 完成範本可找到以下 –[在 Windows 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows)。

## <a name="configuration-script"></a>設定指令碼

虛擬機器擴充功能是針對提供設定自動化的虛擬機器執行的特定的程式。 多個特定用途，例如防毒軟體、 記錄設定和 Docker 設定的可延伸。 自訂指令碼副檔名為可用於執行虛擬機器中的任何指令碼。 音樂市集範例中，是由設定 Windows 虛擬機器並安裝的音樂市集應用程式的自訂指令碼副檔名。

之前詳細說明如何將虛擬機器延伸宣告 Azure 資源管理員範本中，檢查執行的指令碼。 這個指令碼設定 Windows 虛擬機器裝載的音樂市集應用程式。 當執行時，指令碼會安裝所有需要的軟體，安裝音樂 store 應用程式從來源控制項，並準備資料庫。 

> 此範例是供示範。

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>VM 指令碼副檔名

VM 副檔名可以針對來執行虛擬機器建立時間，包括中 Azure 資源管理員範本的副檔名資源。 使用 Visual Studio 新增資源] 精靈中，或有效 JSON 插入範本，您可以加入副檔名。 指令碼副檔名資源是巢狀嵌入的虛擬機器資源。這可以在下列範例中看到。

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 – [VM 指令碼副檔名](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339)。 

請注意，在 JSON，指令碼會儲存在 GitHub 下方。 這個指令碼也可能會儲存在 Azure Blob 儲存體。 此外，Azure 資源管理員範本可讓可建構，例如範本參數值可做為參數中的指令碼執行指令碼執行字串。 部署範本] 時，在此情況下提供資料，並執行指令碼時，可以再使用這些值。

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

如需有關如何使用自訂指令碼延伸模組的詳細資訊，請參閱[自訂指令碼副檔名與資源管理員範本](./virtual-machines-windows-extensions-customscript.md)。

## <a name="next-step"></a>下一步

<hr>

[瀏覽更多 Azure 資源管理員範本](https://github.com/Azure/azure-quickstart-templates)
