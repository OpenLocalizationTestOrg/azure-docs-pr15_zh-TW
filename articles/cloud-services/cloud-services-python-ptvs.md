<properties
    pageTitle="使用 Visual Studio Python web 及工作者角色 |Microsoft Azure"
    description="使用 Visual Studio Python 工具來建立 Azure 雲端服務，包括網頁角色和工作者角色的概觀。"
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/03/2016"
    ms.author="adegeo"/>


# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>使用 Visual studio Python 工具 Python web 及工作者角色

本文提供使用使用[Visual Studio Python 工具][]Python 網頁和背景工作角色的概觀。 您將學習如何使用 Visual Studio 建立並部署使用 Python 基本雲端服務。

## <a name="prerequisites"></a>必要條件

 - Visual Studio 2013 或 2015
 - [Visual Studio Python 工具][](PTVS)
 - [與 2013 azure SDK 工具][]] 或 [[與 2015 Azure SDK 工具][]
 - [Python 2.7 32 位元][]或[Python 3.5 32 位元][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>什麼是 Python web 及工作者角色？

Azure 提供三個計算執行應用程式模型︰ [Azure 應用程式服務中的 Web 應用程式功能][execution model-web sites]， [Azure 虛擬機器][execution model-vms]，和[Azure Cloud Services][execution model-cloud services]。 這三種模型支援 Python。 雲端服務，其中包含網頁和背景工作的角色，提供*的服務 (PaaS) 的平台*。 在雲端服務，網頁角色提供專用的網際網路服務 (IIS) 網頁伺服器主機前端網頁應用程式]，工作者角色可以執行與使用者互動或輸入無關的非同步、 長期或永久工作。

如需詳細資訊，請參閱[什麼是雲端服務？]。

> [AZURE.NOTE]*建立簡單的網站經過？*
如果您的狀況包含只要簡單網站前端，請考慮使用 Azure 應用程式服務中的精簡的 Web 應用程式功能。 當您的網站成長及變更您的需求，您輕鬆地可以升級至雲端服務。 請參閱<a href="/develop/python/">Python 開發人員中心</a>封面開發的 Azure 應用程式服務的 Web 應用程式功能的文章。
<br />


## <a name="project-creation"></a>建立專案

在 Visual Studio 中，您可以選取**Python**下的 [**新專案**] 對話方塊中的**Azure 雲端服務**。

![新增專案] 對話方塊](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Azure 雲端服務在精靈中，您可以建立新 web 及背景工作的角色。

![Azure 雲端服務] 對話方塊](./media/cloud-services-python-ptvs/new-service-wizard.png)

背景工作角色範本隨附連線至 Azure 儲存體帳戶或 Azure 服務匯流排程式碼。

![雲端服務方案](./media/cloud-services-python-ptvs/worker.png)

您可以新增至現有的雲端服務的網頁或背景工作的角色，隨時。  您可以選擇在您的方案中，將現有的專案，或建立新的文件。

![新增角色] 命令](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

您的雲端服務可以包含實作以不同語言的角色。  例如，您可以讓實作 Python，或 C# 工作者角色使用 Django，Python web 角色。  您可以輕鬆地溝通之間使用服務匯流排佇列或儲存空間佇列您角色。

## <a name="install-python-on-the-cloud-service"></a>安裝 Python 雲端服務

>[AZURE.WARNING] 設定指令碼 （在此文件上次更新的時間） 與 Visual Studio 一起安裝的無法使用。 本節說明因應措施。

設定指令碼的主要問題是他們沒有安裝 python。 首先，定義[ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)檔案中的兩個[啟動工作](cloud-services-startup-tasks.md)。 第一個工作 (**PrepPython.ps1**)，下載並安裝 Python 執行階段。 第二個任務 (**PipInstaller.ps1**) 執行 pip 安裝您可能會有任何相依性。

針對選取目標 Python 3.5 撰寫下列指令碼。 如果您想要使用的版本的 python 2.x 設定**PYTHON2**變數檔案為**上**兩個啟動工作及執行階段工作︰ `<Variable name="PYTHON2" value="<mark>on</mark>" />`。


```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
    
  </Task>

</Startup>
```

在**PYTHON2**和**PYPATH**變數需要新增至工作者啟動工作。 如果**PYTHON2**變數設為**上**，只會用**PYPATH**變數。

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>範例 ServiceDefinition.csdef

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



接下來，建立中的**PrepPython.ps1**和**PipInstaller.ps1**檔案**。 / 區間**您的角色] 資料夾。

#### <a name="preppythonps1"></a>PrepPython.ps1

這個指令碼會安裝 python。 如果**PYTHON2** enviornment 變數設定將會安裝**在**然後 Python 2.7，否則將會安裝 Python 3.5。

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }
        
        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1

這個指令碼設定 pip 通話，並安裝所有的相依性**requirements.txt**檔案中。 如果**PYTHON2** enviornment 變數設定將會用於**在**然後 Python 2.7，否則會使用 Python 3.5。

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>修改 LaunchWorker.ps1

>[AZURE.NOTE] 若是**工作者角色**專案， **LauncherWorker.ps1**檔案，才能執行啟動檔案。 在**網頁角色**專案中，啟動檔案被定義專案屬性中。

**Bin\LaunchWorker.ps1**原本建立執行許多準備工作，但不是真正運作。 使用下列指令碼取代該檔案的內容。

這個指令碼通話**worker.py**檔案從 python 專案。 如果**PYTHON2** enviornment 變數設定將會用於**在**然後 Python 2.7，否則會使用 Python 3.5。

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd

Visual Studio 範本應該已建立了**ps.cmd**檔案中的**。 / 區間**資料夾。 這個命令介面指令碼呼叫上述的 PowerShell 包裝紙指令碼，並提供記錄根據稱為 PowerShell 包裝紙的名稱。 如果不建立此檔案，以下是需要什麼中。 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>在本機上執行

如果您設定為啟動專案的雲端服務專案，請按 F5，雲端服務中執行本機 Azure 模擬器。

雖然 PTVS 支援在模擬器中啟動、 偵錯時 （例如，中斷點） 無法運作。

偵錯您網頁和背景工作的角色，您可以設定為啟動專案的角色專案，並改為偵錯的。  您也可以設定多個啟動專案。  以滑鼠右鍵按一下方案，然後選取 [**設定啟動專案**。

![解決辦法啟動專案屬性](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>發佈至 Azure

若要發佈雲端服務方案中的專案，以滑鼠右鍵按一下，然後選取 [**發佈]**。

![Microsoft Azure 發佈登入](./media/cloud-services-python-ptvs/publish-sign-in.png)

遵循精靈。 如果您需要請啟用遠端桌面。 當您需要進行偵錯時，會很有幫助遠端桌面。

當您完成設定的設定時，請按一下 [**發佈**]。

某些進度會出現在 [輸出] 視窗中，然後您會看到 Microsoft Azure 活動記錄視窗。

![Microsoft Azure 活動記錄視窗](./media/cloud-services-python-ptvs/publish-activity-log.png)

部署帶幾分鐘才能完成，然後將 Azure 上執行您的網站及/或工作者角色 ！

### <a name="investigate-logs"></a>調查記錄

雲端服務的虛擬機器啟動，並安裝 Python 之後，您可以查看的記錄，若要尋找的任何錯誤訊息。 這些記錄位於**C:\Resources\Directory\\{角色} \LogFiles**資料夾。 **PrepPython.err.txt**必須至少有一個錯誤中將它從時指令碼嘗試偵測如果已安裝 Python 和**PipInstaller.err.txt**可能抱怨 pip 的舊版本。

## <a name="next-steps"></a>後續步驟

如需使用 Visual studio 中 Python 工具的網頁和背景工作角色的詳細資訊，請參閱 PTVS 文件︰

- [雲端服務的專案][]

如需瞭解如何使用您的網頁和背景工作角色，例如使用 Azure 儲存體] 或 [服務匯流排從 Azure 服務的詳細資訊，請參閱下列文章。

- [Blob 服務][]
- [表格服務][]
- [佇列中服務][]
- [服務匯流排佇列][]
- [服務匯流排主題][]


<!--Link references-->

[什麼是雲端服務？]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob 服務]: ../storage/storage-python-how-to-use-blob-storage.md
[佇列中服務]: ../storage/storage-python-how-to-use-queue-storage.md
[表格服務]: ../storage/storage-python-how-to-use-table-storage.md
[服務匯流排佇列]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[服務匯流排主題]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Visual Studio Python 工具]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[雲端服務的專案]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure SDK 工具與 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK 工具與 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 位元]: https://www.python.org/downloads/
[Python 3.5 32 位元]: https://www.python.org/downloads/
