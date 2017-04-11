<properties
    pageTitle="PHP 網頁和背景工作角色 |Microsoft Azure"
    description="Azure 雲端服務中，建立 PHP 網頁和背景工作角色和設定 PHP 執行階段指南。"
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>如何建立 PHP 網頁和背景工作角色

## <a name="overview"></a>概觀

本指南會顯示如何建立 PHP 網頁或背景工作角色 Windows 開發環境中，選擇特定版本的 PHP 從可用的 「 內建 」 版本、 變更 PHP 設定、 啟用擴充功能，及最後，部署至 Azure。 也將說明如何設定網站或工作使用 PHP 執行階段 （使用自訂的設定和延伸模組） 所提供的角色。

## <a name="what-are-php-web-and-worker-roles"></a>什麼是 PHP 網頁和背景工作角色？

Azure 提供三個計算執行應用程式模型︰ Azure 應用程式服務、 Azure 虛擬機器和 Azure 雲端服務。 這三種模型支援 PHP。 雲端服務，其中包含網頁和背景工作的角色，提供*的服務 (PaaS) 的平台*。 在雲端服務，網頁角色會提供專用的網際網路服務 (IIS) 網頁伺服器主機前端網頁應用程式。 工作者角色可以執行與使用者互動或輸入無關的非同步、 長期或永久工作。

如需這些選項的詳細資訊，請參閱[計算裝載 Azure 所提供的選項](./cloud-services/cloud-services-choose-me.md)。

## <a name="download-the-azure-sdk-for-php"></a>下載適用於 PHP Azure SDK

[Azure SDK PHP]包含數個元件。 本文會使用其中兩個︰ PowerShell 的 Azure 和 Azure 模擬器。 可以透過 Microsoft Web 平台安裝程式安裝這兩個元件。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md)。

## <a name="create-a-cloud-services-project"></a>建立雲端服務的專案

建立 PHP 網頁或背景工作角色的第一步是建立 Azure 服務專案。 Azure 服務專案做為邏輯容器的網頁和背景工作的角色，並包含專案的[服務定義 (.csdef)]及[服務設定 (.cscfg)]檔案。

若要建立新的 Azure 服務專案，執行 PowerShell 的 Azure 身為管理員，並執行下列命令︰

    PS C:\>New-AzureServiceProject myProject

這個命令將會建立新的目錄 (`myProject`)，您可以新增網頁和背景工作的角色。

## <a name="add-php-web-or-worker-roles"></a>新增 PHP 網頁或背景工作角色

若要在專案中新增 PHP 網頁角色，請執行下列命令從專案的根目錄中︰

    PS C:\myProject> Add-AzurePHPWebRole roleName

對於工作者角色，請使用這個命令︰

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] `roleName`參數是選擇性的。 如果省略，會自動產生的角色名稱。 將會建立的第一個網頁角色`WebRole1`，第二個會`WebRole2`，依此類推。 將會建立的第一個工作者角色`WorkerRole1`，第二個會`WorkerRole2`，依此類推。

## <a name="specify-the-built-in-php-version"></a>指定內建的 PHP 版本

當您在專案中新增 PHP 網頁或背景工作角色時，以便在部署時，會在每個網頁或背景工作的執行個體應用程式安裝 PHP 修改專案的設定檔。 若要查看 PHP 的預設會安裝的版本，請執行下列命令︰

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

上述命令的輸出會看起來像什麼如下所示。 在此範例中，`IsDefault`旗標設定為`true`的 PHP 5.3.17，表示它將會安裝的預設 PHP 版本。

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

您可以 PHP 版本所列的任何設定 PHP 執行階段版本。 例如，若要設定 PHP 版本 (名稱的角色`roleName`) 至 5.4.0，請使用下列命令︰

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] 可用的 PHP 版本可能會在未來變更。

## <a name="customize-the-built-in-php-runtime"></a>自訂內建的 PHP 執行階段

您可以完成控制當您遵循上述的步驟，包括修改已安裝的 PHP 執行階段設定`php.ini`設定和啟用的副檔名。

若要自訂內建的 PHP 執行階段，請遵循下列步驟︰

1. 加入新的資料夾，名為`php`、 至`bin`目錄您網站的角色。 工作者角色，請將其新增至角色的根目錄。
2. 在 [ `php` ] 資料夾中，建立另一個資料夾，稱為`ext`。 將任何`.dll`延伸檔案 (例如`php_mongo.dll`) 您想要啟用此資料夾中。
3. 新增`php.ini`檔案`php`資料夾。 啟用任何自訂擴充功能，並設定此檔案中的任何 PHP 指示詞。 例如，如果您想要開啟`display_errors`上並啟用`php_mongo.dll`副檔名，內容您`php.ini`檔案應，如下所示︰

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] 您不明確設定中的任何設定`php.ini`自動提供將檔案設為預設值。 不過，請記住，您可以新增完成`php.ini`檔案。

## <a name="use-your-own-php-runtime"></a>使用您自己的 PHP 執行階段
在某些情況下，而非選取的內建的 PHP 執行時間及設定，如上述，您可能要提供您自己的 PHP 執行階段。 例如，您可以使用相同的 PHP 執行階段在 web 或工作者角色您在您的開發環境中使用。 如此可讓您更輕鬆地確保應用程式不會變更生產環境中的行為。

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>設定以使用您自己的 PHP 執行階段 web 角色

若要設定使用 PHP 執行階段所提供的 web 角色，請遵循下列步驟︰

1. 建立 Azure 服務專案，並說明先前在本主題中，新增 PHP 網頁角色。
2. 建立`php`中的資料夾`bin`資料夾，在您的 web 角色的根目錄，然後將您 PHP 執行階段 （所有的二進位檔案、 設定檔、 子資料夾等） 新增至`php`資料夾。
3. （選用）如果您 PHP 執行階段使用[Microsoft SQL server PHP 驅動程式][sqlsrv drivers]，您必須設定您的 web 角色安裝[SQL Server Native 用戶端 2012年][sql native client]時加以佈建。 若要這麼做，新增至[sqlncli.msi x64 安裝程式]`bin`網頁角色的根目錄中的資料夾。 下一個步驟所述的啟動指令碼時角色也會佈建後，會自動執行安裝程式。 如果您 PHP 執行階段不使用 Microsoft 驅動程式 PHP 用於 SQL Server，您可以從下一個步驟所示的指令碼來移除的下列行︰

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. 定義啟動任務，以設定[網際網路服務 (IIS)] [iis.net]處理的要求使用 PHP 執行`.php`頁面。 若要這麼做，請開啟`setup_web.cmd`檔案 (在`bin`網頁角色的根目錄的檔案) 文字編輯器中，並以下列指令碼取代其內容︰

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. 您的應用程式將檔案新增至網頁角色的根目錄。 這是網頁伺服器的根目錄。

6. 發佈您的應用程式[發佈您的應用程式](#how-to-publish-your-application)] 區段下方所述。

> [AZURE.NOTE] `download.ps1`指令碼 (在`bin`網頁角色的根目錄的資料夾) 您遵循上述使用您自己的 PHP 執行階段的步驟之後，可以刪除。

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>設定以使用您自己的 PHP 執行階段工作者角色

若要設定使用 PHP 執行階段所提供的工作者角色，請遵循下列步驟︰

1. 建立 Azure 服務專案，並說明先前在本主題中，新增 PHP 工作者角色。
2. 建立`php`工作者角色的根目錄中的資料夾，然後新增您 PHP 執行階段 （所有的二進位檔案、 設定檔、 子資料夾等） 到`php`資料夾。
3. （選用）如果您 PHP 執行階段會使用[Microsoft SQL server PHP 驅動程式][sqlsrv drivers]，您必須設定您的工作者角色安裝[SQL Server Native 用戶端 2012年][sql native client]時加以佈建。 若要這麼做，請至工作者角色的根目錄新增[sqlncli.msi x64 安裝程式]。 下一個步驟所述的啟動指令碼時角色也會佈建後，會自動執行安裝程式。 如果您 PHP 執行階段不使用 Microsoft 驅動程式 PHP 用於 SQL Server，您可以從下一個步驟所示的指令碼來移除的下列行︰

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. 定義啟動任務，新增您`php.exe`至工作者角色的 PATH 環境變數當角色也會佈建後，您可執行。 若要這麼做，請開啟`setup_worker.cmd`在文字編輯器 （中工作者角色的根目錄） 檔案，並以下列指令碼取代其內容︰

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. 您的應用程式將檔案新增至您的工作者角色的根目錄。

6. 發佈您的應用程式[發佈您的應用程式](#how-to-publish-your-application)] 區段下方所述。

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>在計算和儲存模擬器中執行應用程式

Azure 模擬器提供本機環境中您可以測試 Azure 應用程式之前將其部署至雲端。 有一些模擬器和 Azure 環境之間的差異。 若要進一步瞭解此，請參閱[Azure 儲存模擬器，開發和測試](./storage/storage-use-emulator.md)。

請注意，您必須安裝至本機可使用計算模擬器 PHP。 計算模擬器會使用您的本機 PHP 安裝執行應用程式。

若要在模擬器中執行您的專案，請從您的專案根目錄中執行下列命令︰

    PS C:\MyProject> Start-AzureEmulator

您會看到類似︰

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

您可以看到您的應用程式在模擬器中執行，開啟網頁瀏覽器，並瀏覽至本機輸出中顯示的地址 (`http://127.0.0.1:81`上述範例輸出)。

若要停止模擬器，執行此命令︰

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>發佈您的應用程式

若要發佈您的應用程式，您必須先匯入您使用[匯入 AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx)指令程式來發佈設定。 然後，您可以使用[發佈 AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx)指令程式來發佈您的應用程式。 登入方面的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。

[Azure SDK PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[服務定義 (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[服務設定 (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 安裝程式]: http://go.microsoft.com/fwlink/?LinkID=239648
