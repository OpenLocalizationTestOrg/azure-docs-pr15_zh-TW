<properties
   pageTitle="在雲端服務角色安裝.NET |Microsoft Azure"
   description="本文將說明如何手動安裝.NET framework 雲端服務 Web 及工作者角色"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>安裝.NET 雲端服務角色 

本文將說明如何安裝.NET framework 雲端服務 Web 和工作者角色。 您可以使用這些步驟來安裝.NET 4.6.1 Azure 來賓 OS 系列 4。 來賓作業系統版本的最新資訊，請參閱[Azure 來賓 OS 發行和 SDK 相容性矩陣圖](cloud-services-guestos-update-matrix.md)。

在您的網頁和背景工作角色安裝.NET 的程序牽涉到包括.NET 安裝程式套件屬於雲端專案和啟動安裝程式的角色啟動工作的一部分。  

## <a name="add-the-net-installer-to-your-project"></a>.NET 安裝程式新增至專案
- 下載您想要安裝的.NET framework 的 web 安裝程式
    - [.NET 4.6.1 web 的安裝程式](http://go.microsoft.com/fwlink/?LinkId=671729)
- Web 角色
  1. 在**方案總管]**中，在雲端服務專案中的**角色**中以滑鼠右鍵按一下您的角色和選取**新增 > 新資料夾**。 建立名為 「*回收筒*的資料夾
  2. 以滑鼠右鍵按一下 [**回收筒**] 資料夾，然後選取 [**新增 > 現有項目**。 選取.NET 安裝程式，並將其新增至 [回收筒] 資料夾。
- 工作者角色
  1. 以滑鼠右鍵按一下您的角色和選取**新增 > 現有項目**。 選取.NET 安裝程式，並將其新增至角色。 

檔案新增到 [角色] 內容資料夾這種方式會自動新增至雲端服務套件並部署至虛擬機器上一致的位置。 讓所有的角色有一份安裝程式，請在您的雲端服務中的所有網頁和背景工作角色都重複此程序。

> [AZURE.NOTE] 即使您的應用程式的目標.NET 4.6，您應該安裝在您的雲端服務角色.NET 4.6.1。 Azure 來賓 OS 包含更新[3098779](https://support.microsoft.com/kb/3098779)和[3097997](https://support.microsoft.com/kb/3097997)。 安裝更新的上方的.NET 4.6 執行.NET 應用程式，讓您直接應該安裝，而不是.NET 4.6.NET 4.6.1 時可能會導致問題。 如需詳細資訊，請參閱[KB 3118750](https://support.microsoft.com/kb/3118750)。

![安裝程式檔案與角色內容][1]

## <a name="define-startup-tasks-for-your-roles"></a>定義您的角色啟動工作
啟動工作可讓您之前角色開始執行作業。 啟動工作的一部分安裝.NET Framework 可確保架構已安裝的應用程式碼的任何執行之前。 如需詳細資訊，在啟動時看到工作︰[執行 Azure 中的啟動工作](cloud-services-startup-tasks.md)。 

1. 新增所有角色**WebRole**或**WorkerRole**節點下*ServiceDefinition.csdef*檔案下列動作︰
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    上述設定會執行主控台命令*install.cmd*系統管理員權限，讓它可以安裝.NET framework。 設定也會建立名稱*NETFXInstall*LocalStorage。 要使用此本機存放區的資源，因此會下載並安裝此資源從.NET framework 安裝程式 temp 資料夾時，會設定啟動指令碼。 請務必確保架構將會安裝正確至少 1024 mb 設定此資源的大小。 如需啟動工作請參閱︰[常見的雲端服務啟動工作](cloud-services-startup-tasks-common.md) 

2. 建立檔案**install.cmd**並將其新增至所有角色中，以滑鼠右鍵按一下該角色所選取**新增 > 現有項目**。 讓所有的角色現在應該.NET 安裝程式檔案，以及 install.cmd 檔案。
    
    ![使用所有檔案的角色內容][2]

    > [AZURE.NOTE] 使用簡易文字編輯器 「 記事本 」 等來建立該檔案。 如果您使用 Visual Studio 建立文字檔案，然後再將它重新命名以 「.cmd' 檔案可能包含 utf-8 位元組順序標記，並執行指令碼的第一行會導致錯誤。 如果您要使用 Visual Studio 建立檔案離開新增 REM （說明） 檔案的第一行，讓執行時，它會忽略。 

3. 將下列指令碼加入**install.cmd**檔案︰

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    安裝程式碼會檢查是否指定的.NET framework 版本已經安裝在電腦上查詢登錄機碼。 如果未安裝.NET 版本，然後.Net Web 的安裝程式會啟動。 若要協助指令碼會命名*startuptasklog-((currentdatetime)).txt* *InstallLogs*本機存放區中儲存的檔案來記錄所有活動的任何問題進行疑難排解。

    > [AZURE.NOTE] 指令碼仍會顯示您如何安裝.NET 4.5.2 或.NET 4.6 持續。 有不需要手動安裝.NET 4.5.2，因為您已在 Azure 來賓 OS 上使用。 而不是安裝.NET 4.6 直接請安裝[KB 3118750](https://support.microsoft.com/kb/3118750)因為.NET 4.6.1。
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>設定診斷以傳送至 blob 儲存體啟動工作記錄檔 
若要簡化任何安裝問題的疑難排解，您可以設定 Azure 診斷以轉接啟動指令碼或 blob 儲存體.NET 安裝程式所產生的任何記錄檔。 使用這個方法您可以檢視記錄檔，只要下載 blob 儲存體記錄檔，不必將該角色的遠端桌面。

若要設定診斷開啟*diagnostics.wadcfgx*並新增**目錄**節點下的下列動作︰ 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

這將會設定 azure 診斷程式診斷儲存帳戶*netfx 安裝*blob 容器中傳送*NETFXInstall*資源之下*記錄*目錄中的所有檔案。

## <a name="deploying-your-service"></a>部署您的服務 
在部署您的服務時將執行啟動工作，並將其安裝.NET framework，如果沒有安裝。 架構安裝可能即使如果並重新啟動架構安裝需要它時，您的角色會忙碌狀態。 

## <a name="additional-resources"></a>其他資源

- [安裝.NET Framework][]
- [如何︰ 判斷哪些.NET Framework 版本已安裝][]
- [疑難排解.NET Framework 安裝][]

[如何︰ 判斷哪些.NET Framework 版本已安裝]: https://msdn.microsoft.com/library/hh925568.aspx
[安裝.NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[疑難排解.NET Framework 安裝]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 
